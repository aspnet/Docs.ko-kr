---
title: ASP.NET Core MVC 시작
author: rick-anderson
description: ASP.NET Core MVC를 시작하는 방법을 알아봅니다.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c96e7107c85bf36f55f6571c71c20d09bc94ddb3
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94688533"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="b55c0-103">ASP.NET Core MVC 시작</span><span class="sxs-lookup"><span data-stu-id="b55c0-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="b55c0-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b55c0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="b55c0-105">이 자습서에서는 ASP.NET Core MVC 웹앱을 만들기 위한 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="b55c0-106">앱은 동영상 제목의 데이터베이스를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="b55c0-107">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b55c0-108">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="b55c0-108">Create a web app.</span></span>
> * <span data-ttu-id="b55c0-109">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="b55c0-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="b55c0-110">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="b55c0-110">Work with a database.</span></span>
> * <span data-ttu-id="b55c0-111">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="b55c0-111">Add search and validation.</span></span>

<span data-ttu-id="b55c0-112">자습서를 마치고 나면 동영상 데이터를 관리하고 표시할 수 있는 앱을 만들게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="b55c0-113">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="b55c0-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b55c0-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b55c0-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b55c0-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b55c0-116">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="b55c0-117">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="b55c0-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b55c0-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b55c0-119">Visual Studio를 시작하고 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="b55c0-120">**새 프로젝트 만들기** 대화 상자에서 **ASP.NET Core 웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="b55c0-121">**새 프로젝트 구성** 대화 상자에서 **프로젝트 이름** 으로 `MvcMovie`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="b55c0-122">코드를 복사할 때 `namespace`가 일치하도록 대문자 표시를 포함하여 이 이름을 정확히 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="b55c0-123">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-123">Select **Create**.</span></span>
1. <span data-ttu-id="b55c0-124">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 다음을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="b55c0-125">드롭다운에서 **.NET Core** 와 **ASP.NET Core 5.0** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="b55c0-126">**ASP.NET Core 웹앱(Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="b55c0-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="b55c0-127">**만들기**</span><span class="sxs-lookup"><span data-stu-id="b55c0-127">**Create**</span></span>

![<span data-ttu-id="b55c0-128">새 ASP.NET Core 웹 애플리케이션 만들기</span><span class="sxs-lookup"><span data-stu-id="b55c0-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="b55c0-129">프로젝트를 만드는 다른 방법은 [Visual Studio에서 새 프로젝트 만들기](/visualstudio/ide/create-new-project)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b55c0-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="b55c0-130">Visual Studio는 방금 만든 MVC 프로젝트에 대해 기본 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="b55c0-131">프로젝트 이름을 입력하고 몇 가지 옵션을 선택하여 바로 작동하는 앱을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="b55c0-132">이 프로젝트가 기본 시작 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b55c0-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b55c0-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b55c0-134">이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="b55c0-135">자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b55c0-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="b55c0-136">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b55c0-137">프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="b55c0-138">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="b55c0-139">**Required assets to build and debug are missing from 'MvcMovie'.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="b55c0-140">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-140">Select **Yes**</span></span>

  * <span data-ttu-id="b55c0-141">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="b55c0-142">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b55c0-143">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b55c0-144">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-144">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="b55c0-146">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="b55c0-147">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS 웹앱 템플릿 선택](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="b55c0-149">**새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="b55c0-150">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="b55c0-151">**대상 프레임워크** 를 선택하는 옵션이 제공되는 경우 최신 5.x 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-151">If presented an option to select a **Target Framework**, select the latest 5.x version.</span></span>

  <span data-ttu-id="b55c0-152">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-152">Select **Next**.</span></span>

* <span data-ttu-id="b55c0-153">프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-153">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 프로젝트 이름 지정](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="b55c0-155">앱 실행</span><span class="sxs-lookup"><span data-stu-id="b55c0-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b55c0-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b55c0-157">**Ctrl-F5** 를 선택하여 비 디버그 모드에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="b55c0-158">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="b55c0-159">주소 표시줄에 `localhost:port#` 같은 주소 대신 `example.com`가 표시되는 점에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="b55c0-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="b55c0-160">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="b55c0-161">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="b55c0-162">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="b55c0-163">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="b55c0-164">**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![디버그 메뉴](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="b55c0-166">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="b55c0-168">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-168">The following image shows the app:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b55c0-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b55c0-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b55c0-171">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="b55c0-172">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="b55c0-173">주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="b55c0-174">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="b55c0-175">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="b55c0-176">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="b55c0-177">대부분의 개발자는 페이지 및 보기 변경 내용을 새로 고치기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b55c0-179">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b55c0-180">**실행** > **디버깅하지 않고 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="b55c0-181">Mac용 Visual Studio에서 [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하며, `http://localhost:port`로 이동합니다. 여기서 *port* 는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="b55c0-182">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="b55c0-183">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="b55c0-184">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="b55c0-185">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="b55c0-186">**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="b55c0-187">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-187">The following image shows the app:</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="b55c0-189">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b55c0-190">다음</span><span class="sxs-lookup"><span data-stu-id="b55c0-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="b55c0-191">이 자습서에서는 ASP.NET Core MVC 웹앱을 만들기 위한 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="b55c0-192">앱은 동영상 제목의 데이터베이스를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="b55c0-193">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b55c0-194">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="b55c0-194">Create a web app.</span></span>
> * <span data-ttu-id="b55c0-195">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="b55c0-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="b55c0-196">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="b55c0-196">Work with a database.</span></span>
> * <span data-ttu-id="b55c0-197">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="b55c0-197">Add search and validation.</span></span>

<span data-ttu-id="b55c0-198">자습서를 마치고 나면 동영상 데이터를 관리하고 표시할 수 있는 앱을 만들게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="b55c0-199">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="b55c0-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b55c0-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b55c0-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b55c0-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b55c0-202">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="b55c0-203">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="b55c0-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b55c0-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b55c0-205">Visual Studio에서 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="b55c0-206">**ASP.NET Core 웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="b55c0-208">프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="b55c0-209">코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 **MvcMovie** 로 지정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/config.png)

* <span data-ttu-id="b55c0-211">**웹 애플리케이션(Model-View-Controller)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="b55c0-212">드롭다운 상자에서 **.NET Core** 와 **ASP.NET Core 3.1** 을 선택한 다음 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="b55c0-213">새 프로젝트 대화 상자, 왼쪽 창의 .NET Core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="b55c0-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="b55c0-214">Visual Studio는 방금 만든 MVC 프로젝트에 대해 기본 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="b55c0-215">프로젝트 이름을 입력하고 몇 가지 옵션을 선택하여 바로 작동하는 앱을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="b55c0-216">이 프로젝트가 기본 시작 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b55c0-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b55c0-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b55c0-218">이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="b55c0-219">자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b55c0-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="b55c0-220">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b55c0-221">프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="b55c0-222">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="b55c0-223">**Required assets to build and debug are missing from 'MvcMovie'.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="b55c0-224">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-224">Select **Yes**</span></span>

  * <span data-ttu-id="b55c0-225">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="b55c0-226">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b55c0-227">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b55c0-228">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-228">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="b55c0-230">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="b55c0-231">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS 웹앱 템플릿 선택](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="b55c0-233">**새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="b55c0-234">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="b55c0-235">**대상 프레임워크** 를 선택하는 옵션이 제공되는 경우 최신 3.x 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-235">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="b55c0-236">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-236">Select **Next**.</span></span>

* <span data-ttu-id="b55c0-237">프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-237">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 프로젝트 이름 지정](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="b55c0-239">앱 실행</span><span class="sxs-lookup"><span data-stu-id="b55c0-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b55c0-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b55c0-241">**Ctrl-F5** 를 선택하여 비 디버그 모드에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="b55c0-242">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="b55c0-243">주소 표시줄에 `localhost:port#` 같은 주소 대신 `example.com`가 표시되는 점에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="b55c0-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="b55c0-244">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="b55c0-245">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="b55c0-246">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="b55c0-247">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="b55c0-248">**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![디버그 메뉴](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="b55c0-250">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="b55c0-252">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-252">The following image shows the app:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b55c0-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b55c0-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b55c0-255">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="b55c0-256">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="b55c0-257">주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="b55c0-258">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="b55c0-259">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="b55c0-260">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="b55c0-261">대부분의 개발자는 페이지 및 보기 변경 내용을 새로 고치기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b55c0-263">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b55c0-264">**실행** > **디버깅하지 않고 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="b55c0-265">Mac용 Visual Studio에서 [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하며, `http://localhost:port`로 이동합니다. 여기서 *port* 는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="b55c0-266">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="b55c0-267">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="b55c0-268">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="b55c0-269">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="b55c0-270">**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="b55c0-271">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-271">The following image shows the app:</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="b55c0-273">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b55c0-274">다음</span><span class="sxs-lookup"><span data-stu-id="b55c0-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="b55c0-275">이 자습서에서는 ASP.NET Core MVC 웹앱을 만들기 위한 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="b55c0-276">앱은 동영상 제목의 데이터베이스를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="b55c0-277">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b55c0-278">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="b55c0-278">Create a web app.</span></span>
> * <span data-ttu-id="b55c0-279">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="b55c0-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="b55c0-280">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="b55c0-280">Work with a database.</span></span>
> * <span data-ttu-id="b55c0-281">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="b55c0-281">Add search and validation.</span></span>

<span data-ttu-id="b55c0-282">자습서를 마치고 나면 동영상 데이터를 관리하고 표시할 수 있는 앱을 만들게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="b55c0-283">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="b55c0-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b55c0-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b55c0-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b55c0-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b55c0-286">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="b55c0-287">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="b55c0-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b55c0-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b55c0-289">Visual Studio에서 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="b55c0-290">**ASP.NET Core 웹 응용 프로그램** 을 선택한 후, **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="b55c0-292">프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="b55c0-293">코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 **MvcMovie** 로 지정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/config.png)


* <span data-ttu-id="b55c0-295">**웹 애플리케이션(Model-View-Controller)** 을 선택한 다음, **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-295">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="b55c0-296">새 프로젝트 대화 상자, 왼쪽 창의 .NET Core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="b55c0-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="b55c0-297">Visual Studio는 방금 만든 MVC 프로젝트에 대해 기본 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="b55c0-298">프로젝트 이름을 입력하고 몇 가지 옵션을 선택하여 바로 작동하는 앱을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="b55c0-299">다음은 기본 시작 프로젝트이며 여기서 시작하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b55c0-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b55c0-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b55c0-301">이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="b55c0-302">자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b55c0-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="b55c0-303">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b55c0-304">프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="b55c0-305">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="b55c0-306">**Required assets to build and debug are missing from 'MvcMovie'.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="b55c0-307">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-307">Select **Yes**</span></span>

  * <span data-ttu-id="b55c0-308">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="b55c0-309">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b55c0-310">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b55c0-311">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-311">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="b55c0-313">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="b55c0-314">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="b55c0-315">**새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="b55c0-316">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="b55c0-317">**대상 프레임워크** 를 선택하는 옵션이 제공되는 경우 최신 2.x 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-317">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="b55c0-318">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-318">Select **Next**.</span></span>

* <span data-ttu-id="b55c0-319">프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-319">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="b55c0-320">앱 실행</span><span class="sxs-lookup"><span data-stu-id="b55c0-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b55c0-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b55c0-322">**Ctrl-F5** 를 선택하여 비 디버그 모드에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="b55c0-323">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="b55c0-324">주소 표시줄에 `localhost:port#` 같은 주소 대신 `example.com`가 표시되는 점에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="b55c0-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="b55c0-325">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="b55c0-326">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="b55c0-327">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="b55c0-328">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="b55c0-329">**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![디버그 메뉴](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="b55c0-331">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="b55c0-333">**Accept** 를 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="b55c0-334">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-334">This app doesn't track personal information.</span></span> <span data-ttu-id="b55c0-335">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하는 데 도움이 되는 자산을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/privacy.png)

  <span data-ttu-id="b55c0-337">다음 이미지는 추적을 승인한 후의 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-337">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b55c0-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b55c0-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b55c0-340">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="b55c0-341">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="b55c0-342">주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="b55c0-343">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="b55c0-344">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="b55c0-345">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="b55c0-346">대부분의 개발자는 페이지 및 보기 변경 내용을 새로 고치기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="b55c0-347">**Accept** 를 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="b55c0-348">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-348">This app doesn't track personal information.</span></span> <span data-ttu-id="b55c0-349">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하는 데 도움이 되는 자산을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/privacy.png)

  <span data-ttu-id="b55c0-351">다음 이미지는 추적을 승인한 후의 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-351">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b55c0-353">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b55c0-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b55c0-354">**실행** > **디버깅하지 않고 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="b55c0-355">Mac용 Visual Studio에서 [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하며, `http://localhost:port`로 이동합니다. 여기서 *port* 는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="b55c0-356">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="b55c0-357">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="b55c0-358">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="b55c0-359">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="b55c0-360">**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="b55c0-361">**Accept** 를 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="b55c0-362">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-362">This app doesn't track personal information.</span></span> <span data-ttu-id="b55c0-363">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하는 데 도움이 되는 자산을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="b55c0-365">다음 이미지는 추적을 승인한 후의 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-365">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="b55c0-367">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b55c0-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b55c0-368">다음</span><span class="sxs-lookup"><span data-stu-id="b55c0-368">Next</span></span>](adding-controller.md)

::: moniker-end
