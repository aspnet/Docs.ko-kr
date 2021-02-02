---
title: ASP.NET Core MVC 시작
author: rick-anderson
description: ASP.NET Core MVC를 시작하는 방법을 알아봅니다.
ms.author: riande
ms.date: 01/20/2021
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
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710798"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="706ff-103">ASP.NET Core MVC 시작</span><span class="sxs-lookup"><span data-stu-id="706ff-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="706ff-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="706ff-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="706ff-105">이 자습서는 컨트롤러와 뷰를 사용한 ASP.NET Core MVC 웹 개발을 설명하는 시리즈의 첫 번째 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-105">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="706ff-106">시리즈가 끝나면 영화 데이터를 관리하고 표시하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-106">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="706ff-107">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="706ff-108">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="706ff-108">Create a web app.</span></span>
> * <span data-ttu-id="706ff-109">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="706ff-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="706ff-110">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="706ff-110">Work with a database.</span></span>
> * <span data-ttu-id="706ff-111">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="706ff-111">Add search and validation.</span></span>

<span data-ttu-id="706ff-112">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="706ff-113">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="706ff-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="706ff-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706ff-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="706ff-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="706ff-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="706ff-116">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706ff-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="706ff-117">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="706ff-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="706ff-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706ff-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="706ff-119">Visual Studio를 시작하고 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-119">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="706ff-120">**새 프로젝트 만들기** 대화 상자에서 **ASP.NET Core 웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
* <span data-ttu-id="706ff-121">**새 프로젝트 구성** 대화 상자에서 **프로젝트 이름** 으로 `MvcMovie`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="706ff-122">프로젝트 이름을 *MvcMovie* 로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-122">It's important to name the project *MvcMovie*.</span></span> <span data-ttu-id="706ff-123">코드를 복사할 때 대문자 표시가 각 `namespace` 일치 항목과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-123">Capitalization needs to match each `namespace` matches when code is copied.</span></span>
* <span data-ttu-id="706ff-124">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-124">Select **Create**.</span></span>
* <span data-ttu-id="706ff-125">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 다음을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-125">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
  * <span data-ttu-id="706ff-126">드롭다운에서 **.NET Core** 와 **ASP.NET Core 5.0** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-126">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
  * <span data-ttu-id="706ff-127">**ASP.NET Core 웹앱(Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="706ff-127">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
  * <span data-ttu-id="706ff-128">**Create**.</span><span class="sxs-lookup"><span data-stu-id="706ff-128">**Create**.</span></span>

![<span data-ttu-id="706ff-129">새 ASP.NET Core 웹 애플리케이션 만들기</span><span class="sxs-lookup"><span data-stu-id="706ff-129">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="706ff-130">프로젝트를 만드는 다른 방법은 [Visual Studio에서 새 프로젝트 만들기](/visualstudio/ide/create-new-project)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="706ff-130">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="706ff-131">Visual Studio는 생성된 MVC 프로젝트에 기본 프로젝트 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-131">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="706ff-132">생성된 프로젝트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-132">The created project:</span></span>

* <span data-ttu-id="706ff-133">작동하는 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-133">Is a working app.</span></span>
* <span data-ttu-id="706ff-134">기본 시작 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-134">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="706ff-135">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="706ff-135">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="706ff-136">이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-136">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="706ff-137">자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="706ff-137">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="706ff-138">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="706ff-139">프로젝트가 포함될 디렉터리(`cd`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-139">Change to the directory (`cd`) that will contain the project.</span></span>
* <span data-ttu-id="706ff-140">다음 명령 실행:</span><span class="sxs-lookup"><span data-stu-id="706ff-140">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="706ff-141">**빌드 및 디버그하려는 자산이 'MvcMovie'에 없습니다. 추가할까요?** 라는 대화 상자가 표시되면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-141">If a dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**</span></span>

  * <span data-ttu-id="706ff-142">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-142">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="706ff-143">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-143">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="706ff-144">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706ff-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="706ff-145">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-145">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="706ff-147">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-147">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="706ff-148">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-148">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS 웹앱 템플릿 선택](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="706ff-150">**새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-150">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="706ff-151">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-151">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="706ff-152">**대상 프레임워크** 를 선택하는 옵션이 표시되면 최신 5.x 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-152">If an option to select a **Target Framework** is presented, select the latest 5.x version.</span></span>
  * <span data-ttu-id="706ff-153">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-153">Select **Next**.</span></span>

* <span data-ttu-id="706ff-154">프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-154">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 프로젝트 이름 지정](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="706ff-156">앱 실행</span><span class="sxs-lookup"><span data-stu-id="706ff-156">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="706ff-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706ff-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="706ff-158">Ctrl+F5를 선택하여 디버거 없이 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-158">Select Ctrl+F5 to run the app without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="706ff-159">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="706ff-159">Visual Studio:</span></span>

  * <span data-ttu-id="706ff-160">[IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-160">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="706ff-161">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-161">Runs the app.</span></span>

  <span data-ttu-id="706ff-162">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-162">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="706ff-163">로컬 컴퓨터의 표준 호스트 이름은 `localhost`입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-163">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="706ff-164">Visual Studio가 웹 프로젝트를 만들면 임의의 포트가 웹 서버에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-164">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="706ff-165">Ctrl+F5를 선택하여 디버깅 없이 앱을 시작하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-165">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="706ff-166">코드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-166">Make code changes.</span></span>
* <span data-ttu-id="706ff-167">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-167">Save the file.</span></span>
* <span data-ttu-id="706ff-168">브라우저를 빠르게 새로 고치고 코드 변경 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-168">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="706ff-169">**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-169">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![디버그 메뉴](start-mvc/_static/debug_menu50.png)

<span data-ttu-id="706ff-171">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-171">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express50.png)

<span data-ttu-id="706ff-173">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-173">The following image shows the app:</span></span>

![홈 또는 인덱스 페이지](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="706ff-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="706ff-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="706ff-176">Ctrl+F5를 선택하여 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-176">Select Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="706ff-177">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="706ff-177">Visual Studio Code:</span></span>

  * <span data-ttu-id="706ff-178">[Kestrel](xref:fundamentals/servers/kestrel)을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-178">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="706ff-179">브라우저를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-179">Launches a browser.</span></span>
  * <span data-ttu-id="706ff-180">`https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-180">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="706ff-181">주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-181">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="706ff-182">로컬 컴퓨터의 표준 호스트 이름은 `localhost`입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-182">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="706ff-183">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-183">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="706ff-184">Ctrl+F5를 선택하여 디버깅 없이 앱을 시작하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-184">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="706ff-185">코드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-185">Make code changes.</span></span>
* <span data-ttu-id="706ff-186">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-186">Save the file.</span></span>
* <span data-ttu-id="706ff-187">브라우저를 빠르게 새로 고치고 코드 변경 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-187">Quickly refresh the browser and see the code changes.</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="706ff-189">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706ff-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="706ff-190">**실행** > **디버깅하지 않고 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-190">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="706ff-191">Mac용 Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="706ff-191">Visual Studio for Mac:</span></span>

  * <span data-ttu-id="706ff-192">[Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-192">Starts [Kestrel](xref:fundamentals/servers/index#kestrel) server.</span></span>
  * <span data-ttu-id="706ff-193">브라우저를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-193">Launches a browser.</span></span>
  * <span data-ttu-id="706ff-194">`http://localhost:port`로 이동합니다. 여기서 *port* 는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-194">Navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

  [!INCLUDE[](~/includes/trustCertMac.md)]

  <span data-ttu-id="706ff-195">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-195">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="706ff-196">로컬 컴퓨터의 표준 호스트 이름은 `localhost`입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-196">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="706ff-197">Visual Studio가 웹 프로젝트를 만들면 임의의 포트가 웹 서버에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-197">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="706ff-198">**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-198">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="706ff-199">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-199">The following image shows the app:</span></span>

![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="706ff-201">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-201">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="706ff-202">다음: 컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="706ff-202">Next: Add a controller</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="706ff-203">이 자습서는 컨트롤러와 뷰를 사용한 ASP.NET Core MVC 웹 개발을 설명하는 시리즈의 첫 번째 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-203">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="706ff-204">시리즈가 끝나면 영화 데이터를 관리하고 표시하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-204">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="706ff-205">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-205">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="706ff-206">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="706ff-206">Create a web app.</span></span>
> * <span data-ttu-id="706ff-207">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="706ff-207">Add and scaffold a model.</span></span>
> * <span data-ttu-id="706ff-208">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="706ff-208">Work with a database.</span></span>
> * <span data-ttu-id="706ff-209">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="706ff-209">Add search and validation.</span></span>

<span data-ttu-id="706ff-210">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="706ff-211">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="706ff-211">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="706ff-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706ff-212">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="706ff-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="706ff-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="706ff-214">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706ff-214">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="706ff-215">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="706ff-215">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="706ff-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706ff-216">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="706ff-217">Visual Studio에서 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-217">From the Visual Studio, select **Create a new project**.</span></span>

* <span data-ttu-id="706ff-218">**ASP.NET Core 웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-218">Select **ASP.NET Core Web Application** > **Next**.</span></span>

  ![새 ASP.NET Core 웹 애플리케이션 프로젝트 만들기](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="706ff-220">프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-220">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="706ff-221">코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 **MvcMovie** 로 지정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-221">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![새 프로젝트 구성](start-mvc/_static/config.png)

* <span data-ttu-id="706ff-223">**웹 애플리케이션(Model-View-Controller)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-223">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="706ff-224">드롭다운 상자에서 **.NET Core** 와 **ASP.NET Core 3.1** 을 선택한 다음 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-224">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

  ![<span data-ttu-id="706ff-225">새 프로젝트 대화 상자, 왼쪽 창의 .NET Core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="706ff-225">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="706ff-226">Visual Studio는 생성된 MVC 프로젝트에 기본 프로젝트 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-226">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="706ff-227">생성된 프로젝트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-227">The created project:</span></span>

* <span data-ttu-id="706ff-228">작동하는 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-228">Is a working app.</span></span>
* <span data-ttu-id="706ff-229">기본 시작 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-229">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="706ff-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="706ff-230">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="706ff-231">이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-231">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="706ff-232">자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="706ff-232">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="706ff-233">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-233">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="706ff-234">디렉터리(`cd`)를 프로젝트가 포함될 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-234">Change directories (`cd`) to a folder that will contain the project.</span></span>
* <span data-ttu-id="706ff-235">다음 명령 실행:</span><span class="sxs-lookup"><span data-stu-id="706ff-235">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="706ff-236">**Required assets to build and debug are missing from 'MvcMovie'. Add them?** 이라는 대화 상자가 표시되면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-236">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**.</span></span>

  * <span data-ttu-id="706ff-237">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-237">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="706ff-238">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-238">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="706ff-239">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706ff-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="706ff-240">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-240">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="706ff-242">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-242">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="706ff-243">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-243">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS 웹앱 템플릿 선택](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="706ff-245">**새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-245">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="706ff-246">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-246">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="706ff-247">**대상 프레임워크** 를 선택하는 옵션이 표시되면 최신 3.x 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-247">If an option to select a **Target Framework** is presented, select the latest 3.x version.</span></span>
  * <span data-ttu-id="706ff-248">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-248">Select **Next**.</span></span>

* <span data-ttu-id="706ff-249">프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-249">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 프로젝트 이름 지정](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="706ff-251">앱 실행</span><span class="sxs-lookup"><span data-stu-id="706ff-251">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="706ff-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706ff-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="706ff-253">Ctrl+F5를 선택하여 디버깅 없이 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-253">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="706ff-254">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="706ff-254">Visual Studio:</span></span>

  * <span data-ttu-id="706ff-255">[IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-255">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="706ff-256">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-256">Runs the app.</span></span>

  <span data-ttu-id="706ff-257">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-257">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="706ff-258">로컬 컴퓨터의 표준 호스트 이름은 `localhost`입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-258">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="706ff-259">Visual Studio가 웹 프로젝트를 만들면 임의의 포트가 웹 서버에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-259">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="706ff-260">Ctrl+F5를 선택하여 디버깅 없이 앱을 시작하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-260">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="706ff-261">코드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-261">Make code changes.</span></span>
* <span data-ttu-id="706ff-262">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-262">Save the file.</span></span>
* <span data-ttu-id="706ff-263">브라우저를 빠르게 새로 고치고 코드 변경 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-263">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="706ff-264">**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-264">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![디버그 메뉴](start-mvc/_static/debug_menu.png)

<span data-ttu-id="706ff-266">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-266">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="706ff-268">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-268">The following image shows the app:</span></span>

![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="706ff-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="706ff-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="706ff-271">Ctrl+F5를 선택하여 디버깅 없이 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-271">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="706ff-272">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="706ff-272">Visual Studio Code:</span></span>

  * <span data-ttu-id="706ff-273">[Kestrel](xref:fundamentals/servers/kestrel)을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-273">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="706ff-274">브라우저를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-274">Launches a browser.</span></span>
  * <span data-ttu-id="706ff-275">`https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-275">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="706ff-276">주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-276">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="706ff-277">로컬 컴퓨터의 표준 호스트 이름은 `localhost`입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-277">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="706ff-278">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-278">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="706ff-279">Ctrl+F5를 선택하여 디버깅 없이 앱을 시작하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-279">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="706ff-280">코드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-280">Make code changes.</span></span>
* <span data-ttu-id="706ff-281">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-281">Save the file.</span></span>
* <span data-ttu-id="706ff-282">브라우저를 빠르게 새로 고치고 코드 변경 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-282">Quickly refresh the browser and see the code changes.</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="706ff-284">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706ff-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="706ff-285">**실행** > **디버깅하지 않고 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-285">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="706ff-286">Mac용 Visual Studio: [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하고, `http://localhost:port`로 이동합니다. 여기서 *port* 는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-286">Visual Studio for Mac: starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<span data-ttu-id="706ff-287">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-287">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="706ff-288">로컬 컴퓨터의 표준 호스트 이름은 `localhost`입니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-288">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="706ff-289">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-289">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="706ff-290">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-290">When you run the app, you'll see a different port number.</span></span>

<span data-ttu-id="706ff-291">**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-291">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="706ff-292">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-292">The following image shows the app:</span></span>

![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="706ff-294">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="706ff-294">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="706ff-295">다음</span><span class="sxs-lookup"><span data-stu-id="706ff-295">Next</span></span>](adding-controller.md)

::: moniker-end
