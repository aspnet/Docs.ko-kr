---
title: 호스트된 Blazor WebAssembly 앱을 활용하여 ASP.NET Core SignalR 사용
author: guardrex
description: Blazor WebAssembly와 함께 ASP.NET Core SignalR을 사용하는 채팅 앱을 만듭니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 2975ee699a2535cdf63ef2f5af5790f178a09f93
ms.sourcegitcommit: e087b6a38e3d38625ebb567a973e75b4d79547b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637732"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="00dbe-103">호스트된 Blazor WebAssembly 앱을 활용하여 ASP.NET Core SignalR 사용</span><span class="sxs-lookup"><span data-stu-id="00dbe-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="00dbe-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="00dbe-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="00dbe-105">이 자습서에서는 Blazor WebAssembly와 함께 SignalR을 이용해서 실시간 앱을 구현하기 위한 기본 사항을 알려 줍니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="00dbe-106">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="00dbe-107">Blazor WebAssembly 호스트된 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="00dbe-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="00dbe-108">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="00dbe-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="00dbe-109">SignalR 허브 추가</span><span class="sxs-lookup"><span data-stu-id="00dbe-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="00dbe-110">SignalR 서비스 및 SignalR 허브에 대한 엔드포인트 추가</span><span class="sxs-lookup"><span data-stu-id="00dbe-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="00dbe-111">채팅을 위한 Razor 구성 요소 코드 추가</span><span class="sxs-lookup"><span data-stu-id="00dbe-111">Add Razor component code for chat</span></span>

<span data-ttu-id="00dbe-112">이 모든 과정을 마치면 채팅 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="00dbe-113">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="00dbe-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="00dbe-114">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="00dbe-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="00dbe-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00dbe-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="00dbe-116">[Visual Studio 2019 16.8 이상](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)과 **ASP.NET 및 웹 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="00dbe-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="00dbe-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00dbe-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="00dbe-118">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00dbe-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="00dbe-119">Mac용 Visual Studio 버전 8.8 이상</span><span class="sxs-lookup"><span data-stu-id="00dbe-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="00dbe-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="00dbe-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="00dbe-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00dbe-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="00dbe-122">[Visual Studio 2019 16.6 이상](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)과 **ASP.NET 및 웹 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="00dbe-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="00dbe-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00dbe-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="00dbe-124">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00dbe-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="00dbe-125">Mac용 Visual Studio 버전 8.6 이상</span><span class="sxs-lookup"><span data-stu-id="00dbe-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="00dbe-126">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="00dbe-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="00dbe-127">호스트된 Blazor WebAssembly 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="00dbe-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="00dbe-128">선택한 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="00dbe-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="00dbe-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00dbe-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="00dbe-130">Visual Studio 16.8 이상 및 .NET Core SDK 5.0.0 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="00dbe-131">Visual Studio 16.6 이상 및 .NET Core SDK 3.1.300 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="00dbe-132">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-132">Create a new project.</span></span>

1. <span data-ttu-id="00dbe-133">**Blazor 앱** 를 선택하고 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="00dbe-134">**프로젝트 이름** 필드에 `BlazorSignalRApp`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="00dbe-135">**위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="00dbe-136">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-136">Select **Create**.</span></span>

1. <span data-ttu-id="00dbe-137">**Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="00dbe-138">**고급** 에서 **ASP.NET Core 호스팅** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="00dbe-139">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="00dbe-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00dbe-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="00dbe-141">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="00dbe-142">Visual Studio Code에서 앱의 프로젝트 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="00dbe-143">앱을 빌드하고 디버그할 자산을 추가하기 위한 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="00dbe-144">Visual Studio Code는 생성된 `launch.json` 및 `tasks.json` 파일이 포함된 `.vscode` 폴더를 자동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="00dbe-145">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00dbe-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="00dbe-146">최신 버전의 [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치하고 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="00dbe-147">**파일** > **새 솔루션** 을 선택하거나 **시작 창** 에서 **새** 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="00dbe-148">사이드바에서 **웹 및 콘솔** > **앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="00dbe-149">**Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="00dbe-150">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-150">Select **Next**.</span></span>

1. <span data-ttu-id="00dbe-151">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="00dbe-152">**ASP.NET Core에서 호스트** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="00dbe-153">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-153">Select **Next**.</span></span>

1. <span data-ttu-id="00dbe-154">**프로젝트 이름** 필드에서 앱 이름을 `BlazorSignalRApp`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="00dbe-155">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-155">Select **Create**.</span></span>

   <span data-ttu-id="00dbe-156">개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="00dbe-157">인증서를 신뢰하려면 사용자 및 키 집합 암호가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="00dbe-158">프로젝트 폴더로 이동하고 프로젝트의 솔루션 파일(`.sln`)을 열어 프로젝트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="00dbe-159">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="00dbe-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="00dbe-160">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="00dbe-161">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="00dbe-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="00dbe-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00dbe-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="00dbe-163">**솔루션 탐색기** 에서 `BlazorSignalRApp.Client` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="00dbe-164">**NuGet 패키지 관리** 대화 상자에서 **패키지 원본** 이 `nuget.org`로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="00dbe-165">**찾아보기** 를 선택하고 검색 상자에 `Microsoft.AspNetCore.SignalR.Client`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="00dbe-166">검색 결과에서 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 패키지를 선택하고 **설치** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="00dbe-167">**변경 내용 미리 보기** 대화 상자가 표시되면 **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="00dbe-168">**라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="00dbe-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00dbe-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="00dbe-170">**통합 터미널**(도구 모음에서 **보기** > **터미널**)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="00dbe-171">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00dbe-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="00dbe-172">**솔루션** 사이드바에서 `BlazorSignalRApp.Client` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="00dbe-173">**NuGet 패키지 관리** 대화 상자에서 원본 드롭다운이 `nuget.org`로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="00dbe-174">**찾아보기** 를 선택하고 검색 상자에 `Microsoft.AspNetCore.SignalR.Client`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="00dbe-175">검색 결과에서 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 패키지 옆의 확인란을 선택하고 **패키지 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="00dbe-176">**라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="00dbe-177">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="00dbe-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="00dbe-178">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="00dbe-179">SignalR 허브 추가</span><span class="sxs-lookup"><span data-stu-id="00dbe-179">Add a SignalR hub</span></span>

