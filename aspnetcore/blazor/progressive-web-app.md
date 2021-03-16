---
title: ASP.NET Core Blazor WebAssembly를 사용하여 프로그레시브 웹 애플리케이션 빌드
author: guardrex
description: 최신 브라우저 기능을 사용하여 데스크톱 앱과 같은 동작을 하는 Blazor 기반 PWA(프로그레시브 웹 애플리케이션)를 빌드하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/11/2021
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
uid: blazor/progressive-web-app
ms.openlocfilehash: 9e7063297e124aabbdf1defd01ac90f735ef5321
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395008"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a><span data-ttu-id="90fab-103">ASP.NET Core Blazor WebAssembly를 사용하여 프로그레시브 웹 애플리케이션 빌드</span><span class="sxs-lookup"><span data-stu-id="90fab-103">Build Progressive Web Applications with ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="90fab-104">일반적으로 PWA(프로그레시브 웹 애플리케이션)는 최신 브라우저 API 및 기능을 사용하여 데스크톱 앱처럼 동작하는 SPA(단일 페이지 애플리케이션)입니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-104">A Progressive Web Application (PWA) is usually a Single Page Application (SPA) that uses modern browser APIs and capabilities to behave like a desktop app.</span></span> <span data-ttu-id="90fab-105">Blazor WebAssembly는 표준 기반 클라이언트 쪽 웹앱 플랫폼이므로 다음 기능에 필요한 PWA API를 비롯한 모든 브라우저 API를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-105">Blazor WebAssembly is a standards-based client-side web app platform, so it can use any browser API, including PWA APIs required for the following capabilities:</span></span>

* <span data-ttu-id="90fab-106">네트워크 속도와 상관없이 오프라인으로 작동하고 항상 즉시 로드</span><span class="sxs-lookup"><span data-stu-id="90fab-106">Working offline and loading instantly, independent of network speed.</span></span>
* <span data-ttu-id="90fab-107">브라우저 창뿐만 아니라 자체 앱 창에서 실행</span><span class="sxs-lookup"><span data-stu-id="90fab-107">Running in its own app window, not just a browser window.</span></span>
* <span data-ttu-id="90fab-108">호스트 운영 체제 시작 메뉴, 도킹 또는 홈 화면에서 시작</span><span class="sxs-lookup"><span data-stu-id="90fab-108">Being launched from the host's operating system start menu, dock, or home screen.</span></span>
* <span data-ttu-id="90fab-109">사용자가 앱을 사용하지 않을 때도 백엔드 서버에서 푸시 알림 받기</span><span class="sxs-lookup"><span data-stu-id="90fab-109">Receiving push notifications from a backend server, even while the user isn't using the app.</span></span>
* <span data-ttu-id="90fab-110">백그라운드에서 자동 업데이트</span><span class="sxs-lookup"><span data-stu-id="90fab-110">Automatically updating in the background.</span></span>

<span data-ttu-id="90fab-111">이러한 앱을 설명하는 데 ‘프로그레시브’가 사용되는 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-111">The word *progressive* is used to describe such apps because:</span></span>

* <span data-ttu-id="90fab-112">사용자는 먼저 다른 SPA와 마찬가지로 웹 브라우저 내에서 앱을 검색하고 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-112">A user might first discover and use the app within their web browser like any other SPA.</span></span>
* <span data-ttu-id="90fab-113">나중에 사용자가 OS에 설치하고 푸시 알림을 사용하도록 설정하는 과정을 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-113">Later, the user progresses to installing it in their OS and enabling push notifications.</span></span>

## <a name="create-a-project-from-the-pwa-template"></a><span data-ttu-id="90fab-114">PWA 템플릿에서 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="90fab-114">Create a project from the PWA template</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90fab-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90fab-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="90fab-116">**새 프로젝트 만들기** 대화 상자에서 새 **Blazor WebAssembly 앱** 을 만들 때 **프로그레시브 웹 애플리케이션** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-116">When creating a new **Blazor WebAssembly App** in the **Create a New Project** dialog, select the **Progressive Web Application** check box:</span></span>

