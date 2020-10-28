---
title: ASP.NET Core MVC 시작
author: rick-anderson
description: ASP.NET Core MVC를 시작하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/16/2019
no-loc:
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 177112106d143a6826c1f927aac807da0aa9f2b4
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113831"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="f2290-103">ASP.NET Core MVC 시작</span><span class="sxs-lookup"><span data-stu-id="f2290-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="f2290-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f2290-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f2290-105">이 자습서에서는 ASP.NET Core MVC 웹앱을 만들기 위한 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="f2290-106">앱은 동영상 제목의 데이터베이스를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="f2290-107">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f2290-108">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="f2290-108">Create a web app.</span></span>
> * <span data-ttu-id="f2290-109">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="f2290-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f2290-110">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="f2290-110">Work with a database.</span></span>
> * <span data-ttu-id="f2290-111">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="f2290-111">Add search and validation.</span></span>

<span data-ttu-id="f2290-112">자습서를 마치고 나면 동영상 데이터를 관리하고 표시할 수 있는 앱을 만들게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="f2290-113">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="f2290-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2290-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2290-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2290-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2290-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2290-116">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2290-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="f2290-117">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="f2290-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2290-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2290-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2290-119">Visual Studio에서 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-119">From the Visual Studio select **Create a new project** .</span></span>

* <span data-ttu-id="f2290-120">**ASP.NET Core 웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-120">Select **ASP.NET Core Web Application** > **Next** .</span></span>

