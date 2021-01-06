---
title: ASP.NET Core Blazor용 도구
author: guardrex
description: Blazor 앱을 빌드하는 데 사용할 수 있는 도구에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
ms.openlocfilehash: 4813668f5278473fbaae36d572e69700b3fe771a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97764239"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="f8a68-103">ASP.NET Core Blazor용 도구</span><span class="sxs-lookup"><span data-stu-id="f8a68-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="f8a68-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f8a68-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="f8a68-105">**ASP.NET 및 웹 개발** 워크로드가 있는 최신 버전의 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="f8a68-106">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-106">Create a new project.</span></span>

1. <span data-ttu-id="f8a68-107">**Blazor 앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-107">Select **Blazor App**.</span></span> <span data-ttu-id="f8a68-108">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-108">Select **Next**.</span></span>

1. <span data-ttu-id="f8a68-109">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f8a68-110">**위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f8a68-111">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-111">Select **Create**.</span></span>

1. <span data-ttu-id="f8a68-112">Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f8a68-113">Blazor Server 환경의 경우 **Blazor Server 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f8a68-114">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-114">Select **Create**.</span></span>

   <span data-ttu-id="f8a68-115">호스트된 Blazor WebAssembly 환경의 경우 **ASP.NET Core 호스팅** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-115">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="f8a68-116">두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* (독립 실행형 및 호스팅) 및 *Blazor Server* 에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f8a68-116">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f8a68-117"><kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-117">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="f8a68-118">ASP.NET Core HTTPS 개발 인증서 신뢰에 대한 자세한 내용은 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f8a68-118">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="f8a68-119">[.NET Core SDK](https://dotnet.microsoft.com/download)의 최신 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="f8a68-120">이전에 SDK를 설치한 경우 명령 셸에서 다음 명령을 실행하여 설치된 버전을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="f8a68-121">최신 버전의 [Visual Studio Code](https://code.visualstudio.com)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="f8a68-122">최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="f8a68-123">Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="f8a68-124">호스트된 Blazor WebAssembly 환경의 경우 호스트된 옵션(`-ho` 또는 `--hosted`)을 명령에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="f8a68-125">Blazor Server 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="f8a68-126">두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* (독립 실행형 및 호스팅) 및 *Blazor Server* 에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f8a68-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f8a68-127">Visual Studio Code에서 `WebApplication1` 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="f8a68-128">IDE에서 프로젝트를 빌드 및 디버그하기 위한 자산을 추가하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="f8a68-129">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-129">Select **Yes**.</span></span>

1. <span data-ttu-id="f8a68-130"><kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-130">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="f8a68-131">개발 인증서 신뢰</span><span class="sxs-lookup"><span data-stu-id="f8a68-131">Trust a development certificate</span></span>

<span data-ttu-id="f8a68-132">Linux에서 인증서를 신뢰할 수 있는 중앙 집중식 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-132">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="f8a68-133">일반적으로 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-133">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="f8a68-134">브라우저의 제외 목록에서 애플리케이션의 URL을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-134">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="f8a68-135">`localhost`에 대한 모든 자체 서명된 인증서를 신뢰합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-135">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="f8a68-136">브라우저의 신뢰할 수 있는 인증서 목록에 인증서를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-136">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="f8a68-137">자세한 내용은 브라우저 제조업체 및 Linux 배포판에서 제공하는 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f8a68-137">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="f8a68-138">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-138">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="f8a68-139">**파일** > **새 솔루션** 을 선택하거나 **시작 창** 에서 **새** 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-139">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="f8a68-140">사이드바에서 **웹 및 콘솔** > **앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-140">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="f8a68-141">Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-141">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f8a68-142">Blazor Server 환경의 경우 **Blazor Server 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-142">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f8a68-143">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-143">Select **Next**.</span></span>

   <span data-ttu-id="f8a68-144">두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* (독립 실행형 및 호스팅) 및 *Blazor Server* 에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f8a68-144">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f8a68-145">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-145">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="f8a68-146">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-146">Select **Next**.</span></span>

1. <span data-ttu-id="f8a68-147">호스트된 Blazor WebAssembly 환경의 경우 **ASP.NET Core 호스팅** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-147">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="f8a68-148">**프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="f8a68-149">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-149">Select **Create**.</span></span>

1. <span data-ttu-id="f8a68-150">**실행** > **디버깅하지 않고 시작** 을 선택하여 ‘디버거 없이’ 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="f8a68-151">**실행** > **디버깅 시작** 으로 앱을 실행하거나 실행(&#9654;) 단추를 사용하여 ‘디버거 없이’ 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="f8a68-152">개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="f8a68-153">인증서를 신뢰하려면 사용자 및 키 집합 암호가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-153">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="f8a68-154">ASP.NET Core HTTPS 개발 인증서 신뢰에 대한 자세한 내용은 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f8a68-154">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a><span data-ttu-id="f8a68-155">플랫폼 간 Blazor 개발용 Visual Studio Code 사용</span><span class="sxs-lookup"><span data-stu-id="f8a68-155">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="f8a68-156">[Visual Studio Code](https://code.visualstudio.com/)는 Blazor 앱을 개발하는 데 사용할 수 있는 오픈 소스 플랫폼 간 IDE(통합 개발 환경)입니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-156">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="f8a68-157">.NET CLI를 사용하여 Visual Studio Code를 사용하여 개발할 새 Blazor 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-157">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="f8a68-158">자세한 내용은 [이 문서의 Linux 버전](/aspnet/core/blazor/tooling?pivots=linux)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f8a68-158">For more information, see the [Linux version of this article](/aspnet/core/blazor/tooling?pivots=linux).</span></span>

## <a name="no-locblazor-template-options"></a><span data-ttu-id="f8a68-159">Blazor 템플릿 옵션</span><span class="sxs-lookup"><span data-stu-id="f8a68-159">Blazor template options</span></span>

<span data-ttu-id="f8a68-160">Blazor 프레임워크는 두 개의 각 Blazor 호스팅 모델용 새 앱을 만들기 위한 템플릿을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-160">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="f8a68-161">템플릿은 Blazor 개발을 위해 선택하는 도구(Visual Studio, Mac용 Visual Studio, Visual Studio Code, .NET CLI 등)에 관계없이 새 Blazor 프로젝트와 솔루션을 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-161">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="f8a68-162">Blazor WebAssembly 프로젝트 템플릿: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="f8a68-162">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="f8a68-163">Blazor Server 프로젝트 템플릿: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="f8a68-163">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="f8a68-164">Blazor의 호스팅 모델에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f8a68-164">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="f8a68-165">템플릿 옵션은 명령 셸에서 도움말 옵션(`-h` 또는 `--help`)을 [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI 명령에 전달하여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f8a68-165">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