<span data-ttu-id="00dbe-180">`BlazorSignalRApp.Server` 프로젝트에서 `Hubs`(복수형) 폴더를 만들고 다음 `ChatHub` 클래스(`Hubs/ChatHub.cs`)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-180">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="00dbe-181">서비스 및 SignalR 허브에 대한 엔드포인트 추가</span><span class="sxs-lookup"><span data-stu-id="00dbe-181">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="00dbe-182">`BlazorSignalRApp.Server` 프로젝트에서 `Startup.cs` 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-182">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="00dbe-183">`ChatHub` 클래스에 대한 네임스페이스를 파일의 맨 위에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="00dbe-184">`Startup.ConfigureServices`에 SignalR 및 응답 압축 미들웨어 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-184">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="00dbe-185">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="00dbe-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="00dbe-186">처리 파이프라인 구성의 위쪽에서 응답 압축 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="00dbe-187">컨트롤러와 클라이언트 쪽 대체에 대한 엔드포인트 사이에 허브에 대한 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="00dbe-188">채팅을 위한 Razor 구성 요소 코드 추가</span><span class="sxs-lookup"><span data-stu-id="00dbe-188">Add Razor component code for chat</span></span>

1. <span data-ttu-id="00dbe-189">`BlazorSignalRApp.Client` 프로젝트에서 `Pages/Index.razor` 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-189">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="00dbe-190">태그를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="00dbe-191">앱 실행</span><span class="sxs-lookup"><span data-stu-id="00dbe-191">Run the app</span></span>

1. <span data-ttu-id="00dbe-192">선택한 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="00dbe-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="00dbe-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00dbe-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="00dbe-194">**솔루션 탐색기** 에서 `BlazorSignalRApp.Server` 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-194">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="00dbe-195"><kbd>F5</kbd> 키를 눌러 디버깅이 설정된 상태로 앱을 실행하거나 <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 디버깅 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="00dbe-196">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="00dbe-197">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="00dbe-198">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-198">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="00dbe-200">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="00dbe-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="00dbe-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00dbe-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="00dbe-202">서버 앱의 시작 프로필을 만들기 위한 VS Code가 제공되는 경우(`.vscode/launch.json`), `program` 항목은 다음과 유사하게 표시되어 앱의 어셈블리(`{APPLICATION NAME}.Server.dll`)를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="00dbe-203"><kbd>F5</kbd> 키를 눌러 디버깅이 설정된 상태로 앱을 실행하거나 <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 디버깅 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="00dbe-204">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="00dbe-205">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="00dbe-206">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-206">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="00dbe-208">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="00dbe-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="00dbe-209">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00dbe-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="00dbe-210">**솔루션** 사이드바에서 `BlazorSignalRApp.Server` 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-210">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="00dbe-211"><kbd>⌘</kbd>+<kbd>↩</kbd>를 눌러 디버깅이 설정된 상태로 앱을 실행하거나 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>를 눌러 디버깅 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="00dbe-212">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="00dbe-213">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="00dbe-214">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-214">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="00dbe-216">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="00dbe-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="00dbe-217">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="00dbe-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="00dbe-218">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="00dbe-219">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="00dbe-220">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="00dbe-221">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-221">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="00dbe-223">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="00dbe-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="00dbe-224">다음 단계</span><span class="sxs-lookup"><span data-stu-id="00dbe-224">Next steps</span></span>

<span data-ttu-id="00dbe-225">본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.</span><span class="sxs-lookup"><span data-stu-id="00dbe-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="00dbe-226">Blazor WebAssembly 호스트된 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="00dbe-226">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="00dbe-227">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="00dbe-227">Add the SignalR client library</span></span>
> * <span data-ttu-id="00dbe-228">SignalR 허브 추가</span><span class="sxs-lookup"><span data-stu-id="00dbe-228">Add a SignalR hub</span></span>
> * <span data-ttu-id="00dbe-229">SignalR 서비스 및 SignalR 허브에 대한 엔드포인트 추가</span><span class="sxs-lookup"><span data-stu-id="00dbe-229">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="00dbe-230">채팅을 위한 Razor 구성 요소 코드 추가</span><span class="sxs-lookup"><span data-stu-id="00dbe-230">Add Razor component code for chat</span></span>

<span data-ttu-id="00dbe-231">Blazor 앱 빌드에 대한 자세한 내용은 Blazor 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="00dbe-231">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="00dbe-232"><xref:blazor/index>
> [Identity 서버, WebSocket 및 Server-Sent 이벤트를 사용하는 전달자 토큰 인증](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="00dbe-232"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00dbe-233">추가 자료</span><span class="sxs-lookup"><span data-stu-id="00dbe-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="00dbe-234">SignalR 인증에 대한 원본 간 협상</span><span class="sxs-lookup"><span data-stu-id="00dbe-234">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
