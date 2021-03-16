---
title: '자습서: ASP.NET Core를 사용하여 웹 API 만들기'
author: rick-anderson
description: ASP.NET Core를 사용하여 웹 API를 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 02/04/2021
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 789cd1a867bc8c17401bbac5c02951b4bd2999b6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587660"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="539ca-103">자습서: ASP.NET Core를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="539ca-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="539ca-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="539ca-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="539ca-105">이 자습서에서는 ASP.NET Core를 사용하여 웹 API를 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="539ca-106">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="539ca-107">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-107">Create a web API project.</span></span>
> * <span data-ttu-id="539ca-108">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="539ca-109">CRUD 메서드로 컨트롤러를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="539ca-110">라우팅, URL 경로 및 반환 값을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="539ca-111">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-111">Call the web API with Postman.</span></span>

<span data-ttu-id="539ca-112">과정을 마치면 웹 API를 통해 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="539ca-113">개요</span><span class="sxs-lookup"><span data-stu-id="539ca-113">Overview</span></span>

<span data-ttu-id="539ca-114">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="539ca-115">API</span><span class="sxs-lookup"><span data-stu-id="539ca-115">API</span></span> | <span data-ttu-id="539ca-116">설명</span><span class="sxs-lookup"><span data-stu-id="539ca-116">Description</span></span> | <span data-ttu-id="539ca-117">요청 본문</span><span class="sxs-lookup"><span data-stu-id="539ca-117">Request body</span></span> | <span data-ttu-id="539ca-118">응답 본문</span><span class="sxs-lookup"><span data-stu-id="539ca-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="539ca-119">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="539ca-119">Get all to-do items</span></span> | <span data-ttu-id="539ca-120">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-120">None</span></span> | <span data-ttu-id="539ca-121">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="539ca-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="539ca-122">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="539ca-122">Get an item by ID</span></span> | <span data-ttu-id="539ca-123">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-123">None</span></span> | <span data-ttu-id="539ca-124">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="539ca-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="539ca-125">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-125">Add a new item</span></span> | <span data-ttu-id="539ca-126">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="539ca-126">To-do item</span></span> | <span data-ttu-id="539ca-127">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="539ca-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="539ca-128">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="539ca-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="539ca-129">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="539ca-129">To-do item</span></span> | <span data-ttu-id="539ca-130">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-130">None</span></span> |
|<span data-ttu-id="539ca-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="539ca-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="539ca-132">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="539ca-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="539ca-133">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-133">None</span></span> | <span data-ttu-id="539ca-134">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-134">None</span></span>|

<span data-ttu-id="539ca-135">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-135">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="539ca-141">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="539ca-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="539ca-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="539ca-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="539ca-144">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="539ca-145">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="539ca-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="539ca-147">**파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="539ca-148">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="539ca-149">프로젝트 이름을 *TodoApi* 로 지정하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="539ca-150">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 5.0** 이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="539ca-151">**API** 템플릿을 선택하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-151">Select the **API** template and click **Create**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="539ca-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="539ca-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="539ca-154">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="539ca-155">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="539ca-156">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="539ca-157">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="539ca-158">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="539ca-158">The preceding commands:</span></span>

  * <span data-ttu-id="539ca-159">새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="539ca-160">다음 섹션에서 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="539ca-161">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="539ca-162">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-162">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="539ca-164">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="539ca-165">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 템플릿 선택](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="539ca-167">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 5.x **대상 프레임워크** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="539ca-168">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-168">Select **Next**.</span></span>

* <span data-ttu-id="539ca-169">**프로젝트 이름** 으로 *TodoApi* 를 입력한 다음, **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="539ca-171">프로젝트 폴더에서 명령 터미널을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-171">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="539ca-172">프로젝트 테스트</span><span class="sxs-lookup"><span data-stu-id="539ca-172">Test the project</span></span>

<span data-ttu-id="539ca-173">프로젝트 템플릿은 [Swagger](xref:tutorials/web-api-help-pages-using-swagger)를 지원하는 `WeatherForecast` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="539ca-175">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="539ca-176">Visual Studio에서 다음을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-176">Visual Studio launches:</span></span>

