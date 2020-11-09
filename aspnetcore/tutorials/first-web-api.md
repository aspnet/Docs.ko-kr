---
title: '자습서: ASP.NET Core를 사용하여 웹 API 만들기'
author: rick-anderson
description: ASP.NET Core를 사용하여 웹 API를 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
- 'Models'
uid: tutorials/first-web-api
ms.openlocfilehash: fc41dd13e7d027d9630cd596162f9b5fd2ef9e2b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058495"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="0a34c-103">자습서: ASP.NET Core를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="0a34c-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="0a34c-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="0a34c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="0a34c-105">이 자습서에서는 ASP.NET Core를 사용하여 웹 API를 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0a34c-106">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0a34c-107">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-107">Create a web API project.</span></span>
> * <span data-ttu-id="0a34c-108">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0a34c-109">CRUD 메서드로 컨트롤러를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="0a34c-110">라우팅, URL 경로 및 반환 값을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="0a34c-111">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-111">Call the web API with Postman.</span></span>

<span data-ttu-id="0a34c-112">과정을 마치면 웹 API를 통해 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="0a34c-113">개요</span><span class="sxs-lookup"><span data-stu-id="0a34c-113">Overview</span></span>

<span data-ttu-id="0a34c-114">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0a34c-115">API</span><span class="sxs-lookup"><span data-stu-id="0a34c-115">API</span></span> | <span data-ttu-id="0a34c-116">설명</span><span class="sxs-lookup"><span data-stu-id="0a34c-116">Description</span></span> | <span data-ttu-id="0a34c-117">요청 본문</span><span class="sxs-lookup"><span data-stu-id="0a34c-117">Request body</span></span> | <span data-ttu-id="0a34c-118">응답 본문</span><span class="sxs-lookup"><span data-stu-id="0a34c-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="0a34c-119">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="0a34c-119">Get all to-do items</span></span> | <span data-ttu-id="0a34c-120">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-120">None</span></span> | <span data-ttu-id="0a34c-121">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="0a34c-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="0a34c-122">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="0a34c-122">Get an item by ID</span></span> | <span data-ttu-id="0a34c-123">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-123">None</span></span> | <span data-ttu-id="0a34c-124">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="0a34c-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="0a34c-125">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-125">Add a new item</span></span> | <span data-ttu-id="0a34c-126">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="0a34c-126">To-do item</span></span> | <span data-ttu-id="0a34c-127">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="0a34c-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="0a34c-128">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a34c-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0a34c-129">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="0a34c-129">To-do item</span></span> | <span data-ttu-id="0a34c-130">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-130">None</span></span> |
|<span data-ttu-id="0a34c-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a34c-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a34c-132">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a34c-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a34c-133">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-133">None</span></span> | <span data-ttu-id="0a34c-134">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-134">None</span></span>|