![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="f2290-122">프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-122">Name the project **MvcMovie** and select **Create** .</span></span> <span data-ttu-id="f2290-123">코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 **MvcMovie** 로 지정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/config.png)

* <span data-ttu-id="f2290-125">**웹 애플리케이션(Model-View-Controller)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-125">Select **Web Application(Model-View-Controller)** .</span></span> <span data-ttu-id="f2290-126">드롭다운 상자에서 **.NET Core** 와 **ASP.NET Core 3.1** 을 선택한 다음 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-126">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1** , then select **Create** .</span></span>

![<span data-ttu-id="f2290-127">새 프로젝트 대화 상자, 왼쪽 창의 .NET Core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="f2290-127">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="f2290-128">Visual Studio는 방금 만든 MVC 프로젝트에 대해 기본 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-128">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="f2290-129">프로젝트 이름을 입력하고 몇 가지 옵션을 선택하여 바로 작동하는 앱을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="f2290-130">이 프로젝트가 기본 시작 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-130">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2290-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2290-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f2290-132">이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="f2290-133">자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f2290-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="f2290-134">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f2290-135">프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="f2290-136">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-136">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f2290-137">**Required assets to build and debug are missing from 'MvcMovie'.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="f2290-138">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-138">Select **Yes**</span></span>

  * <span data-ttu-id="f2290-139">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f2290-140">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2290-141">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2290-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f2290-142">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-142">Select **File** > **New Solution** .</span></span>

  ![macOS 새 솔루션](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f2290-144">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-144">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span> <span data-ttu-id="f2290-145">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-145">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span>

  ![macOS 웹앱 템플릿 선택](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="f2290-147">**새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-147">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="f2290-148">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-148">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="f2290-149">**대상 프레임워크** 를 선택하는 옵션이 제공되는 경우 최신 3.x 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-149">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="f2290-150">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-150">Select **Next** .</span></span>

* <span data-ttu-id="f2290-151">프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-151">Name the project **MvcMovie** , and then select **Create** .</span></span>

  ![macOS 프로젝트 이름 지정](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="f2290-153">앱 실행</span><span class="sxs-lookup"><span data-stu-id="f2290-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2290-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2290-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2290-155">**Ctrl-F5** 를 선택하여 비 디버그 모드에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="f2290-156">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="f2290-157">주소 표시줄에 `localhost:port#` 같은 주소 대신 `example.com`가 표시되는 점에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="f2290-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f2290-158">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f2290-159">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="f2290-160">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f2290-161">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="f2290-162">**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![디버그 메뉴](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="f2290-164">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="f2290-166">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-166">The following image shows the app:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2290-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2290-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f2290-169">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f2290-170">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="f2290-171">주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f2290-172">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="f2290-173">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="f2290-174">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f2290-175">대부분의 개발자는 페이지 및 보기 변경 내용을 새로 고치기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2290-177">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2290-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f2290-178">**실행** > **디버깅하지 않고 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="f2290-179">Mac용 Visual Studio에서 [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하며, `http://localhost:port`로 이동합니다. 여기서 *port* 는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="f2290-180">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f2290-181">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f2290-182">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="f2290-183">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="f2290-184">**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="f2290-185">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-185">The following image shows the app:</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f2290-187">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f2290-188">다음</span><span class="sxs-lookup"><span data-stu-id="f2290-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f2290-189">이 자습서에서는 ASP.NET Core MVC 웹앱을 만들기 위한 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="f2290-190">앱은 동영상 제목의 데이터베이스를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="f2290-191">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f2290-192">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="f2290-192">Create a web app.</span></span>
> * <span data-ttu-id="f2290-193">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="f2290-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f2290-194">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="f2290-194">Work with a database.</span></span>
> * <span data-ttu-id="f2290-195">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="f2290-195">Add search and validation.</span></span>

<span data-ttu-id="f2290-196">자습서를 마치고 나면 동영상 데이터를 관리하고 표시할 수 있는 앱을 만들게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="f2290-197">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="f2290-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2290-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2290-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2290-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2290-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2290-200">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2290-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="f2290-201">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="f2290-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2290-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2290-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2290-203">Visual Studio에서 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-203">From the Visual Studio select **Create a new project** .</span></span>

* <span data-ttu-id="f2290-204">**ASP.NET Core 웹 응용 프로그램** 을 선택한 후, **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-204">Select **ASP.NET Core Web Application** and then select **Next** .</span></span>

![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="f2290-206">프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-206">Name the project **MvcMovie** and select **Create** .</span></span> <span data-ttu-id="f2290-207">코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 **MvcMovie** 로 지정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/config.png)


* <span data-ttu-id="f2290-209">**웹 애플리케이션(Model-View-Controller)** 을 선택한 다음, **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-209">Select **Web Application(Model-View-Controller)** , and then select **Create** .</span></span>

![<span data-ttu-id="f2290-210">새 프로젝트 대화 상자, 왼쪽 창의 .NET Core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="f2290-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="f2290-211">Visual Studio는 방금 만든 MVC 프로젝트에 대해 기본 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="f2290-212">프로젝트 이름을 입력하고 몇 가지 옵션을 선택하여 바로 작동하는 앱을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="f2290-213">다음은 기본 시작 프로젝트이며 여기서 시작하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2290-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2290-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f2290-215">이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="f2290-216">자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f2290-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="f2290-217">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f2290-218">프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="f2290-219">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f2290-220">**Required assets to build and debug are missing from 'MvcMovie'.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="f2290-221">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-221">Select **Yes**</span></span>

  * <span data-ttu-id="f2290-222">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f2290-223">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2290-224">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2290-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f2290-225">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-225">Select **File** > **New Solution** .</span></span>

  ![macOS 새 솔루션](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f2290-227">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span> <span data-ttu-id="f2290-228">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span>

* <span data-ttu-id="f2290-229">**새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-229">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="f2290-230">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-230">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="f2290-231">**대상 프레임워크** 를 선택하는 옵션이 제공되는 경우 최신 2.x 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-231">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="f2290-232">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-232">Select **Next** .</span></span>

* <span data-ttu-id="f2290-233">프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-233">Name the project **MvcMovie** , and then select **Create** .</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="f2290-234">앱 실행</span><span class="sxs-lookup"><span data-stu-id="f2290-234">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2290-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2290-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2290-236">**Ctrl-F5** 를 선택하여 비 디버그 모드에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-236">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="f2290-237">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-237">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="f2290-238">주소 표시줄에 `localhost:port#` 같은 주소 대신 `example.com`가 표시되는 점에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="f2290-238">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f2290-239">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-239">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f2290-240">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="f2290-241">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-241">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f2290-242">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-242">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="f2290-243">**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-243">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![디버그 메뉴](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="f2290-245">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-245">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="f2290-247">**Accept** 를 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-247">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f2290-248">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-248">This app doesn't track personal information.</span></span> <span data-ttu-id="f2290-249">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하는 데 도움이 되는 자산을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-249">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/privacy.png)

  <span data-ttu-id="f2290-251">다음 이미지는 추적을 승인한 후의 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-251">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2290-253">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2290-253">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f2290-254">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-254">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f2290-255">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-255">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="f2290-256">주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-256">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f2290-257">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-257">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="f2290-258">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-258">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="f2290-259">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-259">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f2290-260">대부분의 개발자는 페이지 및 보기 변경 내용을 새로 고치기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-260">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="f2290-261">**Accept** 를 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-261">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f2290-262">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-262">This app doesn't track personal information.</span></span> <span data-ttu-id="f2290-263">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하는 데 도움이 되는 자산을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-263">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/privacy.png)

  <span data-ttu-id="f2290-265">다음 이미지는 추적을 승인한 후의 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-265">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2290-267">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2290-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f2290-268">**실행** > **디버깅하지 않고 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-268">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="f2290-269">Mac용 Visual Studio에서 [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하며, `http://localhost:port`로 이동합니다. 여기서 *port* 는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-269">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="f2290-270">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-270">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f2290-271">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-271">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f2290-272">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-272">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="f2290-273">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-273">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="f2290-274">**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-274">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="f2290-275">**Accept** 를 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-275">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f2290-276">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-276">This app doesn't track personal information.</span></span> <span data-ttu-id="f2290-277">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하는 데 도움이 되는 자산을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-277">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="f2290-279">다음 이미지는 추적을 승인한 후의 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-279">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f2290-281">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="f2290-281">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f2290-282">다음</span><span class="sxs-lookup"><span data-stu-id="f2290-282">Next</span></span>](adding-controller.md)

::: moniker-end
