---
title: '자습서: ASP.NET Core에서 Razor Pages 시작'
author: rick-anderson
description: ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명하는 자습서 시리즈 중 첫 번째입니다.
ms.author: riande
ms.date: 09/15/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: b4dcbe9536107cdc5b0342782abc4bad0b89a8dc
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360926"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="c5321-103">자습서: ASP.NET Core에서 Razor Pages 시작</span><span class="sxs-lookup"><span data-stu-id="c5321-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="c5321-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c5321-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="c5321-105">ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명하는 자습서 시리즈 중 첫 번째입니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="c5321-106">컨트롤러 및 보기에 익숙한 개발자를 대상으로 한 고급 소개는 [Razor Pages 소개](xref:razor-pages/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="c5321-107">시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="c5321-108">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c5321-109">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c5321-110">Razor Pages 웹앱을 Create합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="c5321-111">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-111">Run the app.</span></span>
> * <span data-ttu-id="c5321-112">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-112">Examine the project files.</span></span>

<span data-ttu-id="c5321-113">이 자습서를 마치면 이후의 자습서에서 개선할 Razor Pages 웹앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![홈 또는 Index 페이지](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="c5321-115">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="c5321-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c5321-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c5321-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5321-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c5321-118">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="c5321-119">Razor Pages 웹앱 Create</span><span class="sxs-lookup"><span data-stu-id="c5321-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c5321-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c5321-121">Visual Studio를 시작하고 **새 프로젝트 Create** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="c5321-122">자세한 내용은 [Visual Studio에서 새 프로젝트 Create](/visualstudio/ide/create-new-project)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![시작 창에서 새 프로젝트 Create](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="c5321-124">**새 프로젝트 Create** 대화 상자에서 **ASP.NET Core 웹 애플리케이션** 을 선택한 후, **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

    ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="c5321-126">**새 프로젝트 구성** 대화 상자에서 **프로젝트 이름** 으로 `RazorPagesMovie`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="c5321-127">대문자 표시를 포함하여 프로젝트 이름을 *RazorPagesMovie* 로 지정해야 예제 코드를 복사해 붙여넣을 때 네임스페이스가 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-127">It's important to name the project *RazorPagesMovie*, including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="c5321-128">**Create** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-128">Select **Create**.</span></span>

    ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/config.png)

1. <span data-ttu-id="c5321-130">**새 ASP.NET Core 애플리케이션 Create** 대화 상자에서 다음을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="c5321-131">드롭다운에서 **.NET Core** 와 **ASP.NET Core 5.0** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="c5321-132">**웹 애플리케이션**</span><span class="sxs-lookup"><span data-stu-id="c5321-132">**Web Application**.</span></span>
    1. <span data-ttu-id="c5321-133">**Create**.</span><span class="sxs-lookup"><span data-stu-id="c5321-133">**Create**.</span></span>

     ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="c5321-135">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-135">The following starter project is created:</span></span>

    ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c5321-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5321-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c5321-138">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="c5321-139">프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="c5321-140">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="c5321-141">`dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="c5321-142">`code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c5321-143">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c5321-144">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-144">Select **File** > **New Solution**.</span></span>

    ![macOS 새 솔루션](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="c5321-146">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="c5321-147">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![macOS 웹앱 템플릿 선택](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="c5321-149">**새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="c5321-150">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="c5321-151">**대상 프레임워크** 를 선택하는 옵션이 제공되는 경우 최신 .NET 5.x 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-151">If presented an option to select a **Target Framework**, select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="c5321-152">**다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-152">Select **Next**.</span></span>

1. <span data-ttu-id="c5321-153">프로젝트 이름을 *RazorPagesMovie* 로 지정하고, **Create** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![macOS 프로젝트 이름 지정](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="c5321-155">앱 실행</span><span class="sxs-lookup"><span data-stu-id="c5321-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="c5321-156">프로젝트 파일 검토</span><span class="sxs-lookup"><span data-stu-id="c5321-156">Examine the project files</span></span>

<span data-ttu-id="c5321-157">이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="c5321-158">페이지 폴더</span><span class="sxs-lookup"><span data-stu-id="c5321-158">Pages folder</span></span>

<span data-ttu-id="c5321-159">Razor Pages 및 지원 파일이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="c5321-160">각 Razor 페이지는 파일 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="c5321-161">Razor 구문을 사용하는 C# 코드가 있는 HTML 태그를 포함하는 *.cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="c5321-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="c5321-162">페이지 이벤트를 처리하는 C# 코드가 있는 *.cshtml.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="c5321-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="c5321-163">지원 파일에는 밑줄로 시작하는 이름이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="c5321-164">예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="c5321-165">이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="c5321-166">자세한 내용은 <xref:mvc/views/layout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="c5321-167">wwwroot 폴더</span><span class="sxs-lookup"><span data-stu-id="c5321-167">wwwroot folder</span></span>

<span data-ttu-id="c5321-168">HTML 파일, JavaScript 파일, CSS 파일과 같은 정적 자산을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="c5321-169">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="c5321-170">연결 문자열과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="c5321-171">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="c5321-172">Program.cs</span><span class="sxs-lookup"><span data-stu-id="c5321-172">Program.cs</span></span>

<span data-ttu-id="c5321-173">앱의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-173">Contains the entry point for the app.</span></span> <span data-ttu-id="c5321-174">자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="c5321-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="c5321-175">Startup.cs</span></span>

<span data-ttu-id="c5321-176">앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="c5321-177">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c5321-178">다음 단계</span><span class="sxs-lookup"><span data-stu-id="c5321-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c5321-179">다음: 모델 추가</span><span class="sxs-lookup"><span data-stu-id="c5321-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="c5321-180">ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명하는 자습서 시리즈 중 첫 번째입니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="c5321-181">컨트롤러 및 보기에 익숙한 개발자를 대상으로 한 고급 소개는 [Razor Pages 소개](xref:razor-pages/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="c5321-182">시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="c5321-183">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c5321-184">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c5321-185">Razor Pages 웹앱을 Create합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="c5321-186">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-186">Run the app.</span></span>
> * <span data-ttu-id="c5321-187">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-187">Examine the project files.</span></span>

<span data-ttu-id="c5321-188">이 자습서의 내용을 마치면 이후의 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![홈 또는 Index 페이지](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="c5321-190">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="c5321-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c5321-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c5321-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5321-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c5321-193">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="c5321-194">Razor Pages 웹앱 Create</span><span class="sxs-lookup"><span data-stu-id="c5321-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c5321-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5321-196">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c5321-197">새 ASP.NET Core 웹 애플리케이션을 Create하고 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="c5321-198">![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="c5321-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="c5321-199">프로젝트 이름을 **RazorPagesMovie** 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="c5321-200">코드를 복사하여 붙여 넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie* 로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="c5321-201">![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="c5321-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="c5321-202">드롭다운에서 **ASP.NET Core 3.1** 을 선택하고 **웹 애플리케이션** 을 선택한 다음 **Create** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="c5321-204">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-204">The following starter project is created:</span></span>

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c5321-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5321-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c5321-207">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="c5321-208">프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="c5321-209">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="c5321-210">`dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="c5321-211">`code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="c5321-212">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 **빌드 및 디버그에 필요한 자산이 ‘RazorPagesMovie’에서 누락되었습니다. Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="c5321-213">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-213">Select **Yes**.</span></span>

  <span data-ttu-id="c5321-214">*launch.json* 및 *tasks.json* 파일을 포함한 *.vscode* 디렉터리가 프로젝트의 루트 디렉터리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c5321-215">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c5321-216">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-216">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c5321-218">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="c5321-219">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS 웹앱 템플릿 선택](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="c5321-221">**새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="c5321-222">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="c5321-223">**대상 프레임워크** 를 선택하는 옵션이 제공되는 경우 최신 3.x 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-223">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="c5321-224">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-224">Select **Next**.</span></span>

* <span data-ttu-id="c5321-225">프로젝트 이름을 **RazorPagesMovie** 로 지정한 다음 **Create** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-225">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS 프로젝트 이름 지정](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="c5321-227">앱 실행</span><span class="sxs-lookup"><span data-stu-id="c5321-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="c5321-228">프로젝트 파일 검토</span><span class="sxs-lookup"><span data-stu-id="c5321-228">Examine the project files</span></span>

<span data-ttu-id="c5321-229">이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="c5321-230">페이지 폴더</span><span class="sxs-lookup"><span data-stu-id="c5321-230">Pages folder</span></span>

<span data-ttu-id="c5321-231">Razor Pages 및 지원 파일이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="c5321-232">각 Razor 페이지는 파일 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="c5321-233">Razor 구문을 사용하는 C# 코드가 있는 HTML 태그를 포함하는 *.cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="c5321-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="c5321-234">페이지 이벤트를 처리하는 C# 코드가 있는 *.cshtml.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="c5321-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="c5321-235">지원 파일에는 밑줄로 시작하는 이름이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="c5321-236">예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="c5321-237">이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="c5321-238">자세한 내용은 <xref:mvc/views/layout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="c5321-239">wwwroot 폴더</span><span class="sxs-lookup"><span data-stu-id="c5321-239">wwwroot folder</span></span>

<span data-ttu-id="c5321-240">HTML 파일, JavaScript 파일, CSS 파일과 같은 정적 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="c5321-241">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="c5321-242">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="c5321-242">appSettings.json</span></span>

<span data-ttu-id="c5321-243">연결 문자열과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="c5321-244">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="c5321-245">Program.cs</span><span class="sxs-lookup"><span data-stu-id="c5321-245">Program.cs</span></span>

<span data-ttu-id="c5321-246">프로그램의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-246">Contains the entry point for the program.</span></span> <span data-ttu-id="c5321-247">자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="c5321-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="c5321-248">Startup.cs</span></span>

<span data-ttu-id="c5321-249">앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="c5321-250">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c5321-251">다음 단계</span><span class="sxs-lookup"><span data-stu-id="c5321-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c5321-252">다음: 모델 추가</span><span class="sxs-lookup"><span data-stu-id="c5321-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c5321-253">이 시리즈의 첫 번째 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="c5321-254">[시리즈](xref:tutorials/razor-pages/index)에서는 ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="c5321-255">컨트롤러 및 보기에 익숙한 개발자를 대상으로 한 고급 소개는 [Razor Pages 소개](xref:razor-pages/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="c5321-256">시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="c5321-257">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c5321-258">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c5321-259">Razor Pages 웹앱을 Create합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="c5321-260">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-260">Run the app.</span></span>
> * <span data-ttu-id="c5321-261">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-261">Examine the project files.</span></span>

<span data-ttu-id="c5321-262">이 자습서의 내용을 마치면 이후의 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![홈 또는 Index 페이지](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="c5321-264">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="c5321-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c5321-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c5321-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5321-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c5321-267">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="c5321-268">Razor Pages 웹앱 Create</span><span class="sxs-lookup"><span data-stu-id="c5321-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c5321-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5321-270">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="c5321-271">새 ASP.NET Core 웹 애플리케이션을 Create하고 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="c5321-273">프로젝트 이름을 **RazorPagesMovie** 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="c5321-274">코드를 복사하여 붙여 넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie* 로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/config.png)

* <span data-ttu-id="c5321-276">드롭다운에서 **ASP.NET Core 2.2** 를 선택하고 **웹 애플리케이션** 을 선택한 다음 **Create** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="c5321-278">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-278">The following starter project is created:</span></span>

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c5321-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5321-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c5321-281">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="c5321-282">프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="c5321-283">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="c5321-284">`dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="c5321-285">`code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="c5321-286">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 **빌드 및 디버그에 필요한 자산이 ‘RazorPagesMovie’에서 누락되었습니다. Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="c5321-287">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-287">Select **Yes**.</span></span>

  <span data-ttu-id="c5321-288">*launch.json* 및 *tasks.json* 파일을 포함한 *.vscode* 디렉터리가 프로젝트의 루트 디렉터리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c5321-289">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c5321-290">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-290">Select **File** > **New Solution**.</span></span>

![macOS 새 솔루션](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c5321-292">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="c5321-293">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="c5321-294">**새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="c5321-295">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="c5321-296">**대상 프레임워크** 를 선택하는 옵션이 제공되는 경우 최신 2.x 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-296">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="c5321-297">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-297">Select **Next**.</span></span>

* <span data-ttu-id="c5321-298">프로젝트 이름을 **RazorPagesMovie** 로 지정한 다음 **Create** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-298">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="c5321-300">앱 실행</span><span class="sxs-lookup"><span data-stu-id="c5321-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c5321-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5321-302">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="c5321-303"><kbd>Ctrl+F5</kbd>(디버그 이외 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c5321-304">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>


  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="c5321-305">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c5321-306">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c5321-307">`localhost`가 로컬 컴퓨터의 표준 호스트 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="c5321-308">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="c5321-309">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="c5321-310">앱의 홈페이지에서 **승인** 을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c5321-311">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 Index 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="c5321-313">다음 이미지는 추적에 동의한 후 앱을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![홈 또는 Index 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="c5321-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5321-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="c5321-316"><kbd>Ctrl+F5</kbd>를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="c5321-317"><kbd>Ctrl+F5</kbd>(디버그 이외 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c5321-318">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="c5321-319">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="c5321-320">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c5321-321">`localhost`가 로컬 컴퓨터의 표준 호스트 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="c5321-322">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="c5321-323">앱의 홈페이지에서 **승인** 을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c5321-324">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 Index 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="c5321-326">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-326">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 Index 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c5321-328">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5321-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c5321-329">디버거 없이 실행하려면 **Cmd-Opt-F5** 를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="c5321-330"><kbd>Cmd+Opt+F5</kbd>(비디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c5321-331">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="c5321-332">Visual Studio가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="c5321-333">앱의 홈페이지에서 **승인** 을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c5321-334">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 Index 페이지](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="c5321-336">다음 이미지는 추적에 동의한 후 앱을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![홈 또는 Index 페이지](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="c5321-338">프로젝트 파일 검토</span><span class="sxs-lookup"><span data-stu-id="c5321-338">Examine the project files</span></span>

<span data-ttu-id="c5321-339">이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="c5321-340">페이지 폴더</span><span class="sxs-lookup"><span data-stu-id="c5321-340">Pages folder</span></span>

<span data-ttu-id="c5321-341">Razor Pages 및 지원 파일이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="c5321-342">각 Razor 페이지는 파일 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="c5321-343">Razor 구문을 사용하는 C# 코드가 있는 HTML 태그를 포함하는 *.cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="c5321-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="c5321-344">페이지 이벤트를 처리하는 C# 코드가 있는 *.cshtml.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="c5321-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="c5321-345">지원 파일에는 밑줄로 시작하는 이름이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="c5321-346">예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="c5321-347">이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="c5321-348">자세한 내용은 <xref:mvc/views/layout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="c5321-349">Razor Pages는 `PageModel`에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="c5321-350">일반적으로 `PageModel` 파생 클래스의 이름은 `<PageName>Model`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="c5321-351">wwwroot 폴더</span><span class="sxs-lookup"><span data-stu-id="c5321-351">wwwroot folder</span></span>

<span data-ttu-id="c5321-352">HTML 파일, JavaScript 파일, CSS 파일과 같은 정적 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="c5321-353">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="c5321-354">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="c5321-354">appSettings.json</span></span>

<span data-ttu-id="c5321-355">연결 문자열과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="c5321-356">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="c5321-357">Program.cs</span><span class="sxs-lookup"><span data-stu-id="c5321-357">Program.cs</span></span>

<span data-ttu-id="c5321-358">프로그램의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-358">Contains the entry point for the program.</span></span> <span data-ttu-id="c5321-359">자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="c5321-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="c5321-360">Startup.cs</span></span>

<span data-ttu-id="c5321-361">cookie에 대한 동의 필요 여부 등 앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c5321-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="c5321-362">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c5321-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c5321-363">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c5321-363">Additional resources</span></span>

* [<span data-ttu-id="c5321-364">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="c5321-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="c5321-365">다음 단계</span><span class="sxs-lookup"><span data-stu-id="c5321-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c5321-366">다음: 모델 추가</span><span class="sxs-lookup"><span data-stu-id="c5321-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
