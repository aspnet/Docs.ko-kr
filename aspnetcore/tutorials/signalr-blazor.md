---
title: Blazor와 함께 ASP.NET Core SignalR 사용
author: guardrex
description: Blazor와 함께 ASP.NET Core SignalR을 사용하는 채팅 앱을 만듭니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: f4e51b39c4c3b0c444b08025e9bd74eec0747541
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536388"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="76095-103">Blazor와 함께 ASP.NET Core SignalR 사용</span><span class="sxs-lookup"><span data-stu-id="76095-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="76095-104">이 자습서에서는 Blazor와 함께 SignalR을 이용해서 실시간 앱을 구현하기 위한 기본 사항을 알려 줍니다.</span><span class="sxs-lookup"><span data-stu-id="76095-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="76095-105">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="76095-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="76095-106">Blazor 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="76095-106">Create a Blazor project</span></span>
> * <span data-ttu-id="76095-107">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="76095-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="76095-108">SignalR 허브 추가</span><span class="sxs-lookup"><span data-stu-id="76095-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="76095-109">SignalR 서비스 및 SignalR 허브에 대한 엔드포인트 추가</span><span class="sxs-lookup"><span data-stu-id="76095-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="76095-110">채팅을 위한 Razor 구성 요소 코드 추가</span><span class="sxs-lookup"><span data-stu-id="76095-110">Add Razor component code for chat</span></span>

<span data-ttu-id="76095-111">이 모든 과정을 마치면 채팅 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="76095-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="76095-112">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="76095-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="76095-113">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="76095-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="76095-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="76095-115">[Visual Studio 2019 16.8 이상](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)과 **ASP.NET 및 웹 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="76095-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="76095-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76095-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76095-117">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="76095-118">Mac용 Visual Studio 버전 8.8 이상</span><span class="sxs-lookup"><span data-stu-id="76095-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="76095-119">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="76095-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="76095-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="76095-121">[Visual Studio 2019 16.6 이상](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)과 **ASP.NET 및 웹 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="76095-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="76095-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76095-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76095-123">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="76095-124">Mac용 Visual Studio 버전 8.6 이상</span><span class="sxs-lookup"><span data-stu-id="76095-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="76095-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="76095-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="76095-126">호스트된 Blazor WebAssembly 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="76095-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="76095-127">선택한 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="76095-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76095-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="76095-129">Visual Studio 16.8 이상 및 .NET Core SDK 5.0.0 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="76095-130">Visual Studio 16.6 이상 및 .NET Core SDK 3.1.300 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="76095-131">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-131">Create a new project.</span></span>

1. <span data-ttu-id="76095-132">**Blazor 앱** 를 선택하고 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="76095-133">**프로젝트 이름** 필드에 `BlazorWebAssemblySignalRApp`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="76095-134">**위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="76095-135">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-135">Select **Create**.</span></span>

