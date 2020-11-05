---
title: ASP.NET Core :::no-loc(Blazor):::용 도구
author: guardrex
description: ':::no-loc(Blazor)::: 앱을 빌드하는 데 사용할 수 있는 도구에 대해 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 500342ac979efdee824ac0d4b5757ca9804f3b30
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054816"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="d5c27-103">ASP.NET Core :::no-loc(Blazor):::용 도구</span><span class="sxs-lookup"><span data-stu-id="d5c27-103">Tooling for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="d5c27-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d5c27-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="d5c27-105">**ASP.NET 및 웹 개발** 워크로드가 있는 최신 버전의 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="d5c27-106">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-106">Create a new project.</span></span>

1. <span data-ttu-id="d5c27-107">**:::no-loc(Blazor)::: 앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-107">Select **:::no-loc(Blazor)::: App**.</span></span> <span data-ttu-id="d5c27-108">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-108">Select **Next**.</span></span>

1. <span data-ttu-id="d5c27-109">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="d5c27-110">**위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d5c27-111">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-111">Select **Create**.</span></span>

1. <span data-ttu-id="d5c27-112">:::no-loc(Blazor WebAssembly)::: 환경의 경우 **:::no-loc(Blazor WebAssembly)::: 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-112">For a :::no-loc(Blazor WebAssembly)::: experience, choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span> <span data-ttu-id="d5c27-113">:::no-loc(Blazor Server)::: 환경의 경우 **:::no-loc(Blazor Server)::: 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-113">For a :::no-loc(Blazor Server)::: experience, choose the **:::no-loc(Blazor Server)::: App** template.</span></span> <span data-ttu-id="d5c27-114">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-114">Select **Create**.</span></span>

   <span data-ttu-id="d5c27-115">두 가지 :::no-loc(Blazor)::: 호스팅 모델인 *:::no-loc(Blazor WebAssembly):::* 및 *:::no-loc(Blazor Server):::* 에 관한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d5c27-115">For information on the two :::no-loc(Blazor)::: hosting models, *:::no-loc(Blazor WebAssembly):::* and *:::no-loc(Blazor Server):::* , see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d5c27-116"><kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="d5c27-117">ASP.NET Core HTTPS 개발 인증서 신뢰에 대한 자세한 내용은 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d5c27-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="d5c27-118">[.NET Core SDK](https://dotnet.microsoft.com/download)의 최신 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-118">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="d5c27-119">이전에 SDK를 설치한 경우 명령 셸에서 다음 명령을 실행하여 설치된 버전을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="d5c27-120">최신 버전의 [Visual Studio Code](https://code.visualstudio.com)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="d5c27-121">최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="d5c27-122">:::no-loc(Blazor WebAssembly)::: 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-122">For a :::no-loc(Blazor WebAssembly)::: experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="d5c27-123">:::no-loc(Blazor Server)::: 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-123">For a :::no-loc(Blazor Server)::: experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="d5c27-124">두 가지 :::no-loc(Blazor)::: 호스팅 모델인 *:::no-loc(Blazor WebAssembly):::* 및 *:::no-loc(Blazor Server):::* 에 관한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d5c27-124">For information on the two :::no-loc(Blazor)::: hosting models, *:::no-loc(Blazor WebAssembly):::* and *:::no-loc(Blazor Server):::* , see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d5c27-125">Visual Studio Code에서 `WebApplication1` 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-125">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="d5c27-126">IDE에서 프로젝트를 빌드 및 디버그하기 위한 자산을 추가하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-126">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="d5c27-127">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-127">Select **Yes**.</span></span>

1. <span data-ttu-id="d5c27-128"><kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-128">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="d5c27-129">개발 인증서 신뢰</span><span class="sxs-lookup"><span data-stu-id="d5c27-129">Trust a development certificate</span></span>

<span data-ttu-id="d5c27-130">Linux에서 인증서를 신뢰할 수 있는 중앙 집중식 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-130">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="d5c27-131">일반적으로 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-131">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="d5c27-132">브라우저의 제외 목록에서 애플리케이션의 URL을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-132">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="d5c27-133">`localhost`에 대한 모든 자체 서명된 인증서를 신뢰합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-133">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="d5c27-134">브라우저의 신뢰할 수 있는 인증서 목록에 인증서를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-134">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="d5c27-135">자세한 내용은 브라우저 제조업체 및 Linux 배포판에서 제공하는 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d5c27-135">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="d5c27-136">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-136">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="d5c27-137">**파일** > **새 솔루션** 을 선택하거나 **시작 창** 에서 **새** 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-137">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="d5c27-138">사이드바에서 **웹 및 콘솔** > **앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-138">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="d5c27-139">:::no-loc(Blazor WebAssembly)::: 환경의 경우 **:::no-loc(Blazor WebAssembly)::: 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-139">For a :::no-loc(Blazor WebAssembly)::: experience, choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span> <span data-ttu-id="d5c27-140">:::no-loc(Blazor Server)::: 환경의 경우 **:::no-loc(Blazor Server)::: 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-140">For a :::no-loc(Blazor Server)::: experience, choose the **:::no-loc(Blazor Server)::: App** template.</span></span> <span data-ttu-id="d5c27-141">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-141">Select **Next**.</span></span>

   <span data-ttu-id="d5c27-142">두 가지 :::no-loc(Blazor)::: 호스팅 모델인 *:::no-loc(Blazor WebAssembly):::* 및 *:::no-loc(Blazor Server):::* 에 관한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d5c27-142">For information on the two :::no-loc(Blazor)::: hosting models, *:::no-loc(Blazor WebAssembly):::* and *:::no-loc(Blazor Server):::* , see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d5c27-143">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-143">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="d5c27-144">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-144">Select **Next**.</span></span>

1. <span data-ttu-id="d5c27-145">**프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="d5c27-146">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-146">Select **Create**.</span></span>

1. <span data-ttu-id="d5c27-147">**실행** > **디버깅하지 않고 시작** 을 선택하여 ‘디버거 없이’ 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="d5c27-148">**실행** > **디버깅 시작** 으로 앱을 실행하거나 실행(&#9654;) 단추를 사용하여 ‘디버거 없이’ 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="d5c27-149">개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="d5c27-150">인증서를 신뢰하려면 사용자 및 키 집합 암호가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d5c27-150">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="d5c27-151">ASP.NET Core HTTPS 개발 인증서 신뢰에 대한 자세한 내용은 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d5c27-151">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end
