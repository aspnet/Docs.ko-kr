---
title: ASP.NET Core Blazor용 도구
author: guardrex
description: Blazor 앱을 빌드하는 데 사용할 수 있는 도구에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 19270bb74326dccfee9466b7c1fa61daeab805a2
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394462"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="f862a-103">ASP.NET Core Blazor용 도구</span><span class="sxs-lookup"><span data-stu-id="f862a-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="f862a-104">**ASP.NET 및 웹 개발** 워크로드가 있는 최신 버전의 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="f862a-105">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-105">Create a new project.</span></span>

1. <span data-ttu-id="f862a-106">**Blazor 앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-106">Select **Blazor App**.</span></span> <span data-ttu-id="f862a-107">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-107">Select **Next**.</span></span>

1. <span data-ttu-id="f862a-108">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f862a-109">**위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f862a-110">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-110">Select **Create**.</span></span>

1. <span data-ttu-id="f862a-111">Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f862a-112">Blazor Server 환경의 경우 **Blazor Server 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f862a-113">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-113">Select **Create**.</span></span>

   <span data-ttu-id="f862a-114">호스트된 Blazor WebAssembly 환경의 경우 **ASP.NET Core 호스팅** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="f862a-115">두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* (독립 실행형 및 호스팅) 및 *Blazor Server* 에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f862a-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f862a-116"><kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="f862a-117">ASP.NET Core HTTPS 개발 인증서 신뢰에 대한 자세한 내용은 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f862a-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="f862a-118">호스트된 Blazor WebAssembly 앱을 실행하는 경우 솔루션의 **`Server`** 프로젝트에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-118">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="f862a-119">[.NET Core SDK](https://dotnet.microsoft.com/download)의 최신 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="f862a-120">이전에 SDK를 설치한 경우 명령 셸에서 다음 명령을 실행하여 설치된 버전을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="f862a-121">최신 버전의 [Visual Studio Code](https://code.visualstudio.com)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="f862a-122">최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="f862a-123">Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="f862a-124">호스트된 Blazor WebAssembly 환경의 경우 호스트된 옵션(`-ho` 또는 `--hosted`)을 명령에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   <span data-ttu-id="f862a-125">Blazor Server 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="f862a-126">두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* (독립 실행형 및 호스팅) 및 *Blazor Server* 에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f862a-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f862a-127">Visual Studio Code에서 `WebApplication1` 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="f862a-128">IDE에서 프로젝트를 빌드 및 디버그하기 위한 자산을 추가하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="f862a-129">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-129">Select **Yes**.</span></span>

   <span data-ttu-id="f862a-130">**호스트된 Blazor WebAssembly 시작 및 작업 구성**</span><span class="sxs-lookup"><span data-stu-id="f862a-130">**Hosted Blazor WebAssembly launch and task configuration**</span></span>

   <span data-ttu-id="f862a-131">호스트된 Blazor WebAssembly 솔루션의 경우 `launch.json` 및 `tasks.json` 파일이 포함된 `.vscode` 폴더를 솔루션의 부모 폴더에 추가(또는 이동)합니다. 이 폴더는 `Client`, `Server` 및 `Shared`의 일반적인 프로젝트 폴더 이름을 포함하는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-131">For hosted Blazor WebAssembly solutions, add (or move) the `.vscode` folder with `launch.json` and `tasks.json` files to the solution's parent folder, which is the folder that contains the typical project folder names of `Client`, `Server`, and `Shared`.</span></span> <span data-ttu-id="f862a-132">`launch.json` 및 `tasks.json` 파일의 구성이 **`Server`** 프로젝트에서 호스트된 Blazor WebAssembly 앱을 실행하는지 확인하거나 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-132">Update or confirm that the configuration in the `launch.json` and `tasks.json` files execute a hosted Blazor WebAssembly app from the **`Server`** project.</span></span>

   <span data-ttu-id="f862a-133">**`.vscode/launch.json`** (`launch` 구성):</span><span class="sxs-lookup"><span data-stu-id="f862a-133">**`.vscode/launch.json`** (`launch` configuration):</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   <span data-ttu-id="f862a-134">현재 작업 디렉터리(`cwd`)에 대한 위 구성에서 `{SERVER APP FOLDER}` 자리 표시자는 **`Server`** 프로젝트의 폴더(일반적으로 “`Server`”)입니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-134">In the preceding configuration for the current working directory (`cwd`), the `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>

   <span data-ttu-id="f862a-135">Microsoft Edge를 사용하고 Google Chrome이 시스템에 설치되지 않은 경우 `"browser": "edge"`의 추가 속성을 구성에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-135">If Microsoft Edge is used and Google Chrome isn't installed on the system, add an additional property of `"browser": "edge"` to the configuration.</span></span>

   <span data-ttu-id="f862a-136">`Server`의 프로젝트 폴더 및 기본 브라우저 Google Chrome 대신 디버그 실행용 브라우저로 Microsoft Edge를 생성하는 예제:</span><span class="sxs-lookup"><span data-stu-id="f862a-136">Example for a project folder of `Server` and that spawns Microsoft Edge as the browser for debug runs instead of the default browser Google Chrome:</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   <span data-ttu-id="f862a-137">**`.vscode/tasks.json`** ([`dotnet` 명령](/dotnet/core/tools/dotnet) 인수):</span><span class="sxs-lookup"><span data-stu-id="f862a-137">**`.vscode/tasks.json`** ([`dotnet` command](/dotnet/core/tools/dotnet) arguments):</span></span>

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   <span data-ttu-id="f862a-138">위의 인수는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-138">In the preceding argument:</span></span>

   * <span data-ttu-id="f862a-139">`{SERVER APP FOLDER}` 자리 표시자는 **`Server`** 프로젝트의 폴더(일반적으로 “`Server`”)입니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-139">The `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>
   * <span data-ttu-id="f862a-140">`{PROJECT NAME}` 자리 표시자는 앱의 이름이며 일반적으로 [Blazor 프로젝트 템플릿](xref:blazor/project-structure)에서 생성된 앱에서 솔루션 이름과 그 뒤에 오는 “`.Server`”를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-140">The `{PROJECT NAME}` placeholder is the app's name, typically based on the solution's name followed by "`.Server`" in an app generated from the [Blazor project template](xref:blazor/project-structure).</span></span>

   <span data-ttu-id="f862a-141">[Blazor WebAssembly 앱에서 SignalR 사용에 대한 자습서](xref:tutorials/signalr-blazor)의 다음 예제에서는 `Server`의 프로젝트 폴더 이름과 `BlazorWebAssemblySignalRApp.Server`의 프로젝트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-141">The following example from the [tutorial for using SignalR with a Blazor WebAssembly app](xref:tutorials/signalr-blazor) uses a project folder name of `Server` and a project name of `BlazorWebAssemblySignalRApp.Server`:</span></span>

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. <span data-ttu-id="f862a-142"><kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-142">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="f862a-143">개발 인증서 신뢰</span><span class="sxs-lookup"><span data-stu-id="f862a-143">Trust a development certificate</span></span>

<span data-ttu-id="f862a-144">Linux에서 인증서를 신뢰할 수 있는 중앙 집중식 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-144">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="f862a-145">일반적으로 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-145">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="f862a-146">브라우저의 제외 목록에서 애플리케이션의 URL을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-146">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="f862a-147">`localhost`에 대한 모든 자체 서명된 인증서를 신뢰합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-147">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="f862a-148">브라우저의 신뢰할 수 있는 인증서 목록에 인증서를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-148">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="f862a-149">자세한 내용은 브라우저 제조업체 및 Linux 배포판에서 제공하는 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f862a-149">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="f862a-150">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-150">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="f862a-151">**파일** > **새 솔루션** 을 선택하거나 **시작 창** 에서 **새** 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-151">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="f862a-152">사이드바에서 **웹 및 콘솔** > **앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-152">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="f862a-153">Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-153">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f862a-154">Blazor Server 환경의 경우 **Blazor Server 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-154">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f862a-155">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-155">Select **Next**.</span></span>

   <span data-ttu-id="f862a-156">두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* (독립 실행형 및 호스팅) 및 *Blazor Server* 에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f862a-156">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f862a-157">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-157">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="f862a-158">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-158">Select **Next**.</span></span>

1. <span data-ttu-id="f862a-159">호스트된 Blazor WebAssembly 환경의 경우 **ASP.NET Core 호스팅** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-159">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="f862a-160">**프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-160">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="f862a-161">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-161">Select **Create**.</span></span>

1. <span data-ttu-id="f862a-162">**실행** > **디버깅하지 않고 시작** 을 선택하여 ‘디버거 없이’ 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-162">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="f862a-163">**실행** > **디버깅 시작** 으로 앱을 실행하거나 실행(&#9654;) 단추를 사용하여 ‘디버거 없이’ 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-163">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="f862a-164">개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-164">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="f862a-165">인증서를 신뢰하려면 사용자 및 키 집합 암호가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-165">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="f862a-166">ASP.NET Core HTTPS 개발 인증서 신뢰에 대한 자세한 내용은 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f862a-166">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="f862a-167">호스트된 Blazor WebAssembly 앱을 실행하는 경우 솔루션의 **`Server`** 프로젝트에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-167">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="f862a-168">플랫폼 간 Blazor 개발용 Visual Studio Code 사용</span><span class="sxs-lookup"><span data-stu-id="f862a-168">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="f862a-169">[Visual Studio Code](https://code.visualstudio.com/)는 Blazor 앱을 개발하는 데 사용할 수 있는 오픈 소스 플랫폼 간 IDE(통합 개발 환경)입니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-169">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="f862a-170">.NET CLI를 사용하여 Visual Studio Code를 사용하여 개발할 새 Blazor 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-170">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="f862a-171">자세한 내용은 [이 문서의 Linux 버전](?pivots=linux)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f862a-171">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="f862a-172">Blazor 템플릿 옵션</span><span class="sxs-lookup"><span data-stu-id="f862a-172">Blazor template options</span></span>

<span data-ttu-id="f862a-173">Blazor 프레임워크는 두 개의 각 Blazor 호스팅 모델용 새 앱을 만들기 위한 템플릿을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-173">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="f862a-174">템플릿은 Blazor 개발을 위해 선택하는 도구(Visual Studio, Mac용 Visual Studio, Visual Studio Code, .NET CLI 등)에 관계없이 새 Blazor 프로젝트와 솔루션을 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-174">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="f862a-175">Blazor WebAssembly 프로젝트 템플릿: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="f862a-175">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="f862a-176">Blazor Server 프로젝트 템플릿: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="f862a-176">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="f862a-177">Blazor의 호스팅 모델에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f862a-177">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span> <span data-ttu-id="f862a-178">Blazor 프로젝트 템플릿에 대한 자세한 내용은 <xref:blazor/project-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f862a-178">For more information on Blazor project templates, see <xref:blazor/project-structure>.</span></span>

<span data-ttu-id="f862a-179">템플릿 옵션은 명령 셸에서 도움말 옵션(`-h` 또는 `--help`)을 [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI 명령에 전달하여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f862a-179">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```

## <a name="additional-resources"></a><span data-ttu-id="f862a-180">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="f862a-180">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/project-structure>