1. <span data-ttu-id="76095-136">**Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="76095-137">**고급** 에서 **ASP.NET Core 호스팅** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="76095-138">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="76095-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76095-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="76095-140">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="76095-141">`-ho|--hosted` 옵션은 호스트된 Blazor WebAssembly 솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="76095-142">`.vscode` 폴더에서 VS Code 자산 구성에 대한 자세한 내용은 <xref:blazor/tooling>의 **Linux** 운영 체제 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="76095-142">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

   <span data-ttu-id="76095-143">`-o|--output` 옵션은 솔루션에 대한 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="76095-144">솔루션에 대한 폴더를 만든 경우 명령 셸이 해당 폴더에 열려 있으면 `-o|--output` 옵션 및 값을 생략하여 솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="76095-145">Visual Studio Code에서 앱의 프로젝트 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="76095-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="76095-146">앱을 빌드하고 디버그할 자산을 추가하기 위한 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="76095-147">Visual Studio Code는 생성된 `launch.json` 및 `tasks.json` 파일이 포함된 `.vscode` 폴더를 자동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76095-148">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="76095-149">최신 버전의 [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치하고 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="76095-150">**파일** > **새 솔루션** 을 선택하거나 **시작 창** 에서 **새** 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="76095-151">사이드바에서 **웹 및 콘솔** > **앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="76095-152">**Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="76095-153">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-153">Select **Next**.</span></span>

1. <span data-ttu-id="76095-154">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="76095-155">**ASP.NET Core에서 호스트** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="76095-156">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-156">Select **Next**.</span></span>

1. <span data-ttu-id="76095-157">**프로젝트 이름** 필드에서 앱 이름을 `BlazorWebAssemblySignalRApp`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="76095-158">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-158">Select **Create**.</span></span>

   <span data-ttu-id="76095-159">개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="76095-160">인증서를 신뢰하려면 사용자 및 키 집합 암호가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="76095-161">프로젝트 폴더로 이동하고 프로젝트의 솔루션 파일(`.sln`)을 열어 프로젝트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="76095-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76095-162">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="76095-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="76095-163">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="76095-164">`-ho|--hosted` 옵션은 호스트된 Blazor WebAssembly 솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="76095-165">`-o|--output` 옵션은 솔루션에 대한 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="76095-166">솔루션에 대한 폴더를 만든 경우 명령 셸이 해당 폴더에 열려 있으면 `-o|--output` 옵션 및 값을 생략하여 솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="76095-167">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="76095-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76095-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="76095-169">**솔루션 탐색기** 에서 `BlazorWebAssemblySignalRApp.Client` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="76095-170">**NuGet 패키지 관리** 대화 상자에서 **패키지 원본** 이 `nuget.org`로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="76095-171">**찾아보기** 를 선택하고 검색 상자에 `Microsoft.AspNetCore.SignalR.Client`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="76095-172">검색 결과에서 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 패키지를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="76095-173">앱의 공유 프레임워크와 일치하도록 버전을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="76095-174">**설치** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-174">Select **Install**.</span></span>

1. <span data-ttu-id="76095-175">**변경 내용 미리 보기** 대화 상자가 표시되면 **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="76095-176">**라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="76095-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76095-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="76095-178">**통합 터미널**(도구 모음의 **보기** > **터미널**)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="76095-179">이전 버전의 패키지를 추가하려면 `--version {VERSION}` 옵션을 제공합니다. 여기서 `{VERSION}` 자리 표시자는 추가할 패키지의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="76095-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76095-180">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="76095-181">**솔루션** 사이드바에서 `BlazorWebAssemblySignalRApp.Client` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="76095-182">**NuGet 패키지 관리** 대화 상자에서 원본 드롭다운이 `nuget.org`로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="76095-183">**찾아보기** 를 선택하고 검색 상자에 `Microsoft.AspNetCore.SignalR.Client`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="76095-184">검색 결과에서 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 패키지 옆의 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="76095-185">앱의 공유 프레임워크와 일치하도록 버전을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="76095-186">**패키지 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="76095-187">**라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76095-188">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="76095-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="76095-189">솔루션 폴더의 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="76095-190">이전 버전의 패키지를 추가하려면 `--version {VERSION}` 옵션을 제공합니다. 여기서 `{VERSION}` 자리 표시자는 추가할 패키지의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="76095-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="76095-191">System.Text.Encodings.Web 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="76095-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="76095-192">‘이 섹션은 ASP.NET Core 버전 3.x용 앱에만 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="76095-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="76095-193">ASP.NET Core 3.x 앱에서 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x를 사용하는 경우 패키지 확인 문제로 인해 `BlazorWebAssemblySignalRApp.Server` 프로젝트에는 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="76095-194">기본 문제는 .NET 5의 이후 패치 릴리스에서 해결될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="76095-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="76095-195">자세한 내용은 [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="76095-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="76095-196">ASP.NET Core 3.1 호스트 Blazor 솔루션의 `BlazorWebAssemblySignalRApp.Server` 프로젝트에 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web)을 추가하려면 선택한 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="76095-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76095-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="76095-198">**솔루션 탐색기** 에서 `BlazorWebAssemblySignalRApp.Server` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="76095-199">**NuGet 패키지 관리** 대화 상자에서 **패키지 원본** 이 `nuget.org`로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="76095-200">**찾아보기** 를 선택하고 검색 상자에 `System.Text.Encodings.Web`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="76095-201">검색 결과에서 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 패키지를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="76095-202">사용 중인 공유 프레임워크와 일치하는 패키지의 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="76095-203">**설치** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-203">Select **Install**.</span></span>