<span data-ttu-id="0a34c-135">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-135">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="0a34c-141">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="0a34c-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a34c-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a34c-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-144">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="0a34c-145">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="0a34c-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a34c-147">**파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0a34c-148">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0a34c-149">프로젝트 이름을 *TodoApi* 로 지정하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0a34c-150">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 5.0** 이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="0a34c-151">**API** 템플릿을 선택하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-151">Select the **API** template and click **Create**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a34c-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a34c-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a34c-154">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0a34c-155">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0a34c-156">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="0a34c-157">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="0a34c-158">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="0a34c-158">The preceding commands:</span></span>

  * <span data-ttu-id="0a34c-159">새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="0a34c-160">다음 섹션에서 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-161">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a34c-162">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-162">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0a34c-164">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="0a34c-165">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 템플릿 선택](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="0a34c-167">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 3.x **대상 프레임워크** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="0a34c-168">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-168">Select **Next**.</span></span>

* <span data-ttu-id="0a34c-169">**프로젝트 이름** 으로 *TodoApi* 를 입력한 다음, **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="0a34c-171">프로젝트 폴더에서 명령 터미널을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="0a34c-172">프로젝트 테스트</span><span class="sxs-lookup"><span data-stu-id="0a34c-172">Test the project</span></span>

<span data-ttu-id="0a34c-173">프로젝트 템플릿은 [Swagger](xref:tutorials/web-api-help-pages-using-swagger)를 지원하는 `WeatherForecast` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a34c-175">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="0a34c-176">Visual Studio에서 다음을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-176">Visual Studio launches:</span></span>

* <span data-ttu-id="0a34c-177">IIS Express 웹 서버.</span><span class="sxs-lookup"><span data-stu-id="0a34c-177">The IIS Express web server.</span></span>
* <span data-ttu-id="0a34c-178">기본 브라우저를 시작하고 `https://localhost:<port>/https://localhost:5001/swagger/index.html`로 이동합니다. 여기서 `<port>`는 임의로 선택한 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a34c-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a34c-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="0a34c-180">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a34c-181">브라우저에서 [https://localhost:5001/swagger](https://localhost:5001/swagger) URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-182">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a34c-183">**실행** > **디버깅 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0a34c-184">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0a34c-185">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0a34c-186">`/swagger`를 URL에 추가합니다(URL을 `https://localhost:<port>/swagger`로 변경).</span><span class="sxs-lookup"><span data-stu-id="0a34c-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="0a34c-187">Swagger 페이지 `/swagger/index.html`이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="0a34c-188">**GET** > **사용해 보기** > **실행** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="0a34c-189">페이지에 다음이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-189">The page displays:</span></span>

* <span data-ttu-id="0a34c-190">WeatherForecast API를 테스트할 [Curl](https://curl.haxx.se/) 명령</span><span class="sxs-lookup"><span data-stu-id="0a34c-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="0a34c-191">WeatherForecast API를 테스트할 URL</span><span class="sxs-lookup"><span data-stu-id="0a34c-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="0a34c-192">응답 코드, 본문 및 헤더</span><span class="sxs-lookup"><span data-stu-id="0a34c-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="0a34c-193">미디어 유형과 예제 값 및 스키마가 포함된 드롭다운 목록 상자</span><span class="sxs-lookup"><span data-stu-id="0a34c-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="0a34c-194">Swagger는 웹 API에 유용한 설명서 및 도움말 페이지를 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="0a34c-195">이 자습서에서는 웹 API 만들기에 대해 집중적으로 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="0a34c-196">Swagger에 대한 자세한 내용은 <xref:tutorials/web-api-help-pages-using-swagger>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="0a34c-197">브라우저에서 **요청 URL** `https://localhost:<port>/WeatherForecast`를 복사하여 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="0a34c-198">다음과 비슷한 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-198">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a><span data-ttu-id="0a34c-199">launchUrl 업데이트</span><span class="sxs-lookup"><span data-stu-id="0a34c-199">Update the launchUrl</span></span>

<span data-ttu-id="0a34c-200">*Properties\launchSettings.json* 에서 `launchUrl`을 `"swagger"`에서 `"api/TodoItems"`로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-200">In *Properties\launchSettings.json* , update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="0a34c-201">Swagger가 제거되었기 때문에 위의 태그는 다음 섹션에 추가된 컨트롤러의 GET 메서드로 시작되는 URL을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="0a34c-202">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-202">Add a model class</span></span>

<span data-ttu-id="0a34c-203">*모델* 은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0a34c-204">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a34c-206">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-206">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="0a34c-207">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a34c-208">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="0a34c-209">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a34c-210">클래스 이름을 *TodoItem* 으로 지정하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0a34c-211">템플릿 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a34c-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a34c-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a34c-213">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0a34c-214">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-215">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a34c-216">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-216">Right-click the project.</span></span> <span data-ttu-id="0a34c-217">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a34c-218">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-218">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0a34c-220">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0a34c-221">클래스 이름을 *TodoItem* 으로 지정한 다음, **새로 만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-221">Name the class *TodoItem* , and then click **New**.</span></span>

* <span data-ttu-id="0a34c-222">템플릿 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="0a34c-223">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0a34c-224">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="0a34c-225">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-225">Add a database context</span></span>

<span data-ttu-id="0a34c-226">*데이터베이스 컨텍스트* 는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0a34c-227"><xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="0a34c-229">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-229">Add NuGet packages</span></span>

* <span data-ttu-id="0a34c-230">**도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="0a34c-231">**찾아보기** 탭을 선택하고 \*\*Microsoft.</span><span class="sxs-lookup"><span data-stu-id="0a34c-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="0a34c-232">**EntityFrameworkCore.SqlServer** 를 검색 상자에 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="0a34c-233">5\.0 RC 버전을 사용할 수 있도록 **시험판 포함** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="0a34c-234">왼쪽 창에서 **Microsoft.EntityFrameworkCore.SqlServer** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="0a34c-235">오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-235">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="0a34c-236">앞의 지침을 사용하여 **Microsoft.EntityFrameworkCore.InMemory** NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="0a34c-237">![NuGet 패키지 관리자](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="0a34c-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="0a34c-238">TodoContext 데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="0a34c-239">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-239">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a34c-240">클래스 이름을 *TodoContext* 로 지정하고 **추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-240">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-241">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a34c-242">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-242">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0a34c-243">다음 코드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="0a34c-244">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="0a34c-244">Register the database context</span></span>

<span data-ttu-id="0a34c-245">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0a34c-246">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="0a34c-247">다음 코드로 *Startup.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="0a34c-248">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="0a34c-248">The preceding code:</span></span>

* <span data-ttu-id="0a34c-249">Swagger 호출을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="0a34c-250">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0a34c-251">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0a34c-252">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="0a34c-253">컨트롤러 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="0a34c-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a34c-255">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0a34c-256">**추가** > **스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-256">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0a34c-257">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러** 를 선택하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-257">Select **API Controller with actions, using Entity Framework** , and then select **Add**.</span></span>
* <span data-ttu-id="0a34c-258">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="0a34c-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="0a34c-259">**모델 클래스** 에서 **TodoItem(TodoApi.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-259">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="0a34c-260">**데이터 컨텍스트 클래스** 에서 **TodoContext(TodoApi.Models)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-260">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="0a34c-261">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-261">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-262">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0a34c-263">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="0a34c-264">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="0a34c-264">The preceding commands:</span></span>

* <span data-ttu-id="0a34c-265">스캐폴딩에 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="0a34c-266">스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다</span><span class="sxs-lookup"><span data-stu-id="0a34c-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="0a34c-267">`TodoItemsController`를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="0a34c-268">생성된 코드는:</span><span class="sxs-lookup"><span data-stu-id="0a34c-268">The generated code:</span></span>

* <span data-ttu-id="0a34c-269">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="0a34c-270">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0a34c-271">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0a34c-272">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0a34c-273">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="0a34c-274">ASP.NET Core 템플릿과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="0a34c-275">뷰가 있는 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="0a34c-276">API 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="0a34c-277">`[action]` 토큰이 경로 템플릿에 없는 경우 경로에서 [작업](xref:mvc/controllers/routing#action) 이름이 제외됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="0a34c-278">즉, 일치하는 경로에서 작업과 연결된 메서드 이름이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="0a34c-279">PostTodoItem 만들기 메서드 업데이트</span><span class="sxs-lookup"><span data-stu-id="0a34c-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="0a34c-280">`PostTodoItem`의 return 문이 [nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="0a34c-281">위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="0a34c-282">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0a34c-283">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0a34c-284"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="0a34c-285">성공하면 [HTTP 201 상태 코드](https://developer.mozilla.org/docs/Web/HTTP/Status/201)를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="0a34c-286">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0a34c-287">응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="0a34c-288">`Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="0a34c-289">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0a34c-290">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0a34c-291">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="0a34c-292">Postman을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-292">Install Postman</span></span>

<span data-ttu-id="0a34c-293">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0a34c-294">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="0a34c-295">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-295">Start the web app.</span></span>
* <span data-ttu-id="0a34c-296">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-296">Start Postman.</span></span>
* <span data-ttu-id="0a34c-297">**SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="0a34c-298">**파일** > **설정** ( **일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-298">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="0a34c-299">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="0a34c-300">Postman을 사용하여 PostTodoItem 테스트</span><span class="sxs-lookup"><span data-stu-id="0a34c-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="0a34c-301">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-301">Create a new request.</span></span>
* <span data-ttu-id="0a34c-302">HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0a34c-303">URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0a34c-304">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a34c-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0a34c-305">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="0a34c-306">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0a34c-307">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-307">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0a34c-308">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0a34c-309">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-309">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="0a34c-311">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="0a34c-311">Test the location header URI</span></span>

<span data-ttu-id="0a34c-312">위치 헤더 URI는 브라우저에서 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="0a34c-313">위치 헤더 URI를 복사하여 브라우저에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="0a34c-314">Postman에서 테스트하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-314">To test in Postman:</span></span>

* <span data-ttu-id="0a34c-315">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0a34c-316">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-316">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* <span data-ttu-id="0a34c-318">HTTP 메서드를 `GET`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="0a34c-319">URI를 `https://localhost:<port>/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="0a34c-320">예: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="0a34c-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="0a34c-321">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-321">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="0a34c-322">GET 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="0a34c-322">Examine the GET methods</span></span>

<span data-ttu-id="0a34c-323">두 개의 GET 엔드포인트가 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="0a34c-324">브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="0a34c-325">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="0a34c-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="0a34c-326">`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="0a34c-327">Postman을 사용하여 Get 테스트</span><span class="sxs-lookup"><span data-stu-id="0a34c-327">Test Get with Postman</span></span>

* <span data-ttu-id="0a34c-328">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-328">Create a new request.</span></span>
* <span data-ttu-id="0a34c-329">HTTP 메서드를 **GET** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-329">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="0a34c-330">요청 URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0a34c-331">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a34c-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0a34c-332">Postman에서 **두 개의 창 보기** 를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0a34c-333">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-333">Select **Send**.</span></span>

<span data-ttu-id="0a34c-334">이 앱은 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-334">This app uses an in-memory database.</span></span> <span data-ttu-id="0a34c-335">앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="0a34c-336">데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="0a34c-337">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="0a34c-337">Routing and URL paths</span></span>

<span data-ttu-id="0a34c-338">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0a34c-339">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0a34c-340">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="0a34c-341">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0a34c-342">이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems** Controller이므로 컨트롤러 이름은 “TodoItems”입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-342">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="0a34c-343">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0a34c-344">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0a34c-345">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-345">This sample doesn't use a template.</span></span> <span data-ttu-id="0a34c-346">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0a34c-347">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0a34c-348">`GetTodoItem`이 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="0a34c-349">반환 값</span><span class="sxs-lookup"><span data-stu-id="0a34c-349">Return values</span></span>

<span data-ttu-id="0a34c-350">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0a34c-351">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0a34c-352">이 반환 형식의 응답 코드는 [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200)이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0a34c-353">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0a34c-354">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0a34c-355">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0a34c-356">요청된 ID와 일치하는 항목이 없는 경우 메서드에서 [404 상태](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="0a34c-357">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0a34c-358">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="0a34c-359">PutTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="0a34c-359">The PutTodoItem method</span></span>

<span data-ttu-id="0a34c-360">다음과 같이 `PutTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="0a34c-361">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0a34c-362">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0a34c-363">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0a34c-364">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0a34c-365">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="0a34c-366">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="0a34c-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="0a34c-367">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0a34c-368">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0a34c-369">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0a34c-370">Id = 1인 할 일 항목을 업데이트하고 해당 이름을 `"feed fish"`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0a34c-371">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-371">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="0a34c-373">DeleteTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="0a34c-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="0a34c-374">다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="0a34c-375">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="0a34c-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="0a34c-376">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0a34c-377">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0a34c-378">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="0a34c-379">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-379">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="0a34c-380">과도한 게시 방지</span><span class="sxs-lookup"><span data-stu-id="0a34c-380">Prevent over-posting</span></span>

<span data-ttu-id="0a34c-381">현재 샘플 앱은 전체 `TodoItem` 개체를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="0a34c-382">일반적으로 프로덕션 앱은 모델의 하위 집합을 사용하여 입력 및 반환되는 데이터를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="0a34c-383">이 동작에는 여러 가지 이유가 있으며, 보안이 주요 이유 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="0a34c-384">일반적으로 모델의 하위 집합을 DTO(데이터 전송 개체), 입력 모델 또는 뷰 모델이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="0a34c-385">이 문서에서는 **DTO** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="0a34c-386">DTO는 다음과 같은 용도로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-386">A DTO may be used to:</span></span>

* <span data-ttu-id="0a34c-387">과도한 게시를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-387">Prevent over-posting.</span></span>
* <span data-ttu-id="0a34c-388">클라이언트에 표시되지 않아야 하는 속성을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="0a34c-389">페이로드 크기를 줄이기 위해 일부 속성을 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="0a34c-390">중첩된 개체를 포함하는 개체 그래프를 평면화합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="0a34c-391">클라이언트에는 평면화된 개체 그래프가 더 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="0a34c-392">DTO 방법을 설명하려면 비밀 필드를 포함하도록 `TodoItem` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="0a34c-393">이 앱에서는 숨겨진 필드를 숨겨야 하지만, 관리 앱은 숨겨진 필드를 공개할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="0a34c-394">비밀 필드를 게시하고 가져올 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="0a34c-395">DTO 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="0a34c-396">`TodoItemDTO`를 사용하도록 `TodoItemsController`를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="0a34c-397">비밀 필드를 게시하거나 가져올 수 없음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="0a34c-398">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="0a34c-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="0a34c-399">[자습서: JavaScript로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-javascript)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="0a34c-400">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0a34c-401">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-401">Create a web API project.</span></span>
> * <span data-ttu-id="0a34c-402">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0a34c-403">CRUD 메서드로 컨트롤러를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="0a34c-404">라우팅, URL 경로 및 반환 값을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="0a34c-405">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-405">Call the web API with Postman.</span></span>

<span data-ttu-id="0a34c-406">과정을 마치면 웹 API를 통해 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="0a34c-407">개요</span><span class="sxs-lookup"><span data-stu-id="0a34c-407">Overview</span></span>

<span data-ttu-id="0a34c-408">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0a34c-409">API</span><span class="sxs-lookup"><span data-stu-id="0a34c-409">API</span></span> | <span data-ttu-id="0a34c-410">설명</span><span class="sxs-lookup"><span data-stu-id="0a34c-410">Description</span></span> | <span data-ttu-id="0a34c-411">요청 본문</span><span class="sxs-lookup"><span data-stu-id="0a34c-411">Request body</span></span> | <span data-ttu-id="0a34c-412">응답 본문</span><span class="sxs-lookup"><span data-stu-id="0a34c-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="0a34c-413">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="0a34c-413">Get all to-do items</span></span> | <span data-ttu-id="0a34c-414">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-414">None</span></span> | <span data-ttu-id="0a34c-415">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="0a34c-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="0a34c-416">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="0a34c-416">Get an item by ID</span></span> | <span data-ttu-id="0a34c-417">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-417">None</span></span> | <span data-ttu-id="0a34c-418">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="0a34c-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="0a34c-419">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-419">Add a new item</span></span> | <span data-ttu-id="0a34c-420">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="0a34c-420">To-do item</span></span> | <span data-ttu-id="0a34c-421">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="0a34c-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="0a34c-422">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a34c-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0a34c-423">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="0a34c-423">To-do item</span></span> | <span data-ttu-id="0a34c-424">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-424">None</span></span> |
|<span data-ttu-id="0a34c-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a34c-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a34c-426">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a34c-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a34c-427">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-427">None</span></span> | <span data-ttu-id="0a34c-428">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-428">None</span></span>|

<span data-ttu-id="0a34c-429">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-429">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="0a34c-435">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="0a34c-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a34c-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a34c-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-438">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="0a34c-439">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="0a34c-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a34c-441">**파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-441">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0a34c-442">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-442">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0a34c-443">프로젝트 이름을 *TodoApi* 로 지정하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-443">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0a34c-444">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.1** 이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="0a34c-445">**API** 템플릿을 선택하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-445">Select the **API** template and click **Create**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a34c-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a34c-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a34c-448">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0a34c-449">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0a34c-450">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="0a34c-451">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-451">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="0a34c-452">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="0a34c-452">The preceding commands:</span></span>

  * <span data-ttu-id="0a34c-453">새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="0a34c-454">다음 섹션에서 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-455">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a34c-456">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-456">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0a34c-458">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="0a34c-459">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 템플릿 선택](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="0a34c-461">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 3.x **대상 프레임워크** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="0a34c-462">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-462">Select **Next**.</span></span>

* <span data-ttu-id="0a34c-463">**프로젝트 이름** 으로 *TodoApi* 를 입력한 다음, **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-463">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="0a34c-465">프로젝트 폴더에서 명령 터미널을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="0a34c-466">API 테스트</span><span class="sxs-lookup"><span data-stu-id="0a34c-466">Test the API</span></span>

<span data-ttu-id="0a34c-467">프로젝트 템플릿은 `WeatherForecast` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="0a34c-468">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a34c-470">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a34c-471">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/WeatherForecast`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="0a34c-472">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="0a34c-473">다음으로, **보안 경고** 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-473">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a34c-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a34c-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0a34c-475">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a34c-476">브라우저에서 `https://localhost:5001/WeatherForecast` URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-477">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a34c-478">**실행** > **디버깅 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0a34c-479">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0a34c-480">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0a34c-481">`/WeatherForecast`를 URL에 추가합니다(URL을 `https://localhost:<port>/WeatherForecast`로 변경).</span><span class="sxs-lookup"><span data-stu-id="0a34c-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="0a34c-482">다음과 비슷한 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-482">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="0a34c-483">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-483">Add a model class</span></span>

<span data-ttu-id="0a34c-484">*모델* 은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0a34c-485">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a34c-487">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-487">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="0a34c-488">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-488">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a34c-489">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-489">Name the folder *Models*.</span></span>

* <span data-ttu-id="0a34c-490">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-490">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a34c-491">클래스 이름을 *TodoItem* 으로 지정하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-491">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0a34c-492">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a34c-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a34c-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a34c-494">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-494">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0a34c-495">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-495">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-496">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a34c-497">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-497">Right-click the project.</span></span> <span data-ttu-id="0a34c-498">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-498">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a34c-499">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-499">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0a34c-501">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-501">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0a34c-502">클래스 이름을 *TodoItem* 으로 지정한 다음, **새로 만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-502">Name the class *TodoItem* , and then click **New**.</span></span>

* <span data-ttu-id="0a34c-503">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="0a34c-504">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0a34c-505">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-505">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="0a34c-506">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-506">Add a database context</span></span>

<span data-ttu-id="0a34c-507">*데이터베이스 컨텍스트* 는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0a34c-508">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="0a34c-510">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-510">Add NuGet packages</span></span>

* <span data-ttu-id="0a34c-511">**도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="0a34c-512">**찾아보기** 탭을 선택한 다음 검색 상자에 **Microsoft.EntityFrameworkCore.SqlServer** 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="0a34c-513">왼쪽 창에서 **Microsoft.EntityFrameworkCore.SqlServer** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="0a34c-514">오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-514">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="0a34c-515">앞의 지침을 사용하여 **Microsoft.EntityFrameworkCore.InMemory** NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet 패키지 관리자](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="0a34c-517">TodoContext 데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="0a34c-518">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-518">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a34c-519">클래스 이름을 *TodoContext* 로 지정하고 **추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-519">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-520">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a34c-521">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-521">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0a34c-522">다음 코드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="0a34c-523">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="0a34c-523">Register the database context</span></span>

<span data-ttu-id="0a34c-524">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0a34c-525">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="0a34c-526">*Startup.cs* 를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="0a34c-527">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="0a34c-527">The preceding code:</span></span>

* <span data-ttu-id="0a34c-528">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0a34c-529">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0a34c-530">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="0a34c-531">컨트롤러 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="0a34c-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a34c-533">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0a34c-534">**추가** > **스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-534">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0a34c-535">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러** 를 선택하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-535">Select **API Controller with actions, using Entity Framework** , and then select **Add**.</span></span>
* <span data-ttu-id="0a34c-536">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="0a34c-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="0a34c-537">**모델 클래스** 에서 **TodoItem(TodoApi.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-537">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="0a34c-538">**데이터 컨텍스트 클래스** 에서 **TodoContext(TodoApi.Models)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-538">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="0a34c-539">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-539">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-540">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0a34c-541">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="0a34c-542">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="0a34c-542">The preceding commands:</span></span>

* <span data-ttu-id="0a34c-543">스캐폴딩에 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="0a34c-544">스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다</span><span class="sxs-lookup"><span data-stu-id="0a34c-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="0a34c-545">`TodoItemsController`를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="0a34c-546">생성된 코드는:</span><span class="sxs-lookup"><span data-stu-id="0a34c-546">The generated code:</span></span>

* <span data-ttu-id="0a34c-547">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="0a34c-548">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0a34c-549">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0a34c-550">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0a34c-551">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="0a34c-552">ASP.NET Core 템플릿과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="0a34c-553">뷰가 있는 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="0a34c-554">API 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="0a34c-555">`[action]` 토큰이 경로 템플릿에 없는 경우 경로에서 [작업](xref:mvc/controllers/routing#action) 이름이 제외됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="0a34c-556">즉, 일치하는 경로에서 작업과 연결된 메서드 이름이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="0a34c-557">PostTodoItem 만들기 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="0a34c-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="0a34c-558">`PostTodoItem`의 return 문이 [nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="0a34c-559">위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="0a34c-560">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0a34c-561">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0a34c-562"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="0a34c-563">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="0a34c-564">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0a34c-565">응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="0a34c-566">`Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="0a34c-567">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0a34c-568">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0a34c-569">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="0a34c-570">Postman을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-570">Install Postman</span></span>

<span data-ttu-id="0a34c-571">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0a34c-572">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="0a34c-573">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-573">Start the web app.</span></span>
* <span data-ttu-id="0a34c-574">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-574">Start Postman.</span></span>
* <span data-ttu-id="0a34c-575">**SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="0a34c-576">**파일** > **설정** ( **일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-576">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="0a34c-577">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="0a34c-578">Postman을 사용하여 PostTodoItem 테스트</span><span class="sxs-lookup"><span data-stu-id="0a34c-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="0a34c-579">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-579">Create a new request.</span></span>
* <span data-ttu-id="0a34c-580">HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0a34c-581">URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0a34c-582">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a34c-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0a34c-583">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="0a34c-584">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0a34c-585">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-585">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0a34c-586">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0a34c-587">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-587">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="0a34c-589">Postman을 사용하여 위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="0a34c-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="0a34c-590">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0a34c-591">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-591">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* <span data-ttu-id="0a34c-593">HTTP 메서드를 `GET`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="0a34c-594">URI를 `https://localhost:<port>/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="0a34c-595">예: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="0a34c-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="0a34c-596">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-596">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="0a34c-597">GET 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="0a34c-597">Examine the GET methods</span></span>

<span data-ttu-id="0a34c-598">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="0a34c-599">브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="0a34c-600">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="0a34c-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="0a34c-601">`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="0a34c-602">Postman을 사용하여 Get 테스트</span><span class="sxs-lookup"><span data-stu-id="0a34c-602">Test Get with Postman</span></span>

* <span data-ttu-id="0a34c-603">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-603">Create a new request.</span></span>
* <span data-ttu-id="0a34c-604">HTTP 메서드를 **GET** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-604">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="0a34c-605">요청 URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0a34c-606">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a34c-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0a34c-607">Postman에서 **두 개의 창 보기** 를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0a34c-608">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-608">Select **Send**.</span></span>

<span data-ttu-id="0a34c-609">이 앱은 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-609">This app uses an in-memory database.</span></span> <span data-ttu-id="0a34c-610">앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="0a34c-611">데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="0a34c-612">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="0a34c-612">Routing and URL paths</span></span>

<span data-ttu-id="0a34c-613">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0a34c-614">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0a34c-615">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="0a34c-616">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0a34c-617">이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems** Controller이므로 컨트롤러 이름은 “TodoItems”입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-617">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="0a34c-618">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0a34c-619">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0a34c-620">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-620">This sample doesn't use a template.</span></span> <span data-ttu-id="0a34c-621">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0a34c-622">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0a34c-623">`GetTodoItem`이 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="0a34c-624">반환 값</span><span class="sxs-lookup"><span data-stu-id="0a34c-624">Return values</span></span> 

<span data-ttu-id="0a34c-625">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0a34c-626">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0a34c-627">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0a34c-628">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0a34c-629">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0a34c-630">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0a34c-631">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="0a34c-632">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0a34c-633">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="0a34c-634">PutTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="0a34c-634">The PutTodoItem method</span></span>

<span data-ttu-id="0a34c-635">다음과 같이 `PutTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="0a34c-636">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0a34c-637">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0a34c-638">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0a34c-639">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0a34c-640">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="0a34c-641">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="0a34c-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="0a34c-642">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0a34c-643">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0a34c-644">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0a34c-645">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0a34c-646">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-646">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="0a34c-648">DeleteTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="0a34c-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="0a34c-649">다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="0a34c-650">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="0a34c-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="0a34c-651">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0a34c-652">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0a34c-653">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="0a34c-654">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-654">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="0a34c-655">과도한 게시 방지</span><span class="sxs-lookup"><span data-stu-id="0a34c-655">Prevent over-posting</span></span>

<span data-ttu-id="0a34c-656">현재 샘플 앱은 전체 `TodoItem` 개체를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="0a34c-657">일반적으로 프로덕션 앱은 모델의 하위 집합을 사용하여 입력 및 반환되는 데이터를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="0a34c-658">이 동작에는 여러 가지 이유가 있으며, 보안이 주요 이유 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="0a34c-659">일반적으로 모델의 하위 집합을 DTO(데이터 전송 개체), 입력 모델 또는 뷰 모델이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="0a34c-660">이 문서에서는 **DTO** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="0a34c-661">DTO는 다음과 같은 용도로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-661">A DTO may be used to:</span></span>

* <span data-ttu-id="0a34c-662">과도한 게시를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-662">Prevent over-posting.</span></span>
* <span data-ttu-id="0a34c-663">클라이언트에 표시되지 않아야 하는 속성을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="0a34c-664">페이로드 크기를 줄이기 위해 일부 속성을 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="0a34c-665">중첩된 개체를 포함하는 개체 그래프를 평면화합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="0a34c-666">클라이언트에는 평면화된 개체 그래프가 더 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="0a34c-667">DTO 방법을 설명하려면 비밀 필드를 포함하도록 `TodoItem` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="0a34c-668">이 앱에서는 숨겨진 필드를 숨겨야 하지만, 관리 앱은 숨겨진 필드를 공개할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="0a34c-669">비밀 필드를 게시하고 가져올 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="0a34c-670">DTO 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="0a34c-671">`TodoItemDTO`를 사용하도록 `TodoItemsController`를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="0a34c-672">비밀 필드를 게시하거나 가져올 수 없음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="0a34c-673">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="0a34c-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="0a34c-674">[자습서: JavaScript로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-javascript)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0a34c-675">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0a34c-676">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-676">Create a web API project.</span></span>
> * <span data-ttu-id="0a34c-677">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0a34c-678">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-678">Add a controller.</span></span>
> * <span data-ttu-id="0a34c-679">CRUD 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="0a34c-680">라우팅 및 URL 경로 구성</span><span class="sxs-lookup"><span data-stu-id="0a34c-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="0a34c-681">반환 값 지정</span><span class="sxs-lookup"><span data-stu-id="0a34c-681">Specify return values.</span></span>
> * <span data-ttu-id="0a34c-682">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="0a34c-683">JavaScript를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="0a34c-684">작업을 완료하면 웹 API는 관계형 데이터베이스에 저장된 "할 일" 항목을 관리할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="0a34c-685">개요 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-685">Overview 2.1</span></span>

<span data-ttu-id="0a34c-686">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0a34c-687">API</span><span class="sxs-lookup"><span data-stu-id="0a34c-687">API</span></span> | <span data-ttu-id="0a34c-688">설명</span><span class="sxs-lookup"><span data-stu-id="0a34c-688">Description</span></span> | <span data-ttu-id="0a34c-689">요청 본문</span><span class="sxs-lookup"><span data-stu-id="0a34c-689">Request body</span></span> | <span data-ttu-id="0a34c-690">응답 본문</span><span class="sxs-lookup"><span data-stu-id="0a34c-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="0a34c-691">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0a34c-691">GET /api/TodoItems</span></span> | <span data-ttu-id="0a34c-692">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="0a34c-692">Get all to-do items</span></span> | <span data-ttu-id="0a34c-693">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-693">None</span></span> | <span data-ttu-id="0a34c-694">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="0a34c-694">Array of to-do items</span></span>|
|<span data-ttu-id="0a34c-695">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="0a34c-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="0a34c-696">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="0a34c-696">Get an item by ID</span></span> | <span data-ttu-id="0a34c-697">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-697">None</span></span> | <span data-ttu-id="0a34c-698">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="0a34c-698">To-do item</span></span>|
|<span data-ttu-id="0a34c-699">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0a34c-699">POST /api/TodoItems</span></span> | <span data-ttu-id="0a34c-700">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="0a34c-700">Add a new item</span></span> | <span data-ttu-id="0a34c-701">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="0a34c-701">To-do item</span></span> | <span data-ttu-id="0a34c-702">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="0a34c-702">To-do item</span></span> |
|<span data-ttu-id="0a34c-703">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="0a34c-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="0a34c-704">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a34c-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0a34c-705">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="0a34c-705">To-do item</span></span> | <span data-ttu-id="0a34c-706">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-706">None</span></span> |
|<span data-ttu-id="0a34c-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a34c-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a34c-708">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a34c-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a34c-709">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-709">None</span></span> | <span data-ttu-id="0a34c-710">없음</span><span class="sxs-lookup"><span data-stu-id="0a34c-710">None</span></span>|

<span data-ttu-id="0a34c-711">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-711">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="0a34c-717">필수 구성 요소 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a34c-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a34c-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-720">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="0a34c-721">웹 프로젝트 만들기 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a34c-723">**파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-723">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0a34c-724">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-724">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0a34c-725">프로젝트 이름을 *TodoApi* 로 지정하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-725">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0a34c-726">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 2.2** 가 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="0a34c-727">**API** 템플릿을 선택하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-727">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="0a34c-728">**Docker 지원 사용** 을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="0a34c-728">**Don't** select **Enable Docker Support**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a34c-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a34c-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a34c-731">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0a34c-732">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0a34c-733">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="0a34c-734">이러한 명령은 새 웹 API 프로젝트를 만들고 새 프로젝트 폴더에서 Visual Studio Code의 새 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="0a34c-735">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-735">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-736">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a34c-737">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-737">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0a34c-739">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="0a34c-740">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="0a34c-741">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 2.x **대상 프레임워크** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="0a34c-742">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-742">Select **Next**.</span></span>

* <span data-ttu-id="0a34c-743">**프로젝트 이름** 으로 *TodoApi* 를 입력한 다음, **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-743">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="0a34c-745">API 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-745">Test the API 2.1</span></span>

<span data-ttu-id="0a34c-746">프로젝트 템플릿은 `values` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-746">The project template creates a `values` API.</span></span> <span data-ttu-id="0a34c-747">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a34c-749">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a34c-750">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/api/values`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="0a34c-751">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="0a34c-752">다음으로, **보안 경고** 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-752">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a34c-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a34c-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0a34c-754">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a34c-755">브라우저에서 `https://localhost:5001/api/values` URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-756">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a34c-757">**실행** > **디버깅 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0a34c-758">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0a34c-759">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0a34c-760">`/api/values`를 URL에 추가합니다(URL을 `https://localhost:<port>/api/values`로 변경).</span><span class="sxs-lookup"><span data-stu-id="0a34c-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="0a34c-761">다음 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="0a34c-762">모델 클래스 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-762">Add a model class 2.1</span></span>

<span data-ttu-id="0a34c-763">*모델* 은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0a34c-764">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a34c-766">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-766">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="0a34c-767">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-767">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a34c-768">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-768">Name the folder *Models*.</span></span>

* <span data-ttu-id="0a34c-769">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-769">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a34c-770">클래스 이름을 *TodoItem* 으로 지정하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-770">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0a34c-771">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a34c-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a34c-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a34c-773">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-773">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0a34c-774">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-774">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-775">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a34c-776">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-776">Right-click the project.</span></span> <span data-ttu-id="0a34c-777">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-777">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a34c-778">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-778">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0a34c-780">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-780">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0a34c-781">클래스 이름을 *TodoItem* 으로 지정한 다음, **새로 만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-781">Name the class *TodoItem* , and then click **New**.</span></span>

* <span data-ttu-id="0a34c-782">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="0a34c-783">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0a34c-784">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-784">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="0a34c-785">데이터베이스 컨텍스트 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-785">Add a database context 2.1</span></span>

<span data-ttu-id="0a34c-786">*데이터베이스 컨텍스트* 는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0a34c-787">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a34c-789">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-789">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a34c-790">클래스 이름을 *TodoContext* 로 지정하고 **추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-790">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-791">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a34c-792">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-792">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0a34c-793">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="0a34c-794">데이터베이스 컨텍스트 등록 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-794">Register the database context 2.1</span></span>

<span data-ttu-id="0a34c-795">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0a34c-796">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="0a34c-797">*Startup.cs* 를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="0a34c-798">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="0a34c-798">The preceding code:</span></span>

* <span data-ttu-id="0a34c-799">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0a34c-800">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0a34c-801">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="0a34c-802">컨트롤러 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a34c-804">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0a34c-805">**추가** > **새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-805">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="0a34c-806">**새 항목 추가** 대화 상자에서 **API 컨트롤러 클래스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="0a34c-807">클래스 이름을 *TodoController* 로 지정하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-807">Name the class *TodoController* , and select **Add**.</span></span>

  ![검색 상자의 컨트롤러 및 웹 API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-809">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a34c-810">*Controllers* 폴더에 `TodoController`라는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="0a34c-811">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="0a34c-812">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="0a34c-812">The preceding code:</span></span>

* <span data-ttu-id="0a34c-813">메서드 없이 API 컨트롤러 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="0a34c-814">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="0a34c-815">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0a34c-816">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0a34c-817">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0a34c-818">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="0a34c-819">데이터베이스가 비어 있는 경우 데이터베이스에 `Item1`이라는 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="0a34c-820">이 코드는 생성자에 위치하므로 새 HTTP 요청이 발생할 때마다 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="0a34c-821">모든 항목을 삭제하면 생성자는 다음에 API가 호출될 경우 `Item1`을 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="0a34c-822">따라서 실제로 작동되는 경우 삭제가 작동하지 않는 것처럼 보일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="0a34c-823">GET 메서드 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-823">Add Get methods 2.1</span></span>

<span data-ttu-id="0a34c-824">할 일 항목을 가져오는 API를 제공하려면 다음 메서드를 `TodoController` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="0a34c-825">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="0a34c-826">앱이 계속 실행되고 있으면 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-826">Stop the app if it's still running.</span></span> <span data-ttu-id="0a34c-827">그런 다음, 다시 실행하여 최신 변경 내용을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="0a34c-828">브라우저에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="0a34c-829">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="0a34c-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="0a34c-830">`GetTodoItems`를 호출하여 다음 HTTP 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="0a34c-831">라우팅 및 URL 경로 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="0a34c-832">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0a34c-833">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0a34c-834">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="0a34c-835">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0a34c-836">이 샘플의 경우 컨트롤러 클래스 이름은 **Todo** Controller이므로 컨트롤러 이름은 "todo"입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-836">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="0a34c-837">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0a34c-838">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0a34c-839">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-839">This sample doesn't use a template.</span></span> <span data-ttu-id="0a34c-840">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0a34c-841">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0a34c-842">`GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="0a34c-843">반환 값 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-843">Return values 2.1</span></span>

<span data-ttu-id="0a34c-844">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0a34c-845">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0a34c-846">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0a34c-847">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0a34c-848">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0a34c-849">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0a34c-850">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="0a34c-851">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0a34c-852">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="0a34c-853">GetTodoItems 메서드 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="0a34c-854">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0a34c-855">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="0a34c-856">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-856">Start the web app.</span></span>
* <span data-ttu-id="0a34c-857">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-857">Start Postman.</span></span>
* <span data-ttu-id="0a34c-858">**SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-858">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a34c-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a34c-860">**파일** > **설정** ( **일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-860">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a34c-861">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a34c-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a34c-862">**Postman** > **기본 설정** ( **일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-862">From **Postman** > **Preferences** ( **General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="0a34c-863">또는 렌치를 선택하고 **설정** 을 선택한 다음 SSL 인증서 확인을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-863">Alternatively, select the wrench and select **Settings** , then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="0a34c-864">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="0a34c-865">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-865">Create a new request.</span></span>
  * <span data-ttu-id="0a34c-866">HTTP 메서드를 **GET** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-866">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="0a34c-867">요청 URI를 `https://localhost:<port>/api/todo`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="0a34c-868">예: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="0a34c-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="0a34c-869">Postman에서 **두 개의 창 보기** 를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0a34c-870">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-870">Select **Send**.</span></span>

![Get 요청이 있는 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="0a34c-872">Create 메서드 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-872">Add a Create method 2.1</span></span>

<span data-ttu-id="0a34c-873">*Controllers/TodoController.cs* 내부에 다음 `PostTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs* :</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="0a34c-874">위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="0a34c-875">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0a34c-876">`CreatedAtAction` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="0a34c-877">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="0a34c-878">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0a34c-879">`Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="0a34c-880">`Location` 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="0a34c-881">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0a34c-882">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0a34c-883">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="0a34c-884">PostTodoItem 메서드 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="0a34c-885">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-885">Build the project.</span></span>
* <span data-ttu-id="0a34c-886">Postman에서 HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0a34c-887">URI를 `https://localhost:<port>/api/TodoItem`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-887">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="0a34c-888">예: `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="0a34c-888">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="0a34c-889">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="0a34c-890">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0a34c-891">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-891">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0a34c-892">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0a34c-893">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-893">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="0a34c-895">405 메서드가 허용되지 않음 오류가 발생할 경우 `PostTodoItem` 메서드를 추가한 후에 프로젝트를 컴파일하지 않아서 발생한 결과일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="0a34c-896">위치 헤더 URI 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="0a34c-897">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0a34c-898">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-898">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/pmc2.png)

* <span data-ttu-id="0a34c-900">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-900">Set the method to GET.</span></span>
* <span data-ttu-id="0a34c-901">URI를 `https://localhost:<port>/api/TodoItems/2`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-901">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="0a34c-902">예: `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="0a34c-902">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="0a34c-903">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-903">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="0a34c-904">PutTodoItem 메서드 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="0a34c-905">다음 `PutTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="0a34c-906">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0a34c-907">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0a34c-908">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0a34c-909">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0a34c-910">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="0a34c-911">PutTodoItem 메서드 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="0a34c-912">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0a34c-913">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0a34c-914">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0a34c-915">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0a34c-916">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-916">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="0a34c-918">DeleteTodoItem 메서드 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="0a34c-919">다음 `DeleteTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="0a34c-920">`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="0a34c-921">DeleteTodoItem 메서드 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="0a34c-922">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0a34c-923">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0a34c-924">삭제할 개체의 URI를 설정합니다(예: `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="0a34c-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="0a34c-925">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-925">Select **Send**.</span></span>

<span data-ttu-id="0a34c-926">샘플 앱을 사용하면 모든 항목을 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="0a34c-927">하지만 마지막 항목이 삭제되면 다음에 API를 호출하는 경우 모델 클래스 생성자에서 새로운 항목이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="0a34c-928">JavaScript를 사용하여 웹 API 호출 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="0a34c-929">이 섹션에는 JavaScript를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="0a34c-930">jQuery가 요청을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-930">jQuery initiates the request.</span></span> <span data-ttu-id="0a34c-931">JavaScript는 웹 API 응답의 세부 정보를 토대로 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="0a34c-932">다음 강조 표시된 코드로 *Startup.cs* 를 업데이트하여 앱이 [정적 파일을 제공](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)하고 [기본 파일 매핑을 사용](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="0a34c-933">프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="0a34c-934">*index.html* 이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="0a34c-935">다음 표시로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="0a34c-936">*site.js* 라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="0a34c-937">다음 코드로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="0a34c-938">HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="0a34c-939">*Properties\launchSettings.json* 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-939">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="0a34c-940">`launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="0a34c-941">이 샘플은 웹 API의 CRUD 메서드를 모두 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="0a34c-942">API 호출에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="0a34c-943">할 일 항목의 목록 가져오기 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="0a34c-944">jQuery는 할 일 항목의 배열을 나타내는 JSON을 반환하는 웹 API에 HTTP GET 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="0a34c-945">요청이 성공하면 `success` 콜백 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="0a34c-946">콜백에서 DOM은 할 일 정보로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="0a34c-947">할 일 항목 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="0a34c-948">jQuery는 요청 본문에 있는 할 일 항목을 사용하여 HTTP POST 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="0a34c-949">`accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="0a34c-950">[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="0a34c-951">API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="0a34c-952">할 일 항목 업데이트 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="0a34c-953">할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="0a34c-954">`url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="0a34c-955">할 일 항목 삭제 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="0a34c-956">할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a34c-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="0a34c-957">웹 API에 인증 지원 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="0a34c-958">추가 리소스 2.1</span><span class="sxs-lookup"><span data-stu-id="0a34c-958">Additional resources 2.1</span></span>

<span data-ttu-id="0a34c-959">[이 자습서에서 샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="0a34c-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="0a34c-960">[다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="0a34c-961">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0a34c-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="0a34c-962">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="0a34c-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