![‘프로그레시브 웹 애플리케이션’ 확인란은 Visual Studio 새 프로젝트 대화 상자에서 선택합니다.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="90fab-118">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="90fab-118">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="90fab-119">다음 명령을 사용하여 명령 셸에서 `--pwa` 스위치를 사용하여 PWA 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-119">Use the following command to create a PWA project in a command shell with the `--pwa` switch:</span></span>

```dotnetcli
dotnet new blazorwasm -o MyBlazorPwa --pwa
```

<span data-ttu-id="90fab-120">이전 명령에서 `-o|--output` 옵션은 `MyBlazorPwa`라는 앱의 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-120">In the preceding command, the `-o|--output` option creates a new folder for the app named `MyBlazorPwa`.</span></span>

---

<span data-ttu-id="90fab-121">필요에 따라 ASP.NET Core에 호스트된 템플릿에서 만든 앱에 대해 PWA를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-121">Optionally, PWA can be configured for an app created from the ASP.NET Core Hosted template.</span></span> <span data-ttu-id="90fab-122">PWA 시나리오는 호스팅 모델과 관계없습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-122">The PWA scenario is independent of the hosting model.</span></span>

## <a name="convert-an-existing-blazor-webassembly-app-into-a-pwa"></a><span data-ttu-id="90fab-123">기존 Blazor WebAssembly 앱을 PWA로 변환</span><span class="sxs-lookup"><span data-stu-id="90fab-123">Convert an existing Blazor WebAssembly app into a PWA</span></span>

<span data-ttu-id="90fab-124">이 섹션의 지침에 따라 기존 Blazor WebAssembly 앱을 PWA로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-124">Convert an existing Blazor WebAssembly app into a PWA following the guidance in this section.</span></span>

<span data-ttu-id="90fab-125">앱의 프로젝트 파일에서:</span><span class="sxs-lookup"><span data-stu-id="90fab-125">In the app's project file:</span></span>

* <span data-ttu-id="90fab-126">다음 `ServiceWorkerAssetsManifest` 속성을 `PropertyGroup`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-126">Add the following `ServiceWorkerAssetsManifest` property to a `PropertyGroup`:</span></span>

  ```xml
    ...
    <ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
  </PropertyGroup>
   ```

* <span data-ttu-id="90fab-127">다음 `ServiceWorker` 항목을 `ItemGroup`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-127">Add the following `ServiceWorker` item to an `ItemGroup`:</span></span>

  ```xml
  <ItemGroup>
    <ServiceWorker Include="wwwroot\service-worker.js" 
      PublishedContent="wwwroot\service-worker.published.js" />
  </ItemGroup>
  ```

<span data-ttu-id="90fab-128">정적 자산을 얻으려면 다음 접근 방식 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-128">To obtain static assets, use **one** of the following approaches:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="90fab-129">명령 셸에서 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 사용하여 별도의 새 PWA 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-129">Create a separate, new PWA project with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa
  ```
  
  <span data-ttu-id="90fab-130">이전 명령에서 `-o|--output` 옵션은 `MyBlazorPwa`라는 앱의 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-130">In the preceding command, the `-o|--output` option creates a new folder for the app named `MyBlazorPwa`.</span></span>
  
  <span data-ttu-id="90fab-131">**최신 릴리스용 앱을 변환하지 않는 경우** `-f|--framework` 옵션을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-131">**If you aren't converting an app for the latest release**, pass the `-f|--framework` option.</span></span> <span data-ttu-id="90fab-132">다음 예제에서는 ASP.NET Core 버전 3.1용 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-132">The following example creates the app for ASP.NET Core version 3.1:</span></span>
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```

* <span data-ttu-id="90fab-133">`main` 분기 참조 원본 및 자산에 연결되는 다음 URL에서 ASP.NET Core GitHub 리포지토리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-133">Navigate to the ASP.NET Core GitHub repository at the following URL, which links to `main` branch reference source and assets.</span></span> <span data-ttu-id="90fab-134">앱에 적용되는 **Switch branches or tags**(분기 또는 태그 전환) 드롭다운 목록에서 작업 중인 릴리스를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-134">Select the release that you're working with from the **Switch branches or tags** drop-down list that applies to your app.</span></span>

  [<span data-ttu-id="90fab-135">Blazor WebAssembly 프로젝트 템플릿 `wwwroot` 폴더(dotnet/aspnetcore GitHub 리포지토리 `main` 분기)</span><span class="sxs-lookup"><span data-stu-id="90fab-135">Blazor WebAssembly project template `wwwroot` folder (dotnet/aspnetcore GitHub repository `main` branch)</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

  [!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="90fab-136">명령 셸에서 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 사용하여 별도의 새 PWA 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-136">Create a separate, new PWA project with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="90fab-137">`-f|--framework` 옵션을 전달하여 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-137">Pass the `-f|--framework` option to select the version.</span></span> <span data-ttu-id="90fab-138">다음 예제에서는 ASP.NET Core 버전 3.1용 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-138">The following example creates the app for ASP.NET Core version 3.1:</span></span>
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```
  
  <span data-ttu-id="90fab-139">이전 명령에서 `-o|--output` 옵션은 `MyBlazorPwa`라는 앱의 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-139">In the preceding command, the `-o|--output` option creates a new folder for the app named `MyBlazorPwa`.</span></span>

* <span data-ttu-id="90fab-140">3\.1 릴리스 참조 소스 및 자산에 연결되는 다음 URL에서 ASP.NET Core GitHub 리포지토리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-140">Navigate to the ASP.NET Core GitHub repository at the following URL, which links to 3.1 release reference source and assets:</span></span>

  [<span data-ttu-id="90fab-141">Blazor WebAssembly 프로젝트 템플릿 `wwwroot` 폴더(dotnet/aspnetcore GitHub 리포지토리 `release 3.1` 분기)</span><span class="sxs-lookup"><span data-stu-id="90fab-141">Blazor WebAssembly project template `wwwroot` folder (dotnet/aspnetcore GitHub repository `release 3.1` branch)</span></span>](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/ProjectTemplates/ComponentsWebAssembly.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

  > [!NOTE]
  > <span data-ttu-id="90fab-142">ASP.NET Core 3.1 릴리스 후에 변경된 Blazor WebAssembly 프로젝트 템플릿의 URL</span><span class="sxs-lookup"><span data-stu-id="90fab-142">The URL for Blazor WebAssembly project template changed after the release of ASP.NET Core 3.1.</span></span> <span data-ttu-id="90fab-143">릴리스에 대한 참조 자산은 ASP.NET Core 참조 원본에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-143">Reference assets for any release are available from the ASP.NET Core reference source.</span></span> <span data-ttu-id="90fab-144">앱에 적용되는 **Switch branches or tags**(분기 또는 태그 전환) 드롭다운 목록에서 작업 중인 릴리스를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-144">Select the release that you're working with from the **Switch branches or tags** drop-down list that applies to your app.</span></span>
  >
  > [<span data-ttu-id="90fab-145">Blazor WebAssembly 프로젝트 템플릿 `wwwroot` 폴더(dotnet/aspnetcore GitHub 리포지토리 `main` 분기)</span><span class="sxs-lookup"><span data-stu-id="90fab-145">Blazor WebAssembly project template `wwwroot` folder (dotnet/aspnetcore GitHub repository `main` branch)</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)
  >
  > [!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

::: moniker-end

<span data-ttu-id="90fab-146">직접 만든 앱 또는 `dotnet/aspnetcore` GitHub 리포지토리의 참조 자산에서 소스 `wwwroot` 폴더에서 다음 파일을 앱의 `wwwroot` 폴더에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-146">From the source `wwwroot` folder either in the app that you created or from the reference assets in the `dotnet/aspnetcore` GitHub repository, copy the following files into the app's `wwwroot` folder:</span></span>

* `icon-512.png`
* `manifest.json`
* `service-worker.js`
* `service-worker.published.js`

<span data-ttu-id="90fab-147">앱의 `wwwroot/index.html` 파일에서:</span><span class="sxs-lookup"><span data-stu-id="90fab-147">In the app's `wwwroot/index.html` file:</span></span>

* <span data-ttu-id="90fab-148">매니페스트 및 앱 아이콘의 `<link>` 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-148">Add `<link>` elements for the manifest and app icon:</span></span>

  ```html
  <link href="manifest.json" rel="manifest" />
  <link rel="apple-touch-icon" sizes="512x512" href="icon-512.png" />
  ```

* <span data-ttu-id="90fab-149">`blazor.webassembly.js` 스크립트 태그 바로 뒤에 있는 닫는 `</body>` 태그 안에 다음 `<script>` 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-149">Add the following `<script>` tag inside the closing `</body>` tag immediately after the `blazor.webassembly.js` script tag:</span></span>

  ```html
      ...
      <script>navigator.serviceWorker.register('service-worker.js');</script>
  </body>
  ```

## <a name="installation-and-app-manifest"></a><span data-ttu-id="90fab-150">설치 및 앱 매니페스트</span><span class="sxs-lookup"><span data-stu-id="90fab-150">Installation and app manifest</span></span>

<span data-ttu-id="90fab-151">PWA 템플릿을 사용하여 만든 앱을 방문하는 사용자에게는 해당 OS의 시작 메뉴, 도킹 또는 홈 화면에 앱을 설치할 수 있는 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-151">When visiting an app created using the PWA template, users have the option of installing the app into their OS's start menu, dock, or home screen.</span></span> <span data-ttu-id="90fab-152">이 옵션이 표시되는 방법은 사용자의 브라우저에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-152">The way this option is presented depends on the user's browser.</span></span> <span data-ttu-id="90fab-153">Edge 또는 Chrome과 같은 데스크톱 Chromium 기반 브라우저를 사용하는 경우 URL 표시줄에 **추가** 단추가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-153">When using desktop Chromium-based browsers, such as Edge or Chrome, an **Add** button appears within the URL bar.</span></span> <span data-ttu-id="90fab-154">사용자가 **추가** 단추를 선택하면 확인 대화 상자가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-154">After the user selects the **Add** button, they receive a confirmation dialog:</span></span>

![Google Chrome의 확인 대화 상자는 사용자에게 ‘MyBlazorPwa’ 앱의 설치 단추를 제공합니다.](progressive-web-app/_static/image2.png)

<span data-ttu-id="90fab-156">iOS에서 방문자는 Safari의 **‘공유’** 단추 및 **‘홈 화면에 추가’** 옵션을 사용하여 PWA를 설치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-156">On iOS, visitors can install the PWA using Safari's **Share** button and its **Add to Homescreen** option.</span></span> <span data-ttu-id="90fab-157">Android용 Chrome에서 사용자는 오른쪽 위에 있는 **메뉴** 단추를 탭한 다음 **홈 화면에 추가** 를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-157">On Chrome for Android, users should select the **Menu** button in the upper-right corner, followed by **Add to Home screen**.</span></span>

<span data-ttu-id="90fab-158">설치된 앱은 주소 표시줄 없이 자체 창에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-158">Once installed, the app appears in its own window without an address bar:</span></span>

![‘MyBlazorPwa’ 앱은 주소 표시줄 없이 Google Chrome에서 실행됩니다.](progressive-web-app/_static/image3.png)

<span data-ttu-id="90fab-160">창의 제목, 색 구성표, 아이콘 또는 기타 세부 정보를 사용자 지정하려면 프로젝트의 `wwwroot` 디렉터리에서 `manifest.json` 파일을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90fab-160">To customize the window's title, color scheme, icon, or other details, see the `manifest.json` file in the project's `wwwroot` directory.</span></span> <span data-ttu-id="90fab-161">이 파일의 스키마는 웹 표준에 의해 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-161">The schema of this file is defined by web standards.</span></span> <span data-ttu-id="90fab-162">자세한 내용은 [MDN 웹 문서: 웹앱 매니페스트](https://developer.mozilla.org/docs/Web/Manifest)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90fab-162">For more information, see [MDN web docs: Web App Manifest](https://developer.mozilla.org/docs/Web/Manifest).</span></span>

## <a name="offline-support"></a><span data-ttu-id="90fab-163">오프라인 지원</span><span class="sxs-lookup"><span data-stu-id="90fab-163">Offline support</span></span>

<span data-ttu-id="90fab-164">기본적으로 PWA 템플릿 옵션을 사용하여 만든 앱은 오프라인 실행을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-164">By default, apps created using the PWA template option have support for running offline.</span></span> <span data-ttu-id="90fab-165">사용자가 온라인 상태인 동안 먼저 앱을 방문해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-165">A user must first visit the app while they're online.</span></span> <span data-ttu-id="90fab-166">브라우저는 오프라인으로 작업하는 데 필요한 모든 리소스를 자동으로 다운로드하고 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-166">The browser automatically downloads and caches all of the resources required to operate offline.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="90fab-167">개발 지원은 변경을 수행하고 테스트하는 일반적인 개발 주기를 방해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-167">Development support would interfere with the usual development cycle of making changes and testing them.</span></span> <span data-ttu-id="90fab-168">따라서 오프라인 지원은 ‘게시된’ 앱에 대해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-168">Therefore, offline support is only enabled for *published* apps.</span></span> 

> [!WARNING]
> <span data-ttu-id="90fab-169">오프라인 사용 가능 PWA를 배포하려는 경우 [몇 가지 중요한 경고 및 주의 사항](#caveats-for-offline-pwas)을 이해해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-169">If you intend to distribute an offline-enabled PWA, there are [several important warnings and caveats](#caveats-for-offline-pwas).</span></span> <span data-ttu-id="90fab-170">이러한 시나리오는 오프라인 PWA에 해당하는 것으로 Blazor에 국한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-170">These scenarios are inherent to offline PWAs and not specific to Blazor.</span></span> <span data-ttu-id="90fab-171">오프라인 지원 앱이 작동하는 방식에 대해 가정하기 전에 해당 주의 사항을 읽고 이해해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-171">Be sure to read and understand these caveats before making assumptions about how your offline-enabled app will work.</span></span>

<span data-ttu-id="90fab-172">오프라인 지원의 작동 방식을 확인하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-172">To see how offline support works:</span></span>

1. <span data-ttu-id="90fab-173">앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-173">Publish the app.</span></span> <span data-ttu-id="90fab-174">자세한 내용은 <xref:blazor/host-and-deploy/index#publish-the-app>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90fab-174">For more information, see <xref:blazor/host-and-deploy/index#publish-the-app>.</span></span>
1. <span data-ttu-id="90fab-175">HTTPS를 지원하는 서버에 앱을 배포하고 보안 HTTPS 주소를 사용하여 브라우저에서 앱에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-175">Deploy the app to a server that supports HTTPS, and access the app in a browser at its secure HTTPS address.</span></span>
1. <span data-ttu-id="90fab-176">브라우저의 개발자 도구를 열고 **애플리케이션** 탭에서 호스트에 대한 ‘서비스 작업자’가 등록되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-176">Open the browser's dev tools and verify that a *Service Worker* is registered for the host on the **Application** tab:</span></span>

   ![Google Chrome 개발자 도구 ‘애플리케이션’ 탭에서는 활성화되고 실행 중인 서비스 작업자를 표시합니다.](progressive-web-app/_static/image4.png)

1. <span data-ttu-id="90fab-178">페이지를 다시 로드하고 **네트워크** 탭을 검토합니다. **서비스 작업자** 또는 **메모리 캐시** 는 모든 페이지 자산의 원본으로 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-178">Reload the page and examine the **Network** tab. **Service Worker** or **memory cache** are listed as the sources for all of the page's assets:</span></span>

   ![모든 페이지 자산의 원본을 나타내는 Google Chrome 개발자 도구 ‘네트워크’ 탭](progressive-web-app/_static/image5.png)

1. <span data-ttu-id="90fab-180">브라우저가 네트워크 액세스에 의존하지 않고 앱을 로드하는지 확인하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-180">To verify that the browser isn't dependent on network access to load the app, either:</span></span>

   * <span data-ttu-id="90fab-181">웹 서버를 종료하고 페이지 다시 로드를 포함해 앱이 계속 정상적으로 작동하지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-181">Shut down the web server and see how the app continues to function normally, which includes page reloads.</span></span> <span data-ttu-id="90fab-182">마찬가지로 네트워크 연결이 느릴 때 앱이 정상적으로 계속 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-182">Likewise, the app continues to function normally when there's a slow network connection.</span></span>
   * <span data-ttu-id="90fab-183">**네트워크** 탭에서 오프라인 모드를 시뮬레이션하도록 브라우저에 명령합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-183">Instruct the browser to simulate offline mode in the **Network** tab:</span></span>

   ![브라우저 모드 드롭다운이 ‘온라인’에서 ‘오프라인’으로 변경된 Google Chrome 개발자 도구 ‘네트워크’ 탭](progressive-web-app/_static/image6.png)

<span data-ttu-id="90fab-185">서비스 작업자를 사용하는 오프라인 지원은 Blazor에 국한되지 않는 웹 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-185">Offline support using a service worker is a web standard, not specific to Blazor.</span></span> <span data-ttu-id="90fab-186">서비스 작업자에 대한 자세한 내용은 [MDN 웹 문서: 서비스 작업자 API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90fab-186">For more information on service workers, see [MDN web docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).</span></span> <span data-ttu-id="90fab-187">서비스 작업자의 일반적인 사용 패턴에 대한 자세한 내용은 [Google Web: 서비스 작업자 수명 주기](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90fab-187">To learn more about common usage patterns for service workers, see [Google Web: The Service Worker Lifecycle](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).</span></span>

<span data-ttu-id="90fab-188">Blazor의 PWA 템플릿은 다음 두 개의 서비스 작업자 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-188">Blazor's PWA template produces two service worker files:</span></span>

* <span data-ttu-id="90fab-189">개발 중에 사용되는 `wwwroot/service-worker.js`</span><span class="sxs-lookup"><span data-stu-id="90fab-189">`wwwroot/service-worker.js`, which is used during development.</span></span>
* <span data-ttu-id="90fab-190">앱이 게시된 후에 사용되는 `wwwroot/service-worker.published.js`</span><span class="sxs-lookup"><span data-stu-id="90fab-190">`wwwroot/service-worker.published.js`, which is used after the app is published.</span></span>

<span data-ttu-id="90fab-191">두 서비스 작업자 파일 간의 논리를 공유하려면 다음 방법을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="90fab-191">To share logic between the two service worker files, consider the following approach:</span></span>

* <span data-ttu-id="90fab-192">공통 논리를 보관할 세 번째 JavaScript 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-192">Add a third JavaScript file to hold the common logic.</span></span>
* <span data-ttu-id="90fab-193">[`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts)를 사용하여 두 서비스 작업자 파일에 공통 논리를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-193">Use [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) to load the common logic into both service worker files.</span></span>

### <a name="cache-first-fetch-strategy"></a><span data-ttu-id="90fab-194">캐시 우선 인출 전략</span><span class="sxs-lookup"><span data-stu-id="90fab-194">Cache-first fetch strategy</span></span>

<span data-ttu-id="90fab-195">기본 제공 `service-worker.published.js` 서비스 작업자는 ‘캐시 우선’ 전략을 사용하여 요청을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-195">The built-in `service-worker.published.js` service worker resolves requests using a *cache-first* strategy.</span></span> <span data-ttu-id="90fab-196">즉, 사용자가 네트워크에 액세스할 수 있는지 또는 서버에서 최신 콘텐츠를 사용할 수 있는지와 관계없이 캐시된 콘텐츠를 반환하는 것이 항상 선호됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-196">This means that the service worker prefers to return cached content, regardless of whether the user has network access or newer content is available on the server.</span></span>

<span data-ttu-id="90fab-197">캐시 우선 전략은 다음 이유로 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-197">The cache-first strategy is valuable because:</span></span>

* <span data-ttu-id="90fab-198">**안정성을 보장합니다.**</span><span class="sxs-lookup"><span data-stu-id="90fab-198">**It ensures reliability.**</span></span> <span data-ttu-id="90fab-199">네트워크 액세스가 부울 상태가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-199">Network access isn't a boolean state.</span></span> <span data-ttu-id="90fab-200">사용자는 단순히 온라인 또는 오프라인 상태가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-200">A user isn't simply online or offline:</span></span>

  * <span data-ttu-id="90fab-201">사용자의 디바이스는 온라인 상태라고 생각할 수 있지만 네트워크가 너무 느려 대기하는 것이 유리하지 않을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-201">The user's device may assume it's online, but the network might be so slow as to be impractical to wait for.</span></span>
  * <span data-ttu-id="90fab-202">현재 특정 요청을 차단하거나 리디렉션하는 종속 WIFI 포털이 있는 경우와 같이 특정 URL에 대해 잘못된 결과가 네트워크에서 반환될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-202">The network might return invalid results for certain URLs, such as when there's a captive WIFI portal that's currently blocking or redirecting certain requests.</span></span>
  
  <span data-ttu-id="90fab-203">이로 인해 브라우저의 `navigator.onLine` API가 안정적이지 않으므로 의존해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-203">This is why the browser's `navigator.onLine` API isn't reliable and shouldn't be depended upon.</span></span>

* <span data-ttu-id="90fab-204">**정확성을 보장합니다.**</span><span class="sxs-lookup"><span data-stu-id="90fab-204">**It ensures correctness.**</span></span> <span data-ttu-id="90fab-205">오프라인 리소스의 캐시를 빌드할 때 서비스 작업자는 콘텐츠 해시를 사용하여 완전하고 자체 일관성 있는 리소스 스냅샷을 인출했음을 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-205">When building a cache of offline resources, the service worker uses content hashing to guarantee it has fetched a complete and self-consistent snapshot of resources at a single instant in time.</span></span> <span data-ttu-id="90fab-206">이 캐시는 원자성 단위로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-206">This cache is then used as an atomic unit.</span></span> <span data-ttu-id="90fab-207">사용자가 이미 캐시한 버전만 사용하게 되므로 네트워크에 최신 리소스를 요청할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-207">There's no point asking the network for newer resources, since the only versions required are the ones already cached.</span></span> <span data-ttu-id="90fab-208">그 밖의 위험 요소는 불일치 및 비호환성입니다(예: 함께 컴파일되지 않은 .NET 어셈블리 버전을 사용하려는 경우).</span><span class="sxs-lookup"><span data-stu-id="90fab-208">Anything else risks inconsistency and incompatibility (for example, trying to use versions of .NET assemblies that weren't compiled together).</span></span>

### <a name="background-updates"></a><span data-ttu-id="90fab-209">백그라운드 업데이트</span><span class="sxs-lookup"><span data-stu-id="90fab-209">Background updates</span></span>

<span data-ttu-id="90fab-210">멘탈 모델로서는, 오프라인 중심 PWA가 설치할 수 있는 모바일 앱처럼 동작한다고 생각할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-210">As a mental model, you can think of an offline-first PWA as behaving like a mobile app that can be installed.</span></span> <span data-ttu-id="90fab-211">앱은 네트워크 연결과 관계없이 항상 즉시 시작되지만, 설치된 앱 논리는 최신 버전이 아닐 수도 있는 지정 시간 스냅샷에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-211">The app starts up immediately regardless of network connectivity, but the installed app logic comes from a point-in-time snapshot that might not be the latest version.</span></span>

<span data-ttu-id="90fab-212">Blazor PWA 템플릿은 사용자가 방문하여 네트워크에 연결되어 있을 때마다 백그라운드에서 자동으로 업데이트를 시도하는 앱을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-212">The Blazor PWA template produces apps that automatically try to update themselves in the background whenever the user visits and has a working network connection.</span></span> <span data-ttu-id="90fab-213">이렇게 작동하는 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-213">The way this works is as follows:</span></span>

* <span data-ttu-id="90fab-214">컴파일하는 동안 프로젝트는 ‘서비스 작업자 자산 매니페스트’를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-214">During compilation, the project generates a *service worker assets manifest*.</span></span> <span data-ttu-id="90fab-215">기본적으로 `service-worker-assets.js`라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-215">By default, this is called `service-worker-assets.js`.</span></span> <span data-ttu-id="90fab-216">앱에서 콘텐츠 해시를 포함하여 .NET 어셈블리, JavaScript 파일, CSS 등이 오프라인에서 기능하는 데 필요한 모든 정적 리소스를 매니페스트에서 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-216">The manifest lists all the static resources that the app requires to function offline, such as .NET assemblies, JavaScript files, and CSS, including their content hashes.</span></span> <span data-ttu-id="90fab-217">이 리소스 목록은 캐시할 리소스를 알 수 있도록 서비스 작업자에 의해 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-217">The resource list is loaded by the service worker so that it knows which resources to cache.</span></span>
* <span data-ttu-id="90fab-218">사용자가 앱을 방문할 때마다 브라우저는 백그라운드에서 `service-worker.js` 및 `service-worker-assets.js`를 다시 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-218">Each time the user visits the app, the browser re-requests `service-worker.js` and `service-worker-assets.js` in the background.</span></span> <span data-ttu-id="90fab-219">이 파일은 설치된 기존 서비스 작업자와 바이트 단위로 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-219">The files are compared byte-for-byte with the existing installed service worker.</span></span> <span data-ttu-id="90fab-220">서버에서 이러한 파일 중 하나에 대해 변경된 콘텐츠를 반환하는 경우 서비스 작업자는 새 버전의 파일을 설치하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-220">If the server returns changed content for either of these files, the service worker attempts to install a new version of itself.</span></span>
* <span data-ttu-id="90fab-221">새 버전을 설치하는 경우 서비스 작업자는 오프라인 리소스에 대한 별도의 캐시를 새로 만들고 `service-worker-assets.js`에 나열된 리소스로 캐시를 채우기 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-221">When installing a new version of itself, the service worker creates a new, separate cache for offline resources and starts populating the cache with resources listed in `service-worker-assets.js`.</span></span> <span data-ttu-id="90fab-222">이 논리는 `service-worker.published.js` 내의 `onInstall` 함수에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-222">This logic is implemented in the `onInstall` function inside `service-worker.published.js`.</span></span>
* <span data-ttu-id="90fab-223">모든 리소스가 오류 없이 로드되고 모든 콘텐츠 해시가 일치하면 프로세스가 성공적으로 완료됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-223">The process completes successfully when all of the resources are loaded without error and all content hashes match.</span></span> <span data-ttu-id="90fab-224">성공하면 새 서비스 작업자는 ‘활성화 대기’ 상태로 전환됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-224">If successful, the new service worker enters a *waiting for activation* state.</span></span> <span data-ttu-id="90fab-225">사용자가 앱을 닫으면(남아 있는 앱 탭 또는 창 없음) 새 서비스 작업자는 ‘활성’ 상태가 되며 이후 앱 방문에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-225">As soon as the user closes the app (no remaining app tabs or windows), the new service worker becomes *active* and is used for subsequent app visits.</span></span> <span data-ttu-id="90fab-226">이전 서비스 작업자 및 해당 캐시가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-226">The old service worker and its cache are deleted.</span></span>
* <span data-ttu-id="90fab-227">프로세스가 성공적으로 완료되지 않으면 새 서비스 작업자 인스턴스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-227">If the process doesn't complete successfully, the new service worker instance is discarded.</span></span> <span data-ttu-id="90fab-228">클라이언트에서 요청을 완료할 수 있는 더 나은 네트워크 연결을 사용할 수 있게 되면 사용자의 다음 방문에서 업데이트 프로세스가 다시 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-228">The update process is attempted again on the user's next visit, when hopefully the client has a better network connection that can complete the requests.</span></span>

<span data-ttu-id="90fab-229">서비스 작업자 논리를 편집하여 이 프로세스의 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-229">Customize this process by editing the service worker logic.</span></span> <span data-ttu-id="90fab-230">위의 동작은 Blazor에 국한되지 않지만 PWA 템플릿 옵션에서 제공하는 기본 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-230">None of the preceding behavior is specific to Blazor but is merely the default experience provided by the PWA template option.</span></span> <span data-ttu-id="90fab-231">자세한 내용은 [MDN 웹 문서: 서비스 작업자 API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90fab-231">For more information, see [MDN web docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).</span></span>

### <a name="how-requests-are-resolved"></a><span data-ttu-id="90fab-232">요청을 해결하는 방법</span><span class="sxs-lookup"><span data-stu-id="90fab-232">How requests are resolved</span></span>

<span data-ttu-id="90fab-233">[캐시 우선 페치 전략](#cache-first-fetch-strategy) 단원에서 설명한 대로 기본 서비스 작업자는 ‘캐시 우선’ 전략을 사용합니다. 즉, 사용 가능한 경우 캐시된 콘텐츠를 제공하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-233">As described in the [Cache-first fetch strategy](#cache-first-fetch-strategy) section, the default service worker uses a *cache-first* strategy, meaning that it tries to serve cached content when available.</span></span> <span data-ttu-id="90fab-234">백엔드 API에서 데이터를 요청하는 경우와 같이 특정 URL에 대해 캐시된 콘텐츠가 없는 경우 서비스 작업자는 일반 네트워크 요청으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-234">If there is no content cached for a certain URL, for example when requesting data from a backend API, the service worker falls back on a regular network request.</span></span> <span data-ttu-id="90fab-235">서버에 연결할 수 있는 경우 네트워크 요청이 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-235">The network request succeeds if the server is reachable.</span></span> <span data-ttu-id="90fab-236">이 논리는 `service-worker.published.js` 내의 `onFetch` 함수 안에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-236">This logic is implemented inside `onFetch` function within `service-worker.published.js`.</span></span>

<span data-ttu-id="90fab-237">앱의 Razor 구성 요소가 백엔드 API의 데이터 요청에 의존하며 네트워크 사용 불가로 인해 실패한 요청에 대해 친숙한 사용자 환경을 제공하려는 경우에는 앱의 구성 요소 내에서 논리를 구현해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-237">If the app's Razor components rely on requesting data from backend APIs and you want to provide a friendly user experience for failed requests due to network unavailability, implement logic within the app's components.</span></span> <span data-ttu-id="90fab-238">예를 들어 <xref:System.Net.Http.HttpClient> 요청에 대해 `try/catch`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-238">For example, use `try/catch` around <xref:System.Net.Http.HttpClient> requests.</span></span>

### <a name="support-server-rendered-pages"></a><span data-ttu-id="90fab-239">서버에서 렌더링된 페이지 지원</span><span class="sxs-lookup"><span data-stu-id="90fab-239">Support server-rendered pages</span></span>

<span data-ttu-id="90fab-240">사용자가 `/counter`와 같은 URL 또는 앱의 기타 딥 링크로 처음 이동할 때 발생하는 상황을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-240">Consider what happens when the user first navigates to a URL such as `/counter` or any other deep link in the app.</span></span> <span data-ttu-id="90fab-241">이 경우에는 `/counter`로 캐시된 콘텐츠를 반환하지 않고 대신 Blazor WebAssembly 앱을 시작하기 위해 `/index.html`로 캐시된 콘텐츠를 로드하는 브라우저가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-241">In these cases, you don't want to return content cached as `/counter`, but instead need the browser to load the content cached as `/index.html` to start up your Blazor WebAssembly app.</span></span> <span data-ttu-id="90fab-242">이러한 초기 요청은 다음과 달리 ‘탐색’ 요청이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-242">These initial requests are known as *navigation* requests, as opposed to:</span></span>

* <span data-ttu-id="90fab-243">이미지, 스타일시트 또는 기타 파일에 대한 `subresource` 요청</span><span class="sxs-lookup"><span data-stu-id="90fab-243">`subresource` requests for images, stylesheets, or other files.</span></span>
* <span data-ttu-id="90fab-244">API 데이터에 대한 `fetch/XHR` 요청</span><span class="sxs-lookup"><span data-stu-id="90fab-244">`fetch/XHR` requests for API data.</span></span>

<span data-ttu-id="90fab-245">기본 서비스 작업자는 탐색 요청에 대한 특별 사례 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-245">The default service worker contains special-case logic for navigation requests.</span></span> <span data-ttu-id="90fab-246">서비스 작업자는 요청한 URL과 관계없이 `/index.html`에 대해 캐시된 콘텐츠를 반환하여 요청을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-246">The service worker resolves the requests by returning the cached content for `/index.html`, regardless of the requested URL.</span></span> <span data-ttu-id="90fab-247">이 논리는 `service-worker.published.js` 내의 `onFetch` 함수에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-247">This logic is implemented in the `onFetch` function inside `service-worker.published.js`.</span></span>

<span data-ttu-id="90fab-248">앱에 서버에서 렌더링된 HTML을 반환해야 하고 캐시에서 `/index.html`을 제공하지 않는 특정 URL이 있는 경우 서비스 작업자에서 논리를 편집해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-248">If your app has certain URLs that must return server-rendered HTML, and not serve `/index.html` from the cache, then you need to edit the logic in your service worker.</span></span> <span data-ttu-id="90fab-249">`/Identity/`를 포함하는 모든 URL을 서버에 대한 일반 온라인 전용 요청으로 처리해야 하는 경우 `service-worker.published.js` `onFetch` 논리를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-249">If all URLs containing `/Identity/` need to be handled as regular online-only requests to the server, then modify `service-worker.published.js` `onFetch` logic.</span></span> <span data-ttu-id="90fab-250">다음 코드를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-250">Locate the following code:</span></span>

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

<span data-ttu-id="90fab-251">코드를 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-251">Change the code to the following:</span></span>

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
  && !event.request.url.includes('/Identity/');
```

<span data-ttu-id="90fab-252">이렇게 하지 않으면 네트워크 연결과 관계없이 서비스 작업자는 URL에 대한 요청을 가로채서 `/index.html`을 사용하여 이를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-252">If you don't do this, then regardless of network connectivity, the service worker intercepts requests for such URLs and resolves them using `/index.html`.</span></span>

<span data-ttu-id="90fab-253">외부 인증 공급자를 위한 추가 엔드포인트를 검사에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-253">Add additional endpoints for external authentication providers to the check.</span></span> <span data-ttu-id="90fab-254">다음 예제에서는 Google 인증을 위한 `/signin-google`이 검사에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-254">In the following example, `/signin-google` for Google authentication is added to the check:</span></span>

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
  && !event.request.url.includes('/Identity/')
  && !event.request.url.includes('/signin-google');
```

<span data-ttu-id="90fab-255">콘텐츠를 항상 네트워크에서 가져오는 개발 환경에는 아무 작업도 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-255">No action is required for the Development environment, where content is always fetched from the network.</span></span>

### <a name="control-asset-caching"></a><span data-ttu-id="90fab-256">자산 캐싱 제어</span><span class="sxs-lookup"><span data-stu-id="90fab-256">Control asset caching</span></span>

<span data-ttu-id="90fab-257">프로젝트에서 `ServiceWorkerAssetsManifest` MSBuild 속성을 정의하는 경우 Blazor의 빌드 도구는 지정된 이름을 사용하여 서비스 작업자 자산 매니페스트를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-257">If your project defines the `ServiceWorkerAssetsManifest` MSBuild property, Blazor's build tooling generates a service worker assets manifest with the specified name.</span></span> <span data-ttu-id="90fab-258">기본 PWA 템플릿은 다음 속성을 포함하는 프로젝트 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-258">The default PWA template produces a project file containing the following property:</span></span>

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

<span data-ttu-id="90fab-259">파일은 `wwwroot` 출력 디렉터리에 배치되므로 브라우저는 `/service-worker-assets.js`를 요청하여 이 파일을 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-259">The file is placed in the `wwwroot` output directory, so the browser can retrieve this file by requesting `/service-worker-assets.js`.</span></span> <span data-ttu-id="90fab-260">이 파일의 콘텐츠를 보려면 텍스트 편집기에서 `/bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js`를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-260">To see the contents of this file, open `/bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js` in a text editor.</span></span> <span data-ttu-id="90fab-261">그러나 파일은 각 빌드에서 다시 생성되므로 편집하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="90fab-261">However, don't edit the file, as it's regenerated on each build.</span></span>

<span data-ttu-id="90fab-262">기본적으로 이 매니페스트는 다음을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-262">By default, this manifest lists:</span></span>

* <span data-ttu-id="90fab-263">오프라인에서 작동하는 데 필요한 .NET 어셈블리 및 .NET WebAssembly 런타임 파일 등의 Blazor 관리되는 리소스</span><span class="sxs-lookup"><span data-stu-id="90fab-263">Any Blazor-managed resources, such as .NET assemblies and the .NET WebAssembly runtime files required to function offline.</span></span>
* <span data-ttu-id="90fab-264">외부 프로젝트 및 NuGet 패키지에서 제공하는 정적 웹 자산을 비롯하여 앱의 `wwwroot` 디렉터리에 게시하기 위한 모든 리소스(예: 이미지, 스타일시트 및 JavaScript 파일)</span><span class="sxs-lookup"><span data-stu-id="90fab-264">All resources for publishing to the app's `wwwroot` directory, such as images, stylesheets, and JavaScript files, including static web assets supplied by external projects and NuGet packages.</span></span>

<span data-ttu-id="90fab-265">`service-worker.published.js`의 `onInstall`에서 논리를 편집하여 서비스 작업자에서 이 리소스 중 어떤 항목을 페치하고 캐시하도록 할지 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-265">You can control which of these resources are fetched and cached by the service worker by editing the logic in `onInstall` in `service-worker.published.js`.</span></span> <span data-ttu-id="90fab-266">기본적으로 서비스 작업자는 일반적인 웹 파일 이름 확장명(예: `.html`, `.css`, `.js`, `.wasm`) 및 Blazor WebAssembly 고유의 파일 형식(`.dll`, `.pdb`)과 일치하는 파일을 페치 및 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-266">By default, the service worker fetches and caches files matching typical web filename extensions such as `.html`, `.css`, `.js`, and `.wasm`, plus file types specific to Blazor WebAssembly (`.dll`, `.pdb`).</span></span>

<span data-ttu-id="90fab-267">앱의 `wwwroot` 디렉터리에 없는 추가 리소스를 포함하려면 다음 예제와 같이 추가 MSBuild `ItemGroup` 항목을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-267">To include additional resources that aren't present in the app's `wwwroot` directory, define extra MSBuild `ItemGroup` entries, as shown in the following example:</span></span>

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

<span data-ttu-id="90fab-268">`AssetUrl` 메타데이터는 브라우저가 캐시할 리소스를 인출할 때 사용해야 할 기본적인 상대 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-268">The `AssetUrl` metadata specifies the base-relative URL that the browser should use when fetching the resource to cache.</span></span> <span data-ttu-id="90fab-269">이는 디스크의 원래 원본 파일 이름과 무관할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-269">This can be independent of its original source file name on disk.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="90fab-270">`ServiceWorkerAssetsManifestItem`을 추가한다고 해서 파일이 앱의 `wwwroot` 디렉터리에 게시되지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-270">Adding a `ServiceWorkerAssetsManifestItem` doesn't cause the file to be published in the app's `wwwroot` directory.</span></span> <span data-ttu-id="90fab-271">게시 출력은 개별적으로 제어해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-271">The publish output must be controlled separately.</span></span> <span data-ttu-id="90fab-272">`ServiceWorkerAssetsManifestItem`으로 인해서만 서비스 작업자 자산 매니페스트에 추가 항목이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-272">The `ServiceWorkerAssetsManifestItem` only causes an additional entry to appear in the service worker assets manifest.</span></span>

## <a name="push-notifications"></a><span data-ttu-id="90fab-273">푸시 알림</span><span class="sxs-lookup"><span data-stu-id="90fab-273">Push notifications</span></span>

<span data-ttu-id="90fab-274">다른 PWA와 마찬가지로 Blazor WebAssembly PWA는 백 엔드 서버에서 푸시 알림을 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-274">Like any other PWA, a Blazor WebAssembly PWA can receive push notifications from a backend server.</span></span> <span data-ttu-id="90fab-275">사용자가 앱을 활발히 사용하지 않는 경우에도 서버는 언제든지 푸시 알림을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-275">The server can send push notifications at any time, even when the user isn't actively using the app.</span></span> <span data-ttu-id="90fab-276">예를 들어 다른 사용자가 관련 작업을 수행하는 경우 푸시 알림을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-276">For example, push notifications can be sent when a different user performs a relevant action.</span></span>

<span data-ttu-id="90fab-277">푸시 알림을 전송하는 메커니즘은 모든 기술을 사용할 수 있는 백 엔드 서버에서 구현되기 때문에 Blazor WebAssembly와는 완전히 독립적입니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-277">The mechanism for sending a push notification is entirely independent of Blazor WebAssembly, since it's implemented by the backend server which can use any technology.</span></span> <span data-ttu-id="90fab-278">ASP.NET Core 서버에서 푸시 알림을 보내려면 [Blazing Pizza 워크숍에서 사용하는 접근 방식과 유사한 기법을 사용](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications)하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-278">If you want to send push notifications from an ASP.NET Core server, consider [using a technique similar to the approach taken in the Blazing Pizza workshop](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).</span></span>

<span data-ttu-id="90fab-279">클라이언트에서 푸시 알림을 수신하고 표시하는 메커니즘은 JavaScript 파일인 서비스 작업자에서 구현되기 때문에 이 경우 역시 Blazor WebAssembly와 무관합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-279">The mechanism for receiving and displaying a push notification on the client is also independent of Blazor WebAssembly, since it's implemented in the service worker JavaScript file.</span></span> <span data-ttu-id="90fab-280">예제를 보려면 [Blazing Pizza 워크숍에서 사용하는 접근 방식](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90fab-280">For an example, see [the approach used in the Blazing Pizza workshop](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).</span></span>

## <a name="caveats-for-offline-pwas"></a><span data-ttu-id="90fab-281">오프라인 PWA에 대한 주의 사항</span><span class="sxs-lookup"><span data-stu-id="90fab-281">Caveats for offline PWAs</span></span>

<span data-ttu-id="90fab-282">모든 앱이 오프라인 사용을 지원하려고 하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-282">Not all apps should attempt to support offline use.</span></span> <span data-ttu-id="90fab-283">오프라인 지원은 상당한 복잡성을 추가하고 필요한 사용 사례와 항상 관련이 있는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-283">Offline support adds significant complexity, while not always being relevant for the use cases required.</span></span>

<span data-ttu-id="90fab-284">오프라인 지원은 일반적으로 다음과 같은 경우에만 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-284">Offline support is usually relevant only:</span></span>

* <span data-ttu-id="90fab-285">주 데이터 저장소가 브라우저에 있는 로컬 저장소인 경우.</span><span class="sxs-lookup"><span data-stu-id="90fab-285">If the primary data store is local to the browser.</span></span> <span data-ttu-id="90fab-286">예를 들어 `localStorage` 또는 [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API)에 데이터를 저장하는 [IoT](https://en.wikipedia.org/wiki/Internet_of_things) 디바이스의 UI를 사용하는 앱에서 이 접근 방식이 적절합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-286">For example, the approach is relevant in an app with a UI for an [IoT](https://en.wikipedia.org/wiki/Internet_of_things) device that stores data in `localStorage` or [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).</span></span>
* <span data-ttu-id="90fab-287">앱에서 각 사용자와 관련된 백엔드 API 데이터를 페치 및 캐시하기 위해 많은 작업을 수행하는 경우에는 오프라인으로 데이터를 탐색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-287">If the app performs a significant amount of work to fetch and cache the backend API data relevant to each user so that they can navigate through the data offline.</span></span> <span data-ttu-id="90fab-288">앱에서 편집을 지원해야 하는 경우에는 변경 내용을 추적하고 데이터를 백엔드와 동기화하는 시스템도 빌드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-288">If the app must support editing, a system for tracking changes and synchronizing data with the backend must be built.</span></span>
* <span data-ttu-id="90fab-289">앱이 네트워크 상태와 관계없이 즉시 로드되도록 하는 것이 목표인 경우.</span><span class="sxs-lookup"><span data-stu-id="90fab-289">If the goal is to guarantee that the app loads immediately regardless of network conditions.</span></span> <span data-ttu-id="90fab-290">그런 경우에는 요청 진행 상황을 표시하고 네트워크를 사용할 수 없게 되면 정상적으로 작동하는 백엔드 API 요청에 적합한 사용자 환경을 구현해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-290">Implement a suitable user experience around backend API requests to show the progress of requests and behave gracefully when requests fail due to network unavailability.</span></span>

<span data-ttu-id="90fab-291">또한 오프라인 지원 PWA는 다양한 복잡성을 다루어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-291">Additionally, offline-capable PWAs must deal with a range of additional complications.</span></span> <span data-ttu-id="90fab-292">개발자는 다음 단원의 주의 사항을 숙지해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-292">Developers should carefully familiarize themselves with the caveats in the following sections.</span></span>

### <a name="offline-support-only-when-published"></a><span data-ttu-id="90fab-293">게시된 경우에만 오프라인 지원</span><span class="sxs-lookup"><span data-stu-id="90fab-293">Offline support only when published</span></span>

<span data-ttu-id="90fab-294">개발 중에 백그라운드 업데이트 프로세스를 거치지 않고 브라우저에 즉시 반영되는 각 변경 내용을 확인하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-294">During development you typically want to see each change reflected immediately in the browser without going through a background update process.</span></span> <span data-ttu-id="90fab-295">따라서 Blazor의 PWA 템플릿에서는 게시된 경우에만 오프라인 지원을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-295">Therefore, Blazor's PWA template enables offline support only when published.</span></span>

<span data-ttu-id="90fab-296">오프라인 지원 앱을 빌드하는 경우 개발 환경에서 앱을 테스트하는 것에 충분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-296">When building an offline-capable app, it's not enough to test the app in the Development environment.</span></span> <span data-ttu-id="90fab-297">게시된 상태에서 앱을 테스트하여 다른 네트워크 조건에 어떻게 반응하는지 이해해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-297">You must test the app in its published state to understand how it responds to different network conditions.</span></span>

### <a name="update-completion-after-user-navigation-away-from-app"></a><span data-ttu-id="90fab-298">앱에서 사용자 탐색 후 업데이트 완료</span><span class="sxs-lookup"><span data-stu-id="90fab-298">Update completion after user navigation away from app</span></span>

<span data-ttu-id="90fab-299">사용자가 모든 탭에서 앱을 닫을 때까지는 업데이트가 완료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-299">Updates don't complete until the user has navigated away from the app in all tabs.</span></span> <span data-ttu-id="90fab-300">[백그라운드 업데이트](#background-updates) 단원에 설명된 대로 업데이트를 앱에 배포한 후 브라우저에서 업데이트된 서비스 작업자 파일을 페치하여 업데이트 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-300">As explained in the [Background updates](#background-updates) section, after you deploy an update to the app, the browser fetches the updated service worker files to begin the update process.</span></span>

<span data-ttu-id="90fab-301">많은 개발자가 놀라워하는 부분은 이 업데이트가 완료되는 경우에도 사용자가 모든 탭에서 나갈 때까지 업데이트가 적용되지 **않는다** 는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-301">What surprises many developers is that, even when this update completes, it does **not** take effect until the user has navigated away in all tabs.</span></span> <span data-ttu-id="90fab-302">앱을 표시하는 유일한 탭인 경우에도 앱을 표시하는 탭을 새로 고치는 것으로 충분하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="90fab-302">It is **not** sufficient to refresh the tab displaying the app, even if it's the only tab displaying the app.</span></span> <span data-ttu-id="90fab-303">앱이 완전히 닫힐 때까지 새 서비스 작업자는 ‘활성화 대기’ 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-303">Until your app is completely closed, the new service worker remains in the *waiting to activate* status.</span></span> <span data-ttu-id="90fab-304">**이는 Blazor에 한정되지 않는 표준 웹 플랫폼 동작입니다.**</span><span class="sxs-lookup"><span data-stu-id="90fab-304">**This is not specific to Blazor, but rather is a standard web platform behavior.**</span></span>

<span data-ttu-id="90fab-305">이는 서비스 작업자 또는 오프라인으로 캐시된 리소스에 대한 업데이트를 테스트하려는 개발자를 난감하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-305">This commonly troubles developers who are trying to test updates to their service worker or offline cached resources.</span></span> <span data-ttu-id="90fab-306">브라우저의 개발자 도구를 체크 인하면 다음과 같은 내용이 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-306">If you check in the browser's developer tools, you may see something like the following:</span></span>

![Google Chrome ‘애플리케이션’ 탭에서는 앱의 서비스 작업자가 ‘활성화 대기 중’인지 보여 줍니다.](progressive-web-app/_static/image7.png)

<span data-ttu-id="90fab-308">앱을 표시하는 탭 또는 창에 해당하는 "클라이언트" 목록이 비어 있지 않으면 작업자는 계속 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-308">For as long as the list of "clients," which are tabs or windows displaying your app, is nonempty, the worker continues waiting.</span></span> <span data-ttu-id="90fab-309">서비스 작업자가 이를 수행하는 이유는 일관성을 보장하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-309">The reason service workers do this is to guarantee consistency.</span></span> <span data-ttu-id="90fab-310">일관성은 모든 리소스가 동일한 원자성 캐시에서 페치됨을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-310">Consistency means that all resources are fetched from the same atomic cache.</span></span>

<span data-ttu-id="90fab-311">변경 내용을 테스트할 때 앞의 스크린샷에 표시된 것처럼 “skipWaiting” 링크를 클릭하고 페이지를 다시 로드하는 것이 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-311">When testing changes, you may find it convenient to click the "skipWaiting" link as shown in the preceding screenshot, then reload the page.</span></span> <span data-ttu-id="90fab-312">[“대기 중” 단계를 건너뛰고 업데이트를 즉시 활성화](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase)하도록 서비스 작업자를 코딩하여 모든 사용자에 대해 이를 자동화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-312">You can automate this for all users by coding your service worker to [skip the "waiting" phase and immediately activate on update](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase).</span></span> <span data-ttu-id="90fab-313">대기 중 단계를 건너뛰면 리소스가 항상 동일한 캐시 인스턴스에서 일관되게 페치되는 것을 보장할 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-313">If you skip the waiting phase, you're giving up the guarantee that resources are always fetched consistently from the same cache instance.</span></span>

### <a name="users-may-run-any-historical-version-of-the-app"></a><span data-ttu-id="90fab-314">사용자는 앱의 모든 기록 버전을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-314">Users may run any historical version of the app</span></span>

<span data-ttu-id="90fab-315">웹 개발자는 사용자가 최신 버전의 웹앱만 실행할 것이라고 습관적으로 예상합니다. 그것이 기존 웹 배포 모델 내에서 일반적인 방식이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-315">Web developers habitually expect that users only run the latest deployed version of their web app, since that's normal within the traditional web distribution model.</span></span> <span data-ttu-id="90fab-316">그러나 오프라인 우선 PWA는 네이티브 모바일 앱과 유사하여 사용자가 반드시 최신 버전을 실행하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-316">However, an offline-first PWA is more akin to a native mobile app, where users aren't necessarily running the latest version.</span></span>

<span data-ttu-id="90fab-317">[백그라운드 업데이트](#background-updates)에 설명된 대로 앱에 업데이트를 배포한 후에는 **각 기존 사용자가 최소 1회 이상의 추가 방문을 위해 이전 버전을 계속 사용합니다.** (업데이트는 백그라운드에서 이루어지며 사용자가 앱을 종료하기 전까지는 업데이트가 활성화되지 않기 때문입니다.)</span><span class="sxs-lookup"><span data-stu-id="90fab-317">As explained in the [Background updates](#background-updates) section, after you deploy an update to your app, **each existing user continues to use a previous version for at least one further visit** because the update occurs in the background and isn't activated until the user thereafter navigates away.</span></span> <span data-ttu-id="90fab-318">또한 사용되는 이전 버전은 이전에 배포한 것이 아닐 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-318">Plus, the previous version being used isn't necessarily the previous one you deployed.</span></span> <span data-ttu-id="90fab-319">이전 버전은 사용자가 마지막으로 업데이트를 완료한 시기에 따라 ‘임의의’ 과거 버전일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-319">The previous version can be *any* historical version, depending on when the user last completed an update.</span></span>

<span data-ttu-id="90fab-320">앱의 프런트엔드 및 백엔드 부분에 API 요청을 위한 스키마에 대한 동의가 필요한 경우에 이런 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-320">This can be an issue if the frontend and backend parts of your app require agreement about the schema for API requests.</span></span> <span data-ttu-id="90fab-321">모든 사용자가 업그레이드되었는지 확인할 수 있기 전에는 호환되지 않는 API 스키마 변경을 배포하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-321">You must not deploy backward-incompatible API schema changes until you can be sure that all users have upgraded.</span></span> <span data-ttu-id="90fab-322">또는 사용자가 호환되지 않는 이전 버전의 앱을 사용하지 못하도록 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-322">Alternatively, block users from using incompatible older versions of the app.</span></span> <span data-ttu-id="90fab-323">이 시나리오 요구 사항은 기본 모바일 앱의 경우와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-323">This scenario requirement is the same as for native mobile apps.</span></span> <span data-ttu-id="90fab-324">서버 API에서 호환성이 손상되는 변경을 배포하는 경우 클라이언트 앱은 아직 업데이트되지 않은 사용자를 위해 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-324">If you deploy a breaking change in server APIs, the client app is broken for users who haven't yet updated.</span></span>

<span data-ttu-id="90fab-325">가능하면 백엔드 API에 대한 주요 변경 내용을 배포하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="90fab-325">If possible, don't deploy breaking changes to your backend APIs.</span></span> <span data-ttu-id="90fab-326">그러나 배포해야 한다면 [ServiceWorkerRegistration과 같은 표준 서비스 작업자 API](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration)를 사용하여 앱이 최신 상태인지 여부를 확인하고 그렇지 않은 경우 사용을 금지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-326">If you must do so, consider using [standard Service Worker APIs such as ServiceWorkerRegistration](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) to determine whether the app is up-to-date, and if not, to prevent usage.</span></span>

### <a name="interference-with-server-rendered-pages"></a><span data-ttu-id="90fab-327">서버 렌더링 페이지 간섭</span><span class="sxs-lookup"><span data-stu-id="90fab-327">Interference with server-rendered pages</span></span>

<span data-ttu-id="90fab-328">[서버 렌더링 페이지 지원](#support-server-rendered-pages) 단원에서 설명한 대로 모든 탐색 요청에 대한 서비스 작업자의 `/index.html` 콘텐츠 반환 동작을 무시하려면 서비스 작업자에서 논리를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-328">As described in the [Support server-rendered pages](#support-server-rendered-pages) section, if you want to bypass the service worker's behavior of returning `/index.html` contents for all navigation requests, edit the logic in your service worker.</span></span>

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a><span data-ttu-id="90fab-329">모든 서비스 작업자 자산 매니페스트 콘텐츠는 기본적으로 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-329">All service worker asset manifest contents are cached by default</span></span>

<span data-ttu-id="90fab-330">[자산 캐싱 제어](#control-asset-caching) 섹션에 설명된 대로 `service-worker-assets.js` 파일이 빌드 중에 생성되며 서비스 작업자가 페치 및 캐시해야 하는 모든 자산을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-330">As described in the [Control asset caching](#control-asset-caching) section, the file `service-worker-assets.js` is generated during build and lists all assets the service worker should fetch and cache.</span></span>

<span data-ttu-id="90fab-331">이 목록에는 기본적으로 `wwwroot`(외부 패키지 및 프로젝트에서 제공하는 콘텐츠 포함)로 내보내지는 모든 항목이 포함되므로 너무 많은 콘텐츠를 포함하지 않도록 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-331">Since this list by default includes everything emitted to `wwwroot`, including content supplied by external packages and projects, you must be careful not to put too much content there.</span></span> <span data-ttu-id="90fab-332">`wwwroot` 디렉터리에 수백만 개의 이미지가 포함된 경우 서비스 작업자는 모든 이미지를 페치 및 캐시하기 위해 과도한 대역폭을 사용하므로 성공적으로 완료되지 않을 가능성이 큽니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-332">If the `wwwroot` directory contains millions of images, the service worker tries to fetch and cache them all, consuming excessive bandwidth and most likely not completing successfully.</span></span>

<span data-ttu-id="90fab-333">`service-worker.published.js`의 `onInstall` 함수를 편집하여 페치 및 캐시할 매니페스트 콘텐츠의 하위 집합을 제어하기 위한 임의 논리를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-333">Implement arbitrary logic to control which subset of the manifest's contents should be fetched and cached by editing the `onInstall` function in `service-worker.published.js`.</span></span>

### <a name="interaction-with-authentication"></a><span data-ttu-id="90fab-334">인증과의 상호 작용</span><span class="sxs-lookup"><span data-stu-id="90fab-334">Interaction with authentication</span></span>

<span data-ttu-id="90fab-335">PWA 템플릿은 인증과 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-335">The PWA template can be used in conjunction with authentication.</span></span> <span data-ttu-id="90fab-336">사용자가 초기 네트워크에 연결된 경우 오프라인 지원 PWA에서도 인증을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-336">An offline-capable PWA can also support authentication when the user has initial network connectivity.</span></span>

<span data-ttu-id="90fab-337">사용자가 네트워크에 연결되어 있지 않은 경우에는 액세스 토큰을 인증하거나 획득할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-337">When a user doesn't have network connectivity, they can't authenticate or obtain access tokens.</span></span> <span data-ttu-id="90fab-338">기본적으로 네트워크 액세스 없이 로그인 페이지를 방문하려고 하면 "네트워크 오류" 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-338">By default, attempting to visit the login page without network access results in a "network error" message.</span></span> <span data-ttu-id="90fab-339">사용자를 인증하거나 액세스 토큰을 획득하지 않고 사용자가 오프라인 상태에서 유용한 작업을 수행할 수 있도록 하는 UI 흐름을 디자인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-339">You must design a UI flow that allows the user perform useful tasks while offline without attempting to authenticate the user or obtain access tokens.</span></span> <span data-ttu-id="90fab-340">또는 네트워크를 사용할 수 없을 때 정상적으로 실패하도록 앱을 디자인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-340">Alternatively, you can design the app to gracefully fail when the network isn't available.</span></span> <span data-ttu-id="90fab-341">이러한 시나리오를 처리하도록 앱을 디자인할 수 없는 경우 오프라인 지원을 사용하도록 설정하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-341">If the app can't be designed to handle these scenarios, you might not want to enable offline support.</span></span>

<span data-ttu-id="90fab-342">온라인 및 오프라인에서 사용하도록 디자인된 앱이 다시 온라인 상태가 되는 경우 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-342">When an app that's designed for online and offline use is online again:</span></span>

* <span data-ttu-id="90fab-343">앱이 새 액세스 토큰을 프로비저닝해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-343">The app might need to provision a new access token.</span></span>
* <span data-ttu-id="90fab-344">앱은 오프라인 상태일 때 사용자 계정에 수행한 작업을 적용할 수 있도록 다른 사용자가 서비스에 로그인되어 있는지 검색해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-344">The app must detect if a different user is signed into the service so that it can apply operations to the user's account that were made while they were offline.</span></span>

<span data-ttu-id="90fab-345">인증과 상호 작용하는 오프라인 PWA 앱을 만들려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-345">To create an offline PWA app that interacts with authentication:</span></span>

* <span data-ttu-id="90fab-346">마지막으로 로그인한 사용자를 저장하고 앱이 오프라인일 때 저장된 사용자를 사용하는 팩터리로 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-346">Replace the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> with a factory that stores the last signed-in user and uses the stored user when the app is offline.</span></span>
* <span data-ttu-id="90fab-347">앱이 오프라인 상태인 동안 작업을 큐에 추가한 후 앱이 온라인으로 전환되면 작업을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-347">Queue operations while the app is offline and apply them when the app returns online.</span></span>
* <span data-ttu-id="90fab-348">로그아웃하는 동안 저장된 사용자를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-348">During sign out, clear the stored user.</span></span>

<span data-ttu-id="90fab-349">[`CarChecker`](https://github.com/SteveSandersonMS/CarChecker) 샘플 앱은 위의 접근 방식을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="90fab-349">The [`CarChecker`](https://github.com/SteveSandersonMS/CarChecker) sample app demonstrates the preceding approaches.</span></span> <span data-ttu-id="90fab-350">앱의 다음 부분을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90fab-350">See the following parts of the app:</span></span>

* <span data-ttu-id="90fab-351">`OfflineAccountClaimsPrincipalFactory` (`Client/Data/OfflineAccountClaimsPrincipalFactory.cs`)</span><span class="sxs-lookup"><span data-stu-id="90fab-351">`OfflineAccountClaimsPrincipalFactory` (`Client/Data/OfflineAccountClaimsPrincipalFactory.cs`)</span></span>
* <span data-ttu-id="90fab-352">`LocalVehiclesStore` (`Client/Data/LocalVehiclesStore.cs`)</span><span class="sxs-lookup"><span data-stu-id="90fab-352">`LocalVehiclesStore` (`Client/Data/LocalVehiclesStore.cs`)</span></span>
* <span data-ttu-id="90fab-353">`LoginStatus` 구성 요소(`Client/Shared/LoginStatus.razor`)</span><span class="sxs-lookup"><span data-stu-id="90fab-353">`LoginStatus` component (`Client/Shared/LoginStatus.razor`)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90fab-354">추가 자료</span><span class="sxs-lookup"><span data-stu-id="90fab-354">Additional resources</span></span>

* [<span data-ttu-id="90fab-355">무결성 PowerShell 스크립트 문제 해결</span><span class="sxs-lookup"><span data-stu-id="90fab-355">Troubleshoot integrity PowerShell script</span></span>](xref:blazor/host-and-deploy/webassembly#troubleshoot-integrity-powershell-script)
* [<span data-ttu-id="90fab-356">SignalR 인증에 대한 원본 간 협상</span><span class="sxs-lookup"><span data-stu-id="90fab-356">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