1. <span data-ttu-id="76095-204">**변경 내용 미리 보기** 대화 상자가 표시되면 **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="76095-205">**라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="76095-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76095-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="76095-207">**통합 터미널**(도구 모음에서 **보기** > **터미널**)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="76095-208">이전 버전의 패키지를 추가하려면 `--version {VERSION}` 옵션을 제공합니다. 여기서 `{VERSION}` 자리 표시자는 추가할 패키지의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="76095-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76095-209">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="76095-210">**솔루션** 사이드바에서 `BlazorWebAssemblySignalRApp.Server` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="76095-211">**NuGet 패키지 관리** 대화 상자에서 원본 드롭다운이 `nuget.org`로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="76095-212">**찾아보기** 를 선택하고 검색 상자에 `System.Text.Encodings.Web`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="76095-213">검색 결과에서 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 패키지 옆의 확인란을 선택하고, 사용 중인 공유 프레임워크와 일치하는 패키지의 올바른 버전을 선택한 다음, **패키지 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="76095-214">**라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76095-215">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="76095-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="76095-216">솔루션 폴더의 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="76095-217">이전 버전의 패키지를 추가하려면 `--version {VERSION}` 옵션을 제공합니다. 여기서 `{VERSION}` 자리 표시자는 추가할 패키지의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="76095-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="76095-218">SignalR 허브 추가</span><span class="sxs-lookup"><span data-stu-id="76095-218">Add a SignalR hub</span></span>

<span data-ttu-id="76095-219">`BlazorWebAssemblySignalRApp.Server` 프로젝트에서 `Hubs`(복수형) 폴더를 만들고 다음 `ChatHub` 클래스(`Hubs/ChatHub.cs`)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="76095-220">서비스 및 SignalR 허브에 대한 엔드포인트 추가</span><span class="sxs-lookup"><span data-stu-id="76095-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="76095-221">`BlazorWebAssemblySignalRApp.Server` 프로젝트에서 `Startup.cs` 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="76095-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="76095-222">`ChatHub` 클래스에 대한 네임스페이스를 파일의 맨 위에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="76095-223">`Startup.ConfigureServices`에 SignalR 및 응답 압축 미들웨어 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="76095-224">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="76095-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="76095-225">처리 파이프라인 구성의 위쪽에서 응답 압축 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="76095-226">컨트롤러와 클라이언트 쪽 대체에 대한 엔드포인트 사이에 허브에 대한 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="76095-227">`Startup.ConfigureServices`에 SignalR 및 응답 압축 미들웨어 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="76095-228">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="76095-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="76095-229">처리 파이프라인 구성의 위쪽에서 응답 압축 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="76095-230">컨트롤러와 클라이언트 쪽 대체에 대한 엔드포인트 사이에 허브에 대한 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="76095-231">채팅을 위한 Razor 구성 요소 코드 추가</span><span class="sxs-lookup"><span data-stu-id="76095-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="76095-232">`BlazorWebAssemblySignalRApp.Client` 프로젝트에서 `Pages/Index.razor` 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="76095-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="76095-233">태그를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="76095-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="76095-234">태그를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="76095-234">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="76095-235">앱 실행</span><span class="sxs-lookup"><span data-stu-id="76095-235">Run the app</span></span>