* <span data-ttu-id="539ca-177">IIS Express 웹 서버.</span><span class="sxs-lookup"><span data-stu-id="539ca-177">The IIS Express web server.</span></span>
* <span data-ttu-id="539ca-178">기본 브라우저를 시작하고 `https://localhost:<port>/swagger/index.html`로 이동합니다. 여기서 `<port>`는 임의로 선택한 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="539ca-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="539ca-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="539ca-180">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="539ca-181">브라우저에서 [https://localhost:5001/swagger](https://localhost:5001/swagger) URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="539ca-182">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="539ca-183">**실행** > **디버깅 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="539ca-184">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="539ca-185">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="539ca-186">`/swagger`를 URL에 추가합니다(URL을 `https://localhost:<port>/swagger`로 변경).</span><span class="sxs-lookup"><span data-stu-id="539ca-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="539ca-187">Swagger 페이지 `/swagger/index.html`이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="539ca-188">**GET** > **사용해 보기** > **실행** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="539ca-189">페이지에 다음이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-189">The page displays:</span></span>

* <span data-ttu-id="539ca-190">WeatherForecast API를 테스트할 [Curl](https://curl.haxx.se/) 명령</span><span class="sxs-lookup"><span data-stu-id="539ca-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="539ca-191">WeatherForecast API를 테스트할 URL</span><span class="sxs-lookup"><span data-stu-id="539ca-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="539ca-192">응답 코드, 본문 및 헤더</span><span class="sxs-lookup"><span data-stu-id="539ca-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="539ca-193">미디어 유형과 예제 값 및 스키마가 포함된 드롭다운 목록 상자</span><span class="sxs-lookup"><span data-stu-id="539ca-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="539ca-194">Swagger는 웹 API에 유용한 설명서 및 도움말 페이지를 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="539ca-195">이 자습서에서는 웹 API 만들기에 대해 집중적으로 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="539ca-196">Swagger에 대한 자세한 내용은 <xref:tutorials/web-api-help-pages-using-swagger>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="539ca-197">브라우저에서 **요청 URL** `https://localhost:<port>/WeatherForecast`를 복사하여 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="539ca-198">다음과 비슷한 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="539ca-199">launchUrl 업데이트</span><span class="sxs-lookup"><span data-stu-id="539ca-199">Update the launchUrl</span></span>

<span data-ttu-id="539ca-200">*Properties\launchSettings.json* 에서 `launchUrl`을 `"swagger"`에서 `"api/TodoItems"`로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="539ca-201">Swagger가 제거되었기 때문에 위의 태그는 다음 섹션에 추가된 컨트롤러의 GET 메서드로 시작되는 URL을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="539ca-202">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-202">Add a model class</span></span>

<span data-ttu-id="539ca-203">*모델* 은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="539ca-204">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="539ca-206">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="539ca-207">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="539ca-208">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="539ca-209">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="539ca-210">클래스 이름을 *TodoItem* 으로 지정하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="539ca-211">템플릿 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="539ca-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="539ca-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="539ca-213">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="539ca-214">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="539ca-215">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="539ca-216">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-216">Right-click the project.</span></span> <span data-ttu-id="539ca-217">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="539ca-218">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-218">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="539ca-220">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="539ca-221">클래스 이름을 *TodoItem* 으로 지정한 다음, **새로 만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="539ca-222">템플릿 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="539ca-223">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="539ca-224">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="539ca-225">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-225">Add a database context</span></span>

<span data-ttu-id="539ca-226">*데이터베이스 컨텍스트* 는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="539ca-227"><xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="539ca-229">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-229">Add NuGet packages</span></span>

* <span data-ttu-id="539ca-230">**도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="539ca-231">**찾아보기** 탭을 선택한 다음, 검색 상자에 `Microsoft.EntityFrameworkCore.InMemory`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-231">Select the **Browse** tab, and then enter `Microsoft.EntityFrameworkCore.InMemory` in the search box.</span></span>
* <span data-ttu-id="539ca-232">왼쪽 창에서 `Microsoft.EntityFrameworkCore.InMemory`를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-232">Select `Microsoft.EntityFrameworkCore.InMemory` in the left pane.</span></span>
* <span data-ttu-id="539ca-233">오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet 패키지 관리자](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="539ca-235">TodoContext 데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-235">Add the TodoContext database context</span></span>

* <span data-ttu-id="539ca-236">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-236">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="539ca-237">클래스 이름을 *TodoContext* 로 지정하고 **추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-237">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="539ca-238">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="539ca-239">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-239">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="539ca-240">다음 코드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-240">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="539ca-241">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="539ca-241">Register the database context</span></span>

<span data-ttu-id="539ca-242">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-242">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="539ca-243">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-243">The container provides the service to controllers.</span></span>

<span data-ttu-id="539ca-244">다음 코드로 *Startup.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-244">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="539ca-245">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="539ca-245">The preceding code:</span></span>

* <span data-ttu-id="539ca-246">Swagger 호출을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-246">Removes the Swagger calls.</span></span>
* <span data-ttu-id="539ca-247">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-247">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="539ca-248">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-248">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="539ca-249">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-249">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="539ca-250">컨트롤러 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="539ca-250">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="539ca-252">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-252">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="539ca-253">**추가** > **스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-253">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="539ca-254">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러** 를 선택하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-254">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="539ca-255">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="539ca-255">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="539ca-256">**모델 클래스** 에서 **TodoItem(TodoApi.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-256">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="539ca-257">**데이터 컨텍스트 클래스** 에서 **TodoContext(TodoApi.Models)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-257">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="539ca-258">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-258">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="539ca-259">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="539ca-260">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-260">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="539ca-261">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="539ca-261">The preceding commands:</span></span>

* <span data-ttu-id="539ca-262">스캐폴딩에 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-262">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="539ca-263">스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다</span><span class="sxs-lookup"><span data-stu-id="539ca-263">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="539ca-264">`TodoItemsController`를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-264">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="539ca-265">생성된 코드는:</span><span class="sxs-lookup"><span data-stu-id="539ca-265">The generated code:</span></span>

* <span data-ttu-id="539ca-266">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-266">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="539ca-267">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-267">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="539ca-268">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-268">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="539ca-269">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-269">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="539ca-270">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-270">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="539ca-271">ASP.NET Core 템플릿과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-271">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="539ca-272">뷰가 있는 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-272">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="539ca-273">API 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-273">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="539ca-274">`[action]` 토큰이 경로 템플릿에 없는 경우 경로에서 [작업](xref:mvc/controllers/routing#action) 이름이 제외됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-274">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="539ca-275">즉, 일치하는 경로에서 작업과 연결된 메서드 이름이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-275">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="539ca-276">PostTodoItem 만들기 메서드 업데이트</span><span class="sxs-lookup"><span data-stu-id="539ca-276">Update the PostTodoItem create method</span></span>

<span data-ttu-id="539ca-277">[nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 `PostTodoItem`의 return 문을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-277">Update the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="539ca-278">위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-278">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="539ca-279">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-279">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="539ca-280">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-280">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="539ca-281"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-281">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="539ca-282">성공하면 [HTTP 201 상태 코드](https://developer.mozilla.org/docs/Web/HTTP/Status/201)를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-282">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="539ca-283">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-283">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="539ca-284">응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-284">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="539ca-285">`Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-285">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="539ca-286">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-286">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="539ca-287">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-287">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="539ca-288">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-288">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="539ca-289">Postman을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-289">Install Postman</span></span>

<span data-ttu-id="539ca-290">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-290">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="539ca-291">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-291">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="539ca-292">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-292">Start the web app.</span></span>
* <span data-ttu-id="539ca-293">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-293">Start Postman.</span></span>
* <span data-ttu-id="539ca-294">**SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-294">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="539ca-295">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-295">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="539ca-296">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-296">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="539ca-297">Postman을 사용하여 PostTodoItem 테스트</span><span class="sxs-lookup"><span data-stu-id="539ca-297">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="539ca-298">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-298">Create a new request.</span></span>
* <span data-ttu-id="539ca-299">HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-299">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="539ca-300">URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-300">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="539ca-301">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="539ca-301">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="539ca-302">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-302">Select the **Body** tab.</span></span>
* <span data-ttu-id="539ca-303">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-303">Select the **raw** radio button.</span></span>
* <span data-ttu-id="539ca-304">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-304">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="539ca-305">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-305">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="539ca-306">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-306">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="539ca-308">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="539ca-308">Test the location header URI</span></span>

<span data-ttu-id="539ca-309">위치 헤더 URI는 브라우저에서 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-309">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="539ca-310">위치 헤더 URI를 복사하여 브라우저에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-310">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="539ca-311">Postman에서 테스트하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-311">To test in Postman:</span></span>

* <span data-ttu-id="539ca-312">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-312">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="539ca-313">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-313">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* <span data-ttu-id="539ca-315">HTTP 메서드를 `GET`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-315">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="539ca-316">URI를 `https://localhost:<port>/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-316">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="539ca-317">예: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="539ca-317">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="539ca-318">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-318">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="539ca-319">GET 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="539ca-319">Examine the GET methods</span></span>

<span data-ttu-id="539ca-320">두 개의 GET 엔드포인트가 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-320">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="539ca-321">브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-321">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="539ca-322">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="539ca-322">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="539ca-323">`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-323">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="539ca-324">Postman을 사용하여 Get 테스트</span><span class="sxs-lookup"><span data-stu-id="539ca-324">Test Get with Postman</span></span>

* <span data-ttu-id="539ca-325">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-325">Create a new request.</span></span>
* <span data-ttu-id="539ca-326">HTTP 메서드를 **GET** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-326">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="539ca-327">요청 URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-327">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="539ca-328">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="539ca-328">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="539ca-329">Postman에서 **두 개의 창 보기** 를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-329">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="539ca-330">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-330">Select **Send**.</span></span>

<span data-ttu-id="539ca-331">이 앱은 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-331">This app uses an in-memory database.</span></span> <span data-ttu-id="539ca-332">앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-332">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="539ca-333">데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-333">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="539ca-334">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="539ca-334">Routing and URL paths</span></span>

<span data-ttu-id="539ca-335">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-335">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="539ca-336">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-336">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="539ca-337">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-337">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="539ca-338">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-338">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="539ca-339">이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems** Controller이므로 컨트롤러 이름은 “TodoItems”입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-339">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="539ca-340">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-340">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="539ca-341">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-341">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="539ca-342">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-342">This sample doesn't use a template.</span></span> <span data-ttu-id="539ca-343">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-343">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="539ca-344">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-344">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="539ca-345">`GetTodoItem`이 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-345">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="539ca-346">반환 값</span><span class="sxs-lookup"><span data-stu-id="539ca-346">Return values</span></span>

<span data-ttu-id="539ca-347">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-347">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="539ca-348">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-348">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="539ca-349">이 반환 형식의 응답 코드는 [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200)이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-349">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="539ca-350">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-350">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="539ca-351">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-351">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="539ca-352">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-352">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="539ca-353">요청된 ID와 일치하는 항목이 없는 경우 메서드에서 [404 상태](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-353">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="539ca-354">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-354">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="539ca-355">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-355">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="539ca-356">PutTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="539ca-356">The PutTodoItem method</span></span>

<span data-ttu-id="539ca-357">다음과 같이 `PutTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-357">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="539ca-358">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-358">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="539ca-359">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-359">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="539ca-360">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-360">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="539ca-361">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-361">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="539ca-362">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-362">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="539ca-363">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="539ca-363">Test the PutTodoItem method</span></span>

<span data-ttu-id="539ca-364">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-364">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="539ca-365">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-365">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="539ca-366">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-366">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="539ca-367">Id = 1인 할 일 항목을 업데이트하고 해당 이름을 `"feed fish"`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-367">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="539ca-368">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-368">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="539ca-370">DeleteTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="539ca-370">The DeleteTodoItem method</span></span>

<span data-ttu-id="539ca-371">다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-371">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="539ca-372">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="539ca-372">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="539ca-373">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-373">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="539ca-374">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-374">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="539ca-375">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-375">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="539ca-376">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-376">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="539ca-377">과도한 게시 방지</span><span class="sxs-lookup"><span data-stu-id="539ca-377">Prevent over-posting</span></span>

<span data-ttu-id="539ca-378">현재 샘플 앱은 전체 `TodoItem` 개체를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-378">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="539ca-379">일반적으로 프로덕션 앱은 모델의 하위 집합을 사용하여 입력 및 반환되는 데이터를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-379">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="539ca-380">이 동작에는 여러 가지 이유가 있으며, 보안이 주요 이유 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-380">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="539ca-381">일반적으로 모델의 하위 집합을 DTO(데이터 전송 개체), 입력 모델 또는 뷰 모델이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-381">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="539ca-382">이 문서에서는 **DTO** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-382">**DTO** is used in this article.</span></span>

<span data-ttu-id="539ca-383">DTO는 다음과 같은 용도로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-383">A DTO may be used to:</span></span>

* <span data-ttu-id="539ca-384">과도한 게시를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-384">Prevent over-posting.</span></span>
* <span data-ttu-id="539ca-385">클라이언트에 표시되지 않아야 하는 속성을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-385">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="539ca-386">페이로드 크기를 줄이기 위해 일부 속성을 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-386">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="539ca-387">중첩된 개체를 포함하는 개체 그래프를 평면화합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-387">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="539ca-388">클라이언트에는 평면화된 개체 그래프가 더 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-388">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="539ca-389">DTO 방법을 설명하려면 비밀 필드를 포함하도록 `TodoItem` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-389">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="539ca-390">이 앱에서는 숨겨진 필드를 숨겨야 하지만, 관리 앱은 숨겨진 필드를 공개할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-390">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="539ca-391">비밀 필드를 게시하고 가져올 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-391">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="539ca-392">DTO 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-392">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="539ca-393">`TodoItemDTO`를 사용하도록 `TodoItemsController`를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-393">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="539ca-394">비밀 필드를 게시하거나 가져올 수 없음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-394">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="539ca-395">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="539ca-395">Call the web API with JavaScript</span></span>

<span data-ttu-id="539ca-396">[자습서: JavaScript로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-javascript)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-396">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="539ca-397">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-397">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="539ca-398">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-398">Create a web API project.</span></span>
> * <span data-ttu-id="539ca-399">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-399">Add a model class and a database context.</span></span>
> * <span data-ttu-id="539ca-400">CRUD 메서드로 컨트롤러를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-400">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="539ca-401">라우팅, URL 경로 및 반환 값을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-401">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="539ca-402">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-402">Call the web API with Postman.</span></span>

<span data-ttu-id="539ca-403">과정을 마치면 웹 API를 통해 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-403">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="539ca-404">개요</span><span class="sxs-lookup"><span data-stu-id="539ca-404">Overview</span></span>

<span data-ttu-id="539ca-405">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-405">This tutorial creates the following API:</span></span>

|<span data-ttu-id="539ca-406">API</span><span class="sxs-lookup"><span data-stu-id="539ca-406">API</span></span> | <span data-ttu-id="539ca-407">설명</span><span class="sxs-lookup"><span data-stu-id="539ca-407">Description</span></span> | <span data-ttu-id="539ca-408">요청 본문</span><span class="sxs-lookup"><span data-stu-id="539ca-408">Request body</span></span> | <span data-ttu-id="539ca-409">응답 본문</span><span class="sxs-lookup"><span data-stu-id="539ca-409">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="539ca-410">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="539ca-410">Get all to-do items</span></span> | <span data-ttu-id="539ca-411">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-411">None</span></span> | <span data-ttu-id="539ca-412">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="539ca-412">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="539ca-413">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="539ca-413">Get an item by ID</span></span> | <span data-ttu-id="539ca-414">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-414">None</span></span> | <span data-ttu-id="539ca-415">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="539ca-415">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="539ca-416">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-416">Add a new item</span></span> | <span data-ttu-id="539ca-417">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="539ca-417">To-do item</span></span> | <span data-ttu-id="539ca-418">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="539ca-418">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="539ca-419">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="539ca-419">Update an existing item &nbsp;</span></span> | <span data-ttu-id="539ca-420">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="539ca-420">To-do item</span></span> | <span data-ttu-id="539ca-421">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-421">None</span></span> |
|<span data-ttu-id="539ca-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="539ca-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="539ca-423">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="539ca-423">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="539ca-424">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-424">None</span></span> | <span data-ttu-id="539ca-425">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-425">None</span></span>|

<span data-ttu-id="539ca-426">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-426">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="539ca-432">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="539ca-432">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="539ca-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="539ca-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="539ca-435">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="539ca-436">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="539ca-436">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-437">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="539ca-438">**파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-438">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="539ca-439">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-439">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="539ca-440">프로젝트 이름을 *TodoApi* 로 지정하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-440">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="539ca-441">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.1** 이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-441">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="539ca-442">**API** 템플릿을 선택하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-442">Select the **API** template and click **Create**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="539ca-444">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="539ca-444">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="539ca-445">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-445">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="539ca-446">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-446">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="539ca-447">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-447">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="539ca-448">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-448">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="539ca-449">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="539ca-449">The preceding commands:</span></span>

  * <span data-ttu-id="539ca-450">새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-450">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="539ca-451">다음 섹션에서 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-451">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="539ca-452">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-452">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="539ca-453">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-453">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="539ca-455">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-455">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="539ca-456">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-456">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 템플릿 선택](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="539ca-458">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 3.x **대상 프레임워크** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-458">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="539ca-459">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-459">Select **Next**.</span></span>

* <span data-ttu-id="539ca-460">**프로젝트 이름** 으로 *TodoApi* 를 입력한 다음, **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-460">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="539ca-462">프로젝트 폴더에서 명령 터미널을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-462">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="539ca-463">API 테스트</span><span class="sxs-lookup"><span data-stu-id="539ca-463">Test the API</span></span>

<span data-ttu-id="539ca-464">프로젝트 템플릿은 `WeatherForecast` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-464">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="539ca-465">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-465">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-466">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-466">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="539ca-467">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-467">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="539ca-468">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/WeatherForecast`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-468">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="539ca-469">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-469">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="539ca-470">다음으로, **보안 경고** 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-470">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="539ca-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="539ca-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="539ca-472">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-472">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="539ca-473">브라우저에서 `https://localhost:5001/WeatherForecast` URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-473">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="539ca-474">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="539ca-475">**실행** > **디버깅 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-475">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="539ca-476">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-476">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="539ca-477">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-477">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="539ca-478">`/WeatherForecast`를 URL에 추가합니다(URL을 `https://localhost:<port>/WeatherForecast`로 변경).</span><span class="sxs-lookup"><span data-stu-id="539ca-478">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="539ca-479">다음과 비슷한 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-479">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="539ca-480">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-480">Add a model class</span></span>

<span data-ttu-id="539ca-481">*모델* 은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-481">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="539ca-482">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-482">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="539ca-484">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-484">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="539ca-485">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-485">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="539ca-486">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-486">Name the folder *Models*.</span></span>

* <span data-ttu-id="539ca-487">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-487">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="539ca-488">클래스 이름을 *TodoItem* 으로 지정하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-488">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="539ca-489">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-489">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="539ca-490">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="539ca-490">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="539ca-491">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-491">Add a folder named *Models*.</span></span>

* <span data-ttu-id="539ca-492">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-492">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="539ca-493">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-493">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="539ca-494">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-494">Right-click the project.</span></span> <span data-ttu-id="539ca-495">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-495">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="539ca-496">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-496">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="539ca-498">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-498">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="539ca-499">클래스 이름을 *TodoItem* 으로 지정한 다음, **새로 만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-499">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="539ca-500">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-500">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="539ca-501">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-501">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="539ca-502">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-502">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="539ca-503">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-503">Add a database context</span></span>

<span data-ttu-id="539ca-504">*데이터베이스 컨텍스트* 는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-504">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="539ca-505">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-505">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-506">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="539ca-507">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-507">Add NuGet packages</span></span>

* <span data-ttu-id="539ca-508">**도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-508">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="539ca-509">**찾아보기** 탭을 선택한 다음, 검색 상자에 **Microsoft.EntityFrameworkCore.InMemory** 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-509">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="539ca-510">왼쪽 창에서 **Microsoft.EntityFrameworkCore.InMemory** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-510">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="539ca-511">오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-511">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet 패키지 관리자](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="539ca-513">TodoContext 데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-513">Add the TodoContext database context</span></span>

* <span data-ttu-id="539ca-514">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-514">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="539ca-515">클래스 이름을 *TodoContext* 로 지정하고 **추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-515">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="539ca-516">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-516">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="539ca-517">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-517">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="539ca-518">다음 코드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-518">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="539ca-519">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="539ca-519">Register the database context</span></span>

<span data-ttu-id="539ca-520">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-520">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="539ca-521">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-521">The container provides the service to controllers.</span></span>

<span data-ttu-id="539ca-522">*Startup.cs* 를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-522">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="539ca-523">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="539ca-523">The preceding code:</span></span>

* <span data-ttu-id="539ca-524">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-524">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="539ca-525">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-525">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="539ca-526">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-526">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="539ca-527">컨트롤러 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="539ca-527">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-528">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-528">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="539ca-529">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-529">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="539ca-530">**추가** > **스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-530">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="539ca-531">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러** 를 선택하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-531">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="539ca-532">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="539ca-532">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="539ca-533">**모델 클래스** 에서 **TodoItem(TodoApi.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-533">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="539ca-534">**데이터 컨텍스트 클래스** 에서 **TodoContext(TodoApi.Models)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-534">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="539ca-535">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-535">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="539ca-536">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-536">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="539ca-537">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-537">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="539ca-538">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="539ca-538">The preceding commands:</span></span>

* <span data-ttu-id="539ca-539">스캐폴딩에 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-539">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="539ca-540">스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다</span><span class="sxs-lookup"><span data-stu-id="539ca-540">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="539ca-541">`TodoItemsController`를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-541">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="539ca-542">생성된 코드는:</span><span class="sxs-lookup"><span data-stu-id="539ca-542">The generated code:</span></span>

* <span data-ttu-id="539ca-543">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-543">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="539ca-544">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-544">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="539ca-545">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-545">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="539ca-546">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-546">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="539ca-547">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-547">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="539ca-548">ASP.NET Core 템플릿과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-548">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="539ca-549">뷰가 있는 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-549">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="539ca-550">API 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-550">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="539ca-551">`[action]` 토큰이 경로 템플릿에 없는 경우 경로에서 [작업](xref:mvc/controllers/routing#action) 이름이 제외됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-551">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="539ca-552">즉, 일치하는 경로에서 작업과 연결된 메서드 이름이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-552">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="539ca-553">PostTodoItem 만들기 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="539ca-553">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="539ca-554">`PostTodoItem`의 return 문이 [nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-554">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="539ca-555">위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-555">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="539ca-556">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-556">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="539ca-557">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-557">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="539ca-558"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="539ca-559">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-559">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="539ca-560">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="539ca-561">응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-561">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="539ca-562">`Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-562">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="539ca-563">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="539ca-564">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="539ca-565">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="539ca-566">Postman을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-566">Install Postman</span></span>

<span data-ttu-id="539ca-567">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-567">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="539ca-568">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-568">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="539ca-569">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-569">Start the web app.</span></span>
* <span data-ttu-id="539ca-570">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-570">Start Postman.</span></span>
* <span data-ttu-id="539ca-571">**SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-571">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="539ca-572">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-572">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="539ca-573">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-573">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="539ca-574">Postman을 사용하여 PostTodoItem 테스트</span><span class="sxs-lookup"><span data-stu-id="539ca-574">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="539ca-575">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-575">Create a new request.</span></span>
* <span data-ttu-id="539ca-576">HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-576">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="539ca-577">URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-577">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="539ca-578">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="539ca-578">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="539ca-579">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-579">Select the **Body** tab.</span></span>
* <span data-ttu-id="539ca-580">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-580">Select the **raw** radio button.</span></span>
* <span data-ttu-id="539ca-581">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-581">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="539ca-582">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-582">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="539ca-583">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-583">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="539ca-585">Postman을 사용하여 위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="539ca-585">Test the location header URI with Postman</span></span>

* <span data-ttu-id="539ca-586">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-586">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="539ca-587">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-587">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* <span data-ttu-id="539ca-589">HTTP 메서드를 `GET`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-589">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="539ca-590">URI를 `https://localhost:<port>/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-590">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="539ca-591">예: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="539ca-591">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="539ca-592">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-592">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="539ca-593">GET 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="539ca-593">Examine the GET methods</span></span>

<span data-ttu-id="539ca-594">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-594">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="539ca-595">브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-595">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="539ca-596">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="539ca-596">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="539ca-597">`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-597">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="539ca-598">Postman을 사용하여 Get 테스트</span><span class="sxs-lookup"><span data-stu-id="539ca-598">Test Get with Postman</span></span>

* <span data-ttu-id="539ca-599">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-599">Create a new request.</span></span>
* <span data-ttu-id="539ca-600">HTTP 메서드를 **GET** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-600">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="539ca-601">요청 URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-601">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="539ca-602">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="539ca-602">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="539ca-603">Postman에서 **두 개의 창 보기** 를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-603">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="539ca-604">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-604">Select **Send**.</span></span>

<span data-ttu-id="539ca-605">이 앱은 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-605">This app uses an in-memory database.</span></span> <span data-ttu-id="539ca-606">앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-606">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="539ca-607">데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-607">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="539ca-608">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="539ca-608">Routing and URL paths</span></span>

<span data-ttu-id="539ca-609">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-609">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="539ca-610">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-610">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="539ca-611">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-611">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="539ca-612">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-612">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="539ca-613">이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems** Controller이므로 컨트롤러 이름은 “TodoItems”입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-613">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="539ca-614">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-614">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="539ca-615">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-615">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="539ca-616">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-616">This sample doesn't use a template.</span></span> <span data-ttu-id="539ca-617">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-617">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="539ca-618">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-618">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="539ca-619">`GetTodoItem`이 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-619">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="539ca-620">반환 값</span><span class="sxs-lookup"><span data-stu-id="539ca-620">Return values</span></span> 

<span data-ttu-id="539ca-621">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-621">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="539ca-622">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-622">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="539ca-623">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-623">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="539ca-624">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-624">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="539ca-625">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-625">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="539ca-626">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-626">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="539ca-627">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-627">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="539ca-628">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-628">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="539ca-629">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-629">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="539ca-630">PutTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="539ca-630">The PutTodoItem method</span></span>

<span data-ttu-id="539ca-631">다음과 같이 `PutTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-631">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="539ca-632">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-632">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="539ca-633">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-633">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="539ca-634">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-634">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="539ca-635">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-635">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="539ca-636">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-636">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="539ca-637">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="539ca-637">Test the PutTodoItem method</span></span>

<span data-ttu-id="539ca-638">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-638">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="539ca-639">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-639">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="539ca-640">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-640">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="539ca-641">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-641">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="539ca-642">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-642">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="539ca-644">DeleteTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="539ca-644">The DeleteTodoItem method</span></span>

<span data-ttu-id="539ca-645">다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-645">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="539ca-646">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="539ca-646">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="539ca-647">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-647">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="539ca-648">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-648">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="539ca-649">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-649">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="539ca-650">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-650">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="539ca-651">과도한 게시 방지</span><span class="sxs-lookup"><span data-stu-id="539ca-651">Prevent over-posting</span></span>

<span data-ttu-id="539ca-652">현재 샘플 앱은 전체 `TodoItem` 개체를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-652">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="539ca-653">일반적으로 프로덕션 앱은 모델의 하위 집합을 사용하여 입력 및 반환되는 데이터를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-653">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="539ca-654">이 동작에는 여러 가지 이유가 있으며, 보안이 주요 이유 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-654">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="539ca-655">일반적으로 모델의 하위 집합을 DTO(데이터 전송 개체), 입력 모델 또는 뷰 모델이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-655">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="539ca-656">이 문서에서는 **DTO** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-656">**DTO** is used in this article.</span></span>

<span data-ttu-id="539ca-657">DTO는 다음과 같은 용도로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-657">A DTO may be used to:</span></span>

* <span data-ttu-id="539ca-658">과도한 게시를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-658">Prevent over-posting.</span></span>
* <span data-ttu-id="539ca-659">클라이언트에 표시되지 않아야 하는 속성을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-659">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="539ca-660">페이로드 크기를 줄이기 위해 일부 속성을 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-660">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="539ca-661">중첩된 개체를 포함하는 개체 그래프를 평면화합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-661">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="539ca-662">클라이언트에는 평면화된 개체 그래프가 더 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-662">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="539ca-663">DTO 방법을 설명하려면 비밀 필드를 포함하도록 `TodoItem` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-663">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="539ca-664">이 앱에서는 숨겨진 필드를 숨겨야 하지만, 관리 앱은 숨겨진 필드를 공개할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-664">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="539ca-665">비밀 필드를 게시하고 가져올 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-665">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="539ca-666">DTO 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-666">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="539ca-667">`TodoItemDTO`를 사용하도록 `TodoItemsController`를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-667">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="539ca-668">비밀 필드를 게시하거나 가져올 수 없음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-668">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="539ca-669">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="539ca-669">Call the web API with JavaScript</span></span>

<span data-ttu-id="539ca-670">[자습서: JavaScript로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-javascript)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-670">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="539ca-671">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-671">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="539ca-672">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-672">Create a web API project.</span></span>
> * <span data-ttu-id="539ca-673">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-673">Add a model class and a database context.</span></span>
> * <span data-ttu-id="539ca-674">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-674">Add a controller.</span></span>
> * <span data-ttu-id="539ca-675">CRUD 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-675">Add CRUD methods.</span></span>
> * <span data-ttu-id="539ca-676">라우팅 및 URL 경로 구성</span><span class="sxs-lookup"><span data-stu-id="539ca-676">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="539ca-677">반환 값 지정</span><span class="sxs-lookup"><span data-stu-id="539ca-677">Specify return values.</span></span>
> * <span data-ttu-id="539ca-678">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-678">Call the web API with Postman.</span></span>
> * <span data-ttu-id="539ca-679">JavaScript를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-679">Call the web API with JavaScript.</span></span>

<span data-ttu-id="539ca-680">작업을 완료하면 웹 API는 관계형 데이터베이스에 저장된 "할 일" 항목을 관리할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-680">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="539ca-681">개요 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-681">Overview 2.1</span></span>

<span data-ttu-id="539ca-682">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-682">This tutorial creates the following API:</span></span>

|<span data-ttu-id="539ca-683">API</span><span class="sxs-lookup"><span data-stu-id="539ca-683">API</span></span> | <span data-ttu-id="539ca-684">설명</span><span class="sxs-lookup"><span data-stu-id="539ca-684">Description</span></span> | <span data-ttu-id="539ca-685">요청 본문</span><span class="sxs-lookup"><span data-stu-id="539ca-685">Request body</span></span> | <span data-ttu-id="539ca-686">응답 본문</span><span class="sxs-lookup"><span data-stu-id="539ca-686">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="539ca-687">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="539ca-687">GET /api/TodoItems</span></span> | <span data-ttu-id="539ca-688">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="539ca-688">Get all to-do items</span></span> | <span data-ttu-id="539ca-689">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-689">None</span></span> | <span data-ttu-id="539ca-690">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="539ca-690">Array of to-do items</span></span>|
|<span data-ttu-id="539ca-691">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="539ca-691">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="539ca-692">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="539ca-692">Get an item by ID</span></span> | <span data-ttu-id="539ca-693">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-693">None</span></span> | <span data-ttu-id="539ca-694">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="539ca-694">To-do item</span></span>|
|<span data-ttu-id="539ca-695">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="539ca-695">POST /api/TodoItems</span></span> | <span data-ttu-id="539ca-696">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="539ca-696">Add a new item</span></span> | <span data-ttu-id="539ca-697">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="539ca-697">To-do item</span></span> | <span data-ttu-id="539ca-698">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="539ca-698">To-do item</span></span> |
|<span data-ttu-id="539ca-699">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="539ca-699">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="539ca-700">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="539ca-700">Update an existing item &nbsp;</span></span> | <span data-ttu-id="539ca-701">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="539ca-701">To-do item</span></span> | <span data-ttu-id="539ca-702">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-702">None</span></span> |
|<span data-ttu-id="539ca-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="539ca-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="539ca-704">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="539ca-704">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="539ca-705">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-705">None</span></span> | <span data-ttu-id="539ca-706">없음</span><span class="sxs-lookup"><span data-stu-id="539ca-706">None</span></span>|

<span data-ttu-id="539ca-707">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-707">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="539ca-713">필수 구성 요소 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-713">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-714">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-714">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="539ca-715">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="539ca-715">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="539ca-716">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-716">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="539ca-717">웹 프로젝트 만들기 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-717">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-718">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="539ca-719">**파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-719">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="539ca-720">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-720">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="539ca-721">프로젝트 이름을 *TodoApi* 로 지정하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-721">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="539ca-722">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 2.2** 가 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-722">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="539ca-723">**API** 템플릿을 선택하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-723">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="539ca-724">**Docker 지원 사용** 을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="539ca-724">**Don't** select **Enable Docker Support**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="539ca-726">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="539ca-726">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="539ca-727">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-727">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="539ca-728">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-728">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="539ca-729">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-729">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="539ca-730">이러한 명령은 새 웹 API 프로젝트를 만들고 새 프로젝트 폴더에서 Visual Studio Code의 새 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-730">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="539ca-731">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-731">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="539ca-732">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-732">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="539ca-733">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-733">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="539ca-735">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-735">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="539ca-736">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-736">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="539ca-737">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 2.x **대상 프레임워크** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-737">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="539ca-738">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-738">Select **Next**.</span></span>

* <span data-ttu-id="539ca-739">**프로젝트 이름** 으로 *TodoApi* 를 입력한 다음, **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-739">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="539ca-741">API 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-741">Test the API 2.1</span></span>

<span data-ttu-id="539ca-742">프로젝트 템플릿은 `values` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-742">The project template creates a `values` API.</span></span> <span data-ttu-id="539ca-743">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-743">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-744">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-744">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="539ca-745">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-745">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="539ca-746">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/api/values`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-746">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="539ca-747">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-747">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="539ca-748">다음으로, **보안 경고** 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-748">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="539ca-749">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="539ca-749">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="539ca-750">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-750">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="539ca-751">브라우저에서 `https://localhost:5001/api/values` URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-751">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="539ca-752">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-752">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="539ca-753">**실행** > **디버깅 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-753">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="539ca-754">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-754">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="539ca-755">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-755">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="539ca-756">`/api/values`를 URL에 추가합니다(URL을 `https://localhost:<port>/api/values`로 변경).</span><span class="sxs-lookup"><span data-stu-id="539ca-756">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="539ca-757">다음 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-757">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="539ca-758">모델 클래스 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-758">Add a model class 2.1</span></span>

<span data-ttu-id="539ca-759">*모델* 은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-759">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="539ca-760">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-760">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-761">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="539ca-762">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-762">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="539ca-763">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-763">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="539ca-764">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-764">Name the folder *Models*.</span></span>

* <span data-ttu-id="539ca-765">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-765">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="539ca-766">클래스 이름을 *TodoItem* 으로 지정하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-766">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="539ca-767">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-767">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="539ca-768">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="539ca-768">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="539ca-769">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-769">Add a folder named *Models*.</span></span>

* <span data-ttu-id="539ca-770">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-770">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="539ca-771">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-771">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="539ca-772">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-772">Right-click the project.</span></span> <span data-ttu-id="539ca-773">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-773">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="539ca-774">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-774">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="539ca-776">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-776">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="539ca-777">클래스 이름을 *TodoItem* 으로 지정한 다음, **새로 만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-777">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="539ca-778">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-778">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="539ca-779">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-779">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="539ca-780">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-780">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="539ca-781">데이터베이스 컨텍스트 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-781">Add a database context 2.1</span></span>

<span data-ttu-id="539ca-782">*데이터베이스 컨텍스트* 는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-782">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="539ca-783">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-783">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-784">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-784">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="539ca-785">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-785">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="539ca-786">클래스 이름을 *TodoContext* 로 지정하고 **추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-786">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="539ca-787">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-787">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="539ca-788">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-788">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="539ca-789">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-789">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="539ca-790">데이터베이스 컨텍스트 등록 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-790">Register the database context 2.1</span></span>

<span data-ttu-id="539ca-791">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-791">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="539ca-792">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-792">The container provides the service to controllers.</span></span>

<span data-ttu-id="539ca-793">*Startup.cs* 를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-793">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="539ca-794">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="539ca-794">The preceding code:</span></span>

* <span data-ttu-id="539ca-795">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-795">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="539ca-796">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-796">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="539ca-797">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-797">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="539ca-798">컨트롤러 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-798">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-799">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-799">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="539ca-800">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-800">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="539ca-801">**추가** > **새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-801">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="539ca-802">**새 항목 추가** 대화 상자에서 **API 컨트롤러 클래스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-802">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="539ca-803">클래스 이름을 *TodoController* 로 지정하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-803">Name the class *TodoController*, and select **Add**.</span></span>

  ![검색 상자의 컨트롤러 및 웹 API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="539ca-805">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-805">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="539ca-806">*Controllers* 폴더에 `TodoController`라는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-806">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="539ca-807">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-807">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="539ca-808">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="539ca-808">The preceding code:</span></span>

* <span data-ttu-id="539ca-809">메서드 없이 API 컨트롤러 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-809">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="539ca-810">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-810">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="539ca-811">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-811">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="539ca-812">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-812">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="539ca-813">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-813">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="539ca-814">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-814">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="539ca-815">데이터베이스가 비어 있는 경우 데이터베이스에 `Item1`이라는 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-815">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="539ca-816">이 코드는 생성자에 위치하므로 새 HTTP 요청이 발생할 때마다 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-816">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="539ca-817">모든 항목을 삭제하면 생성자는 다음에 API가 호출될 경우 `Item1`을 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-817">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="539ca-818">따라서 실제로 작동되는 경우 삭제가 작동하지 않는 것처럼 보일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-818">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="539ca-819">GET 메서드 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-819">Add Get methods 2.1</span></span>

<span data-ttu-id="539ca-820">할 일 항목을 가져오는 API를 제공하려면 다음 메서드를 `TodoController` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-820">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="539ca-821">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-821">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="539ca-822">앱이 계속 실행되고 있으면 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-822">Stop the app if it's still running.</span></span> <span data-ttu-id="539ca-823">그런 다음, 다시 실행하여 최신 변경 내용을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-823">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="539ca-824">브라우저에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-824">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="539ca-825">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="539ca-825">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="539ca-826">`GetTodoItems`를 호출하여 다음 HTTP 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-826">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="539ca-827">라우팅 및 URL 경로 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-827">Routing and URL paths 2.1</span></span>

<span data-ttu-id="539ca-828">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-828">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="539ca-829">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-829">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="539ca-830">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-830">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="539ca-831">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-831">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="539ca-832">이 샘플의 경우 컨트롤러 클래스 이름은 **Todo** Controller이므로 컨트롤러 이름은 "todo"입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-832">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="539ca-833">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-833">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="539ca-834">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-834">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="539ca-835">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-835">This sample doesn't use a template.</span></span> <span data-ttu-id="539ca-836">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-836">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="539ca-837">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-837">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="539ca-838">`GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-838">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="539ca-839">반환 값 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-839">Return values 2.1</span></span>

<span data-ttu-id="539ca-840">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-840">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="539ca-841">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-841">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="539ca-842">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-842">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="539ca-843">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-843">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="539ca-844">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-844">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="539ca-845">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-845">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="539ca-846">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-846">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="539ca-847">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-847">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="539ca-848">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-848">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="539ca-849">GetTodoItems 메서드 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-849">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="539ca-850">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-850">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="539ca-851">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-851">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="539ca-852">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-852">Start the web app.</span></span>
* <span data-ttu-id="539ca-853">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-853">Start Postman.</span></span>
* <span data-ttu-id="539ca-854">**SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-854">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="539ca-855">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-855">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="539ca-856">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-856">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="539ca-857">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="539ca-857">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="539ca-858">**Postman** > **기본 설정**(**일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-858">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="539ca-859">또는 렌치를 선택하고 **설정** 을 선택한 다음 SSL 인증서 확인을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-859">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="539ca-860">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-860">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="539ca-861">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-861">Create a new request.</span></span>
  * <span data-ttu-id="539ca-862">HTTP 메서드를 **GET** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-862">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="539ca-863">요청 URI를 `https://localhost:<port>/api/todo`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-863">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="539ca-864">예: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="539ca-864">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="539ca-865">Postman에서 **두 개의 창 보기** 를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-865">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="539ca-866">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-866">Select **Send**.</span></span>

![Get 요청이 있는 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="539ca-868">Create 메서드 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-868">Add a Create method 2.1</span></span>

<span data-ttu-id="539ca-869">*Controllers/TodoController.cs* 내부에 다음 `PostTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-869">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="539ca-870">위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-870">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="539ca-871">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-871">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="539ca-872">`CreatedAtAction` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-872">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="539ca-873">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-873">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="539ca-874">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-874">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="539ca-875">`Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-875">Adds a `Location` header to the response.</span></span> <span data-ttu-id="539ca-876">`Location` 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-876">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="539ca-877">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-877">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="539ca-878">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-878">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="539ca-879">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-879">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="539ca-880">PostTodoItem 메서드 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-880">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="539ca-881">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-881">Build the project.</span></span>
* <span data-ttu-id="539ca-882">Postman에서 HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-882">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="539ca-883">URI를 `https://localhost:<port>/api/Todo`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-883">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="539ca-884">예: `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="539ca-884">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="539ca-885">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-885">Select the **Body** tab.</span></span>
* <span data-ttu-id="539ca-886">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-886">Select the **raw** radio button.</span></span>
* <span data-ttu-id="539ca-887">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-887">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="539ca-888">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-888">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="539ca-889">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-889">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="539ca-891">405 메서드가 허용되지 않음 오류가 발생할 경우 `PostTodoItem` 메서드를 추가한 후에 프로젝트를 컴파일하지 않아서 발생한 결과일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-891">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="539ca-892">위치 헤더 URI 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-892">Test the location header URI 2.1</span></span>

* <span data-ttu-id="539ca-893">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-893">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="539ca-894">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-894">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/pmc2.png)

* <span data-ttu-id="539ca-896">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-896">Set the method to GET.</span></span>
* <span data-ttu-id="539ca-897">URI를 `https://localhost:<port>/api/TodoItems/2`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-897">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="539ca-898">예: `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="539ca-898">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="539ca-899">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-899">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="539ca-900">PutTodoItem 메서드 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-900">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="539ca-901">다음 `PutTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-901">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="539ca-902">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-902">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="539ca-903">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-903">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="539ca-904">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-904">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="539ca-905">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-905">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="539ca-906">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-906">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="539ca-907">PutTodoItem 메서드 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-907">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="539ca-908">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-908">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="539ca-909">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-909">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="539ca-910">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-910">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="539ca-911">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-911">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="539ca-912">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-912">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="539ca-914">DeleteTodoItem 메서드 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-914">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="539ca-915">다음 `DeleteTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-915">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="539ca-916">`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-916">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="539ca-917">DeleteTodoItem 메서드 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-917">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="539ca-918">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-918">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="539ca-919">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-919">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="539ca-920">삭제할 개체의 URI를 설정합니다(예: `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="539ca-920">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="539ca-921">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-921">Select **Send**.</span></span>

<span data-ttu-id="539ca-922">샘플 앱을 사용하면 모든 항목을 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-922">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="539ca-923">하지만 마지막 항목이 삭제되면 다음에 API를 호출하는 경우 모델 클래스 생성자에서 새로운 항목이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-923">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="539ca-924">JavaScript를 사용하여 웹 API 호출 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-924">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="539ca-925">이 섹션에는 JavaScript를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-925">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="539ca-926">jQuery가 요청을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-926">jQuery initiates the request.</span></span> <span data-ttu-id="539ca-927">JavaScript는 웹 API 응답의 세부 정보를 토대로 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-927">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="539ca-928">다음 강조 표시된 코드로 *Startup.cs* 를 업데이트하여 앱이 [정적 파일을 제공](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)하고 [기본 파일 매핑을 사용](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-928">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="539ca-929">프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-929">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="539ca-930">*index.html* 이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-930">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="539ca-931">다음 표시로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-931">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="539ca-932">*site.js* 라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-932">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="539ca-933">다음 코드로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-933">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="539ca-934">HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-934">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="539ca-935">*Properties\launchSettings.json* 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-935">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="539ca-936">`launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-936">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="539ca-937">이 샘플은 웹 API의 CRUD 메서드를 모두 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-937">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="539ca-938">API 호출에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-938">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="539ca-939">할 일 항목의 목록 가져오기 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-939">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="539ca-940">jQuery는 할 일 항목의 배열을 나타내는 JSON을 반환하는 웹 API에 HTTP GET 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-940">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="539ca-941">요청이 성공하면 `success` 콜백 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-941">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="539ca-942">콜백에서 DOM은 할 일 정보로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-942">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="539ca-943">할 일 항목 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-943">Add a to-do item 2.1</span></span>

<span data-ttu-id="539ca-944">jQuery는 요청 본문에 있는 할 일 항목을 사용하여 HTTP POST 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-944">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="539ca-945">`accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-945">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="539ca-946">[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-946">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="539ca-947">API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-947">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="539ca-948">할 일 항목 업데이트 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-948">Update a to-do item 2.1</span></span>

<span data-ttu-id="539ca-949">할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-949">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="539ca-950">`url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-950">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="539ca-951">할 일 항목 삭제 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-951">Delete a to-do item 2.1</span></span>

<span data-ttu-id="539ca-952">할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="539ca-952">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="539ca-953">웹 API에 인증 지원 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-953">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="539ca-954">추가 리소스 2.1</span><span class="sxs-lookup"><span data-stu-id="539ca-954">Additional resources 2.1</span></span>

<span data-ttu-id="539ca-955">[이 자습서에서 샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="539ca-955">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="539ca-956">[다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-956">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="539ca-957">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="539ca-957">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="539ca-958">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="539ca-958">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
* [<span data-ttu-id="539ca-959">Microsoft Learn: ASP.NET Core를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="539ca-959">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