<span data-ttu-id="76095-236">선택한 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="76095-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76095-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="76095-238">**솔루션 탐색기** 에서 `BlazorWebAssemblySignalRApp.Server` 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="76095-239"><kbd>F5</kbd> 키를 눌러 디버깅이 설정된 상태로 앱을 실행하거나 <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 디버깅 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="76095-240">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="76095-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="76095-241">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="76095-242">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="76095-242">The name and message are displayed on both pages instantly:</span></span>

   ![오가는 메시지를 보여 주는 두 개의 브라우저 창이 열려 있는 SignalR Blazor 샘플 앱](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="76095-244">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="76095-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="76095-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76095-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="76095-246">`.vscode` 폴더에서 VS Code 자산 구성에 대한 자세한 내용은 <xref:blazor/tooling>의 **Linux** 운영 체제 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="76095-246">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="76095-247"><kbd>F5</kbd> 키를 눌러 디버깅이 설정된 상태로 앱을 실행하거나 <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 디버깅 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-247">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="76095-248">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="76095-248">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="76095-249">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-249">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="76095-250">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="76095-250">The name and message are displayed on both pages instantly:</span></span>

   ![오가는 메시지를 보여 주는 두 개의 브라우저 창이 열려 있는 SignalR Blazor 샘플 앱](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="76095-252">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="76095-252">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76095-253">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="76095-254">**솔루션** 사이드바에서 `BlazorWebAssemblySignalRApp.Server` 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-254">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="76095-255"><kbd>⌘</kbd>+<kbd>↩</kbd>를 눌러 디버깅이 설정된 상태로 앱을 실행하거나 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>를 눌러 디버깅 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-255">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="76095-256">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="76095-256">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="76095-257">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-257">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="76095-258">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="76095-258">The name and message are displayed on both pages instantly:</span></span>

   ![오가는 메시지를 보여 주는 두 개의 브라우저 창이 열려 있는 SignalR Blazor 샘플 앱](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="76095-260">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="76095-260">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76095-261">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="76095-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="76095-262">솔루션 폴더의 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-262">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="76095-263">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="76095-263">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="76095-264">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-264">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="76095-265">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="76095-265">The name and message are displayed on both pages instantly:</span></span>

   ![오가는 메시지를 보여 주는 두 개의 브라우저 창이 열려 있는 SignalR Blazor 샘플 앱](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="76095-267">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="76095-267">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="76095-268">Blazor Server 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="76095-268">Create a Blazor Server app</span></span>

<span data-ttu-id="76095-269">선택한 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="76095-269">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76095-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-270">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="76095-271">Visual Studio 16.8 이상 및 .NET Core SDK 5.0.0 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-271">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="76095-272">Visual Studio 16.6 이상 및 .NET Core SDK 3.1.300 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-272">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="76095-273">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-273">Create a new project.</span></span>

1. <span data-ttu-id="76095-274">**Blazor 앱** 를 선택하고 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-274">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="76095-275">**프로젝트 이름** 필드에 `BlazorServerSignalRApp`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-275">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="76095-276">**위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-276">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="76095-277">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-277">Select **Create**.</span></span>

1. <span data-ttu-id="76095-278">**Blazor Server 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-278">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="76095-279">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-279">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="76095-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76095-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="76095-281">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-281">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="76095-282">`-o|--output` 옵션은 프로젝트에 대한 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-282">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="76095-283">프로젝트에 대한 폴더를 만든 경우 명령 셸이 해당 폴더에 열려 있으면 `-o|--output` 옵션 및 값을 생략하여 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-283">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="76095-284">Visual Studio Code에서 앱의 프로젝트 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="76095-284">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="76095-285">앱을 빌드하고 디버그할 자산을 추가하기 위한 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-285">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="76095-286">Visual Studio Code는 생성된 `launch.json` 및 `tasks.json` 파일이 포함된 `.vscode` 폴더를 자동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-286">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span> <span data-ttu-id="76095-287">`.vscode` 폴더에서 VS Code 자산 구성에 대한 자세한 내용은 <xref:blazor/tooling>의 **Linux** 운영 체제 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="76095-287">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76095-288">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="76095-289">최신 버전의 [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치하고 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-289">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="76095-290">**파일** > **새 솔루션** 을 선택하거나 **시작 창** 에서 **새** 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-290">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="76095-291">사이드바에서 **웹 및 콘솔** > **앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-291">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="76095-292">**Blazor Server 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-292">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="76095-293">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-293">Select **Next**.</span></span>

1. <span data-ttu-id="76095-294">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-294">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="76095-295">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-295">Select **Next**.</span></span>

1. <span data-ttu-id="76095-296">**프로젝트 이름** 필드에서 앱 이름을 `BlazorServerSignalRApp`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-296">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="76095-297">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-297">Select **Create**.</span></span>

   <span data-ttu-id="76095-298">개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-298">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="76095-299">인증서를 신뢰하려면 사용자 및 키 집합 암호가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-299">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="76095-300">프로젝트 폴더로 이동하고 프로젝트의 솔루션 파일(`.sln`)을 열어 프로젝트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="76095-300">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76095-301">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="76095-301">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="76095-302">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-302">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="76095-303">`-o|--output` 옵션은 프로젝트에 대한 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-303">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="76095-304">프로젝트에 대한 폴더를 만든 경우 명령 셸이 해당 폴더에 열려 있으면 `-o|--output` 옵션 및 값을 생략하여 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="76095-304">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="76095-305">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="76095-305">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76095-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-306">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="76095-307">**솔루션 탐색기** 에서 `BlazorServerSignalRApp` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-307">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="76095-308">**NuGet 패키지 관리** 대화 상자에서 **패키지 원본** 이 `nuget.org`로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-308">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="76095-309">**찾아보기** 를 선택하고 검색 상자에 `Microsoft.AspNetCore.SignalR.Client`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-309">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="76095-310">검색 결과에서 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 패키지를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-310">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="76095-311">앱의 공유 프레임워크와 일치하도록 버전을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-311">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="76095-312">**설치** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-312">Select **Install**.</span></span>

1. <span data-ttu-id="76095-313">**변경 내용 미리 보기** 대화 상자가 표시되면 **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-313">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="76095-314">**라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-314">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="76095-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76095-315">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="76095-316">**통합 터미널**(도구 모음의 **보기** > **터미널**)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-316">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="76095-317">이전 버전의 패키지를 추가하려면 `--version {VERSION}` 옵션을 제공합니다. 여기서 `{VERSION}` 자리 표시자는 추가할 패키지의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="76095-317">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76095-318">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="76095-319">**솔루션** 사이드바에서 `BlazorServerSignalRApp` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-319">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="76095-320">**NuGet 패키지 관리** 대화 상자에서 원본 드롭다운이 `nuget.org`로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-320">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="76095-321">**찾아보기** 를 선택하고 검색 상자에 `Microsoft.AspNetCore.SignalR.Client`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-321">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="76095-322">검색 결과에서 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 패키지 옆의 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-322">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="76095-323">앱의 공유 프레임워크와 일치하도록 버전을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-323">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="76095-324">**패키지 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-324">Select **Add Package**.</span></span>

1. <span data-ttu-id="76095-325">**라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-325">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76095-326">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="76095-326">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="76095-327">프로젝트 폴더의 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-327">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="76095-328">이전 버전의 패키지를 추가하려면 `--version {VERSION}` 옵션을 제공합니다. 여기서 `{VERSION}` 자리 표시자는 추가할 패키지의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="76095-328">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="76095-329">System.Text.Encodings.Web 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="76095-329">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="76095-330">‘이 섹션은 ASP.NET Core 버전 3.x용 앱에만 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="76095-330">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="76095-331">ASP.NET Core 3.x 앱에서 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x를 사용하는 경우 패키지 확인 문제로 인해 프로젝트에는 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-331">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="76095-332">기본 문제는 .NET 5의 이후 패치 릴리스에서 해결될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="76095-332">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="76095-333">자세한 내용은 [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="76095-333">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="76095-334">프로젝트에 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web)을 추가하려면 선택한 도구에 대한 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="76095-334">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76095-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-335">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="76095-336">**솔루션 탐색기** 에서 `BlazorServerSignalRApp` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-336">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="76095-337">**NuGet 패키지 관리** 대화 상자에서 **패키지 원본** 이 `nuget.org`로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-337">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="76095-338">**찾아보기** 를 선택하고 검색 상자에 `System.Text.Encodings.Web`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-338">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="76095-339">검색 결과에서 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 패키지를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-339">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="76095-340">사용 중인 공유 프레임워크와 일치하는 패키지의 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-340">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="76095-341">**설치** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-341">Select **Install**.</span></span>

1. <span data-ttu-id="76095-342">**변경 내용 미리 보기** 대화 상자가 표시되면 **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-342">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="76095-343">**라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-343">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="76095-344">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76095-344">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="76095-345">**통합 터미널**(도구 모음에서 **보기** > **터미널**)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-345">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="76095-346">이전 버전의 패키지를 추가하려면 `--version {VERSION}` 옵션을 제공합니다. 여기서 `{VERSION}` 자리 표시자는 추가할 패키지의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="76095-346">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76095-347">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="76095-348">**솔루션** 사이드바에서 `BlazorServerSignalRApp` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-348">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="76095-349">**NuGet 패키지 관리** 대화 상자에서 원본 드롭다운이 `nuget.org`로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-349">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="76095-350">**찾아보기** 를 선택하고 검색 상자에 `System.Text.Encodings.Web`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-350">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="76095-351">검색 결과에서 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 패키지 옆의 확인란을 선택하고, 사용 중인 공유 프레임워크와 일치하는 패키지의 올바른 버전을 선택한 다음, **패키지 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-351">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="76095-352">**라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-352">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76095-353">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="76095-353">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="76095-354">프로젝트 폴더의 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-354">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="76095-355">이전 버전의 패키지를 추가하려면 `--version {VERSION}` 옵션을 제공합니다. 여기서 `{VERSION}` 자리 표시자는 추가할 패키지의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="76095-355">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="76095-356">SignalR 허브 추가</span><span class="sxs-lookup"><span data-stu-id="76095-356">Add a SignalR hub</span></span>

<span data-ttu-id="76095-357">`Hubs`(복수형) 폴더를 만들고 다음 `ChatHub` 클래스(`Hubs/ChatHub.cs`)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-357">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="76095-358">서비스 및 SignalR 허브에 대한 엔드포인트 추가</span><span class="sxs-lookup"><span data-stu-id="76095-358">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="76095-359">`Startup.cs` 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="76095-359">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="76095-360"><xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> 및 `ChatHub` 클래스에 대한 네임스페이스를 파일 맨 위에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-360">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="76095-361">응답 압축 미들웨어 서비스를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-361">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="76095-362">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="76095-362">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="76095-363">처리 파이프라인 구성의 위쪽에서 응답 압축 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-363">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="76095-364">Blazor 허브를 매핑하기 위한 엔드포인트와 클라이언트 쪽 대체 사이에 허브에 대한 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-364">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="76095-365">응답 압축 미들웨어 서비스를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-365">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="76095-366">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="76095-366">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="76095-367">처리 파이프라인 구성의 위쪽에서 응답 압축 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-367">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="76095-368">Blazor 허브를 매핑하기 위한 엔드포인트와 클라이언트 쪽 대체 사이에 허브에 대한 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-368">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="76095-369">채팅을 위한 Razor 구성 요소 코드 추가</span><span class="sxs-lookup"><span data-stu-id="76095-369">Add Razor component code for chat</span></span>

1. <span data-ttu-id="76095-370">`Pages/Index.razor` 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="76095-370">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="76095-371">태그를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="76095-371">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="76095-372">태그를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="76095-372">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="76095-373">앱 실행</span><span class="sxs-lookup"><span data-stu-id="76095-373">Run the app</span></span>

<span data-ttu-id="76095-374">선택한 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="76095-374">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76095-375">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-375">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="76095-376"><kbd>F5</kbd> 키를 눌러 디버깅이 설정된 상태로 앱을 실행하거나 <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 디버깅 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-376">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="76095-377">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="76095-377">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="76095-378">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-378">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="76095-379">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="76095-379">The name and message are displayed on both pages instantly:</span></span>

   ![오가는 메시지를 보여 주는 두 개의 브라우저 창이 열려 있는 SignalR Blazor 샘플 앱](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="76095-381">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="76095-381">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="76095-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76095-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="76095-383"><kbd>F5</kbd> 키를 눌러 디버깅이 설정된 상태로 앱을 실행하거나 <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 디버깅 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-383">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="76095-384">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="76095-384">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="76095-385">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-385">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="76095-386">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="76095-386">The name and message are displayed on both pages instantly:</span></span>

   ![오가는 메시지를 보여 주는 두 개의 브라우저 창이 열려 있는 SignalR Blazor 샘플 앱](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="76095-388">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="76095-388">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76095-389">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76095-389">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="76095-390"><kbd>⌘</kbd>+<kbd>↩</kbd>를 눌러 디버깅이 설정된 상태로 앱을 실행하거나 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>를 눌러 디버깅 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-390">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="76095-391">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="76095-391">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="76095-392">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-392">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="76095-393">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="76095-393">The name and message are displayed on both pages instantly:</span></span>

   ![오가는 메시지를 보여 주는 두 개의 브라우저 창이 열려 있는 SignalR Blazor 샘플 앱](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="76095-395">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="76095-395">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76095-396">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="76095-396">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="76095-397">프로젝트 폴더의 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-397">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="76095-398">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="76095-398">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="76095-399">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="76095-399">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="76095-400">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="76095-400">The name and message are displayed on both pages instantly:</span></span>

   ![오가는 메시지를 보여 주는 두 개의 브라우저 창이 열려 있는 SignalR Blazor 샘플 앱](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="76095-402">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="76095-402">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="76095-403">다음 단계</span><span class="sxs-lookup"><span data-stu-id="76095-403">Next steps</span></span>

<span data-ttu-id="76095-404">이 자습서에서는 다음 작업 방법을 알아보았습니다.</span><span class="sxs-lookup"><span data-stu-id="76095-404">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="76095-405">Blazor 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="76095-405">Create a Blazor project</span></span>
> * <span data-ttu-id="76095-406">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="76095-406">Add the SignalR client library</span></span>
> * <span data-ttu-id="76095-407">SignalR 허브 추가</span><span class="sxs-lookup"><span data-stu-id="76095-407">Add a SignalR hub</span></span>
> * <span data-ttu-id="76095-408">SignalR 서비스 및 SignalR 허브에 대한 엔드포인트 추가</span><span class="sxs-lookup"><span data-stu-id="76095-408">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="76095-409">채팅을 위한 Razor 구성 요소 코드 추가</span><span class="sxs-lookup"><span data-stu-id="76095-409">Add Razor component code for chat</span></span>

<span data-ttu-id="76095-410">Blazor 앱 빌드에 대한 자세한 내용은 Blazor 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="76095-410">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="76095-411"><xref:blazor/index>
> [Identity 서버, WebSocket 및 Server-Sent 이벤트를 사용하는 전달자 토큰 인증](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="76095-411"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="76095-412">추가 자료</span><span class="sxs-lookup"><span data-stu-id="76095-412">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="76095-413">SignalR 인증에 대한 원본 간 협상</span><span class="sxs-lookup"><span data-stu-id="76095-413">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
