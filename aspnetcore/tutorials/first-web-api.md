---
title: '자습서: ASP.NET Core를 사용하여 웹 API 만들기'
author: rick-anderson
description: ASP.NET Core를 사용하여 웹 API를 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
ms.openlocfilehash: ccbfc27eb89e23938a69f0ab4cb306d6a4136889
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175054"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="91253-103">자습서: ASP.NET Core를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="91253-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="91253-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="91253-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="91253-105">이 자습서에서는 ASP.NET Core를 사용하여 웹 API를 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="91253-106">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="91253-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="91253-107">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-107">Create a web API project.</span></span>
> * <span data-ttu-id="91253-108">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="91253-109">CRUD 메서드로 컨트롤러를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="91253-110">라우팅, URL 경로 및 반환 값을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="91253-111">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-111">Call the web API with Postman.</span></span>

<span data-ttu-id="91253-112">과정을 마치면 웹 API를 통해 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="91253-113">개요</span><span class="sxs-lookup"><span data-stu-id="91253-113">Overview</span></span>

<span data-ttu-id="91253-114">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="91253-115">API</span><span class="sxs-lookup"><span data-stu-id="91253-115">API</span></span> | <span data-ttu-id="91253-116">설명</span><span class="sxs-lookup"><span data-stu-id="91253-116">Description</span></span> | <span data-ttu-id="91253-117">요청 본문</span><span class="sxs-lookup"><span data-stu-id="91253-117">Request body</span></span> | <span data-ttu-id="91253-118">응답 본문</span><span class="sxs-lookup"><span data-stu-id="91253-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="91253-119">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="91253-119">Get all to-do items</span></span> | <span data-ttu-id="91253-120">없음</span><span class="sxs-lookup"><span data-stu-id="91253-120">None</span></span> | <span data-ttu-id="91253-121">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="91253-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="91253-122">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="91253-122">Get an item by ID</span></span> | <span data-ttu-id="91253-123">없음</span><span class="sxs-lookup"><span data-stu-id="91253-123">None</span></span> | <span data-ttu-id="91253-124">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="91253-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="91253-125">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="91253-125">Add a new item</span></span> | <span data-ttu-id="91253-126">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="91253-126">To-do item</span></span> | <span data-ttu-id="91253-127">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="91253-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="91253-128">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="91253-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="91253-129">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="91253-129">To-do item</span></span> | <span data-ttu-id="91253-130">없음</span><span class="sxs-lookup"><span data-stu-id="91253-130">None</span></span> |
|<span data-ttu-id="91253-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="91253-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="91253-132">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="91253-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="91253-133">없음</span><span class="sxs-lookup"><span data-stu-id="91253-133">None</span></span> | <span data-ttu-id="91253-134">없음</span><span class="sxs-lookup"><span data-stu-id="91253-134">None</span></span>|

<span data-ttu-id="91253-135">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="91253-135">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="91253-141">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="91253-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="91253-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91253-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91253-144">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="91253-145">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="91253-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91253-147">**파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="91253-148">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="91253-149">프로젝트 이름을 *TodoApi* 로 지정하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="91253-150">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 5.0** 이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="91253-151">**API** 템플릿을 선택하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-151">Select the **API** template and click **Create**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="91253-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91253-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91253-154">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="91253-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="91253-155">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="91253-156">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="91253-157">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="91253-158">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="91253-158">The preceding commands:</span></span>

  * <span data-ttu-id="91253-159">새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="91253-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="91253-160">다음 섹션에서 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91253-161">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91253-162">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-162">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="91253-164">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="91253-165">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 템플릿 선택](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="91253-167">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 5.x **대상 프레임워크** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="91253-168">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-168">Select **Next**.</span></span>

* <span data-ttu-id="91253-169">**프로젝트 이름** 으로 *TodoApi* 를 입력한 다음, **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="91253-171">프로젝트 폴더에서 명령 터미널을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="91253-172">프로젝트 테스트</span><span class="sxs-lookup"><span data-stu-id="91253-172">Test the project</span></span>

<span data-ttu-id="91253-173">프로젝트 템플릿은 [Swagger](xref:tutorials/web-api-help-pages-using-swagger)를 지원하는 `WeatherForecast` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91253-175">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="91253-176">Visual Studio에서 다음을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-176">Visual Studio launches:</span></span>

* <span data-ttu-id="91253-177">IIS Express 웹 서버.</span><span class="sxs-lookup"><span data-stu-id="91253-177">The IIS Express web server.</span></span>
* <span data-ttu-id="91253-178">기본 브라우저를 시작하고 `https://localhost:<port>/swagger/index.html`로 이동합니다. 여기서 `<port>`는 임의로 선택한 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91253-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91253-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="91253-180">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="91253-181">브라우저에서 [https://localhost:5001/swagger](https://localhost:5001/swagger) URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91253-182">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="91253-183">**실행** > **디버깅 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="91253-184">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="91253-185">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="91253-186">`/swagger`를 URL에 추가합니다(URL을 `https://localhost:<port>/swagger`로 변경).</span><span class="sxs-lookup"><span data-stu-id="91253-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="91253-187">Swagger 페이지 `/swagger/index.html`이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="91253-188">**GET** > **사용해 보기** > **실행** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="91253-189">페이지에 다음이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-189">The page displays:</span></span>

* <span data-ttu-id="91253-190">WeatherForecast API를 테스트할 [Curl](https://curl.haxx.se/) 명령</span><span class="sxs-lookup"><span data-stu-id="91253-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="91253-191">WeatherForecast API를 테스트할 URL</span><span class="sxs-lookup"><span data-stu-id="91253-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="91253-192">응답 코드, 본문 및 헤더</span><span class="sxs-lookup"><span data-stu-id="91253-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="91253-193">미디어 유형과 예제 값 및 스키마가 포함된 드롭다운 목록 상자</span><span class="sxs-lookup"><span data-stu-id="91253-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="91253-194">Swagger는 웹 API에 유용한 설명서 및 도움말 페이지를 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="91253-195">이 자습서에서는 웹 API 만들기에 대해 집중적으로 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="91253-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="91253-196">Swagger에 대한 자세한 내용은 <xref:tutorials/web-api-help-pages-using-swagger>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="91253-197">브라우저에서 **요청 URL** `https://localhost:<port>/WeatherForecast`를 복사하여 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="91253-198">다음과 비슷한 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="91253-199">launchUrl 업데이트</span><span class="sxs-lookup"><span data-stu-id="91253-199">Update the launchUrl</span></span>

<span data-ttu-id="91253-200">*Properties\launchSettings.json* 에서 `launchUrl`을 `"swagger"`에서 `"api/TodoItems"`로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="91253-201">Swagger가 제거되었기 때문에 위의 태그는 다음 섹션에 추가된 컨트롤러의 GET 메서드로 시작되는 URL을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="91253-202">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="91253-202">Add a model class</span></span>

<span data-ttu-id="91253-203">*모델* 은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="91253-204">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91253-206">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="91253-207">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="91253-208">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="91253-209">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="91253-210">클래스 이름을 *TodoItem* 으로 지정하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="91253-211">템플릿 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="91253-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91253-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91253-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91253-213">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="91253-214">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91253-215">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91253-216">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-216">Right-click the project.</span></span> <span data-ttu-id="91253-217">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="91253-218">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-218">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="91253-220">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="91253-221">클래스 이름을 *TodoItem* 으로 지정한 다음, **새로 만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="91253-222">템플릿 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="91253-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="91253-223">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="91253-224">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="91253-225">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="91253-225">Add a database context</span></span>

<span data-ttu-id="91253-226">*데이터베이스 컨텍스트* 는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="91253-227"><xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="91253-229">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="91253-229">Add NuGet packages</span></span>

* <span data-ttu-id="91253-230">**도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="91253-231">**찾아보기** 탭을 선택한 다음 검색 상자에 **Microsoft.EntityFrameworkCore.SqlServer** 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="91253-232">왼쪽 창에서 **Microsoft.EntityFrameworkCore.SqlServer** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="91253-233">오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="91253-234">앞의 지침을 사용하여 **Microsoft.EntityFrameworkCore.InMemory** NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="91253-235">![NuGet 패키지 관리자](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="91253-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="91253-236">TodoContext 데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="91253-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="91253-237">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="91253-238">클래스 이름을 *TodoContext* 로 지정하고 **추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="91253-239">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="91253-240">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="91253-241">다음 코드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="91253-242">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="91253-242">Register the database context</span></span>

<span data-ttu-id="91253-243">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="91253-244">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="91253-245">다음 코드로 *Startup.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="91253-246">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="91253-246">The preceding code:</span></span>

* <span data-ttu-id="91253-247">Swagger 호출을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="91253-248">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="91253-249">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="91253-250">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="91253-251">컨트롤러 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="91253-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91253-253">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="91253-254">**추가** > **스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="91253-255">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러** 를 선택하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="91253-256">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="91253-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="91253-257">**모델 클래스** 에서 **TodoItem(TodoApi.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="91253-258">**데이터 컨텍스트 클래스** 에서 **TodoContext(TodoApi.Models)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="91253-259">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="91253-260">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="91253-261">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="91253-262">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="91253-262">The preceding commands:</span></span>

* <span data-ttu-id="91253-263">스캐폴딩에 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="91253-264">스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다</span><span class="sxs-lookup"><span data-stu-id="91253-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="91253-265">`TodoItemsController`를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="91253-266">생성된 코드는:</span><span class="sxs-lookup"><span data-stu-id="91253-266">The generated code:</span></span>

* <span data-ttu-id="91253-267">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="91253-268">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="91253-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="91253-269">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="91253-270">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="91253-271">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="91253-272">ASP.NET Core 템플릿과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="91253-273">뷰가 있는 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="91253-274">API 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="91253-275">`[action]` 토큰이 경로 템플릿에 없는 경우 경로에서 [작업](xref:mvc/controllers/routing#action) 이름이 제외됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="91253-276">즉, 일치하는 경로에서 작업과 연결된 메서드 이름이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="91253-277">PostTodoItem 만들기 메서드 업데이트</span><span class="sxs-lookup"><span data-stu-id="91253-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="91253-278">`PostTodoItem`의 return 문이 [nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="91253-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="91253-279">위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="91253-280">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="91253-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="91253-281">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="91253-282"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="91253-283">성공하면 [HTTP 201 상태 코드](https://developer.mozilla.org/docs/Web/HTTP/Status/201)를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="91253-284">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="91253-285">응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="91253-286">`Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="91253-287">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="91253-288">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="91253-289">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="91253-290">Postman을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-290">Install Postman</span></span>

<span data-ttu-id="91253-291">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="91253-292">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="91253-293">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-293">Start the web app.</span></span>
* <span data-ttu-id="91253-294">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-294">Start Postman.</span></span>
* <span data-ttu-id="91253-295">**SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="91253-296">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="91253-297">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="91253-298">Postman을 사용하여 PostTodoItem 테스트</span><span class="sxs-lookup"><span data-stu-id="91253-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="91253-299">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-299">Create a new request.</span></span>
* <span data-ttu-id="91253-300">HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="91253-301">URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="91253-302">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="91253-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="91253-303">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="91253-304">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="91253-305">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="91253-306">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="91253-307">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-307">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="91253-309">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="91253-309">Test the location header URI</span></span>

<span data-ttu-id="91253-310">위치 헤더 URI는 브라우저에서 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="91253-311">위치 헤더 URI를 복사하여 브라우저에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="91253-312">Postman에서 테스트하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-312">To test in Postman:</span></span>

* <span data-ttu-id="91253-313">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="91253-314">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-314">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* <span data-ttu-id="91253-316">HTTP 메서드를 `GET`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="91253-317">URI를 `https://localhost:<port>/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="91253-318">예: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="91253-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="91253-319">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="91253-320">GET 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="91253-320">Examine the GET methods</span></span>

<span data-ttu-id="91253-321">두 개의 GET 엔드포인트가 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="91253-322">브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="91253-323">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="91253-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="91253-324">`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="91253-325">Postman을 사용하여 Get 테스트</span><span class="sxs-lookup"><span data-stu-id="91253-325">Test Get with Postman</span></span>

* <span data-ttu-id="91253-326">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-326">Create a new request.</span></span>
* <span data-ttu-id="91253-327">HTTP 메서드를 **GET** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="91253-328">요청 URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="91253-329">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="91253-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="91253-330">Postman에서 **두 개의 창 보기** 를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="91253-331">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-331">Select **Send**.</span></span>

<span data-ttu-id="91253-332">이 앱은 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-332">This app uses an in-memory database.</span></span> <span data-ttu-id="91253-333">앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="91253-334">데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="91253-335">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="91253-335">Routing and URL paths</span></span>

<span data-ttu-id="91253-336">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="91253-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="91253-337">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="91253-338">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="91253-339">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="91253-340">이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems** Controller이므로 컨트롤러 이름은 “TodoItems”입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="91253-341">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="91253-342">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="91253-343">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-343">This sample doesn't use a template.</span></span> <span data-ttu-id="91253-344">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="91253-345">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="91253-346">`GetTodoItem`이 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="91253-347">반환 값</span><span class="sxs-lookup"><span data-stu-id="91253-347">Return values</span></span>

<span data-ttu-id="91253-348">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="91253-349">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="91253-350">이 반환 형식의 응답 코드는 [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200)이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="91253-351">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="91253-352">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="91253-353">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="91253-354">요청된 ID와 일치하는 항목이 없는 경우 메서드에서 [404 상태](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="91253-355">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="91253-356">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="91253-357">PutTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="91253-357">The PutTodoItem method</span></span>

<span data-ttu-id="91253-358">다음과 같이 `PutTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="91253-359">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="91253-360">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="91253-361">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="91253-362">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="91253-363">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="91253-364">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="91253-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="91253-365">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="91253-366">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="91253-367">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="91253-368">Id = 1인 할 일 항목을 업데이트하고 해당 이름을 `"feed fish"`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="91253-369">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="91253-369">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="91253-371">DeleteTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="91253-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="91253-372">다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="91253-373">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="91253-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="91253-374">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="91253-375">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="91253-376">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="91253-377">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="91253-378">과도한 게시 방지</span><span class="sxs-lookup"><span data-stu-id="91253-378">Prevent over-posting</span></span>

<span data-ttu-id="91253-379">현재 샘플 앱은 전체 `TodoItem` 개체를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="91253-380">일반적으로 프로덕션 앱은 모델의 하위 집합을 사용하여 입력 및 반환되는 데이터를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="91253-381">이 동작에는 여러 가지 이유가 있으며, 보안이 주요 이유 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="91253-382">일반적으로 모델의 하위 집합을 DTO(데이터 전송 개체), 입력 모델 또는 뷰 모델이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="91253-383">이 문서에서는 **DTO** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="91253-384">DTO는 다음과 같은 용도로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-384">A DTO may be used to:</span></span>

* <span data-ttu-id="91253-385">과도한 게시를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-385">Prevent over-posting.</span></span>
* <span data-ttu-id="91253-386">클라이언트에 표시되지 않아야 하는 속성을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="91253-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="91253-387">페이로드 크기를 줄이기 위해 일부 속성을 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="91253-388">중첩된 개체를 포함하는 개체 그래프를 평면화합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="91253-389">클라이언트에는 평면화된 개체 그래프가 더 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="91253-390">DTO 방법을 설명하려면 비밀 필드를 포함하도록 `TodoItem` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="91253-391">이 앱에서는 숨겨진 필드를 숨겨야 하지만, 관리 앱은 숨겨진 필드를 공개할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="91253-392">비밀 필드를 게시하고 가져올 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="91253-393">DTO 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="91253-394">`TodoItemDTO`를 사용하도록 `TodoItemsController`를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="91253-395">비밀 필드를 게시하거나 가져올 수 없음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="91253-396">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="91253-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="91253-397">[자습서: JavaScript로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-javascript)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="91253-398">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="91253-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="91253-399">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-399">Create a web API project.</span></span>
> * <span data-ttu-id="91253-400">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="91253-401">CRUD 메서드로 컨트롤러를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="91253-402">라우팅, URL 경로 및 반환 값을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="91253-403">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-403">Call the web API with Postman.</span></span>

<span data-ttu-id="91253-404">과정을 마치면 웹 API를 통해 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="91253-405">개요</span><span class="sxs-lookup"><span data-stu-id="91253-405">Overview</span></span>

<span data-ttu-id="91253-406">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="91253-407">API</span><span class="sxs-lookup"><span data-stu-id="91253-407">API</span></span> | <span data-ttu-id="91253-408">설명</span><span class="sxs-lookup"><span data-stu-id="91253-408">Description</span></span> | <span data-ttu-id="91253-409">요청 본문</span><span class="sxs-lookup"><span data-stu-id="91253-409">Request body</span></span> | <span data-ttu-id="91253-410">응답 본문</span><span class="sxs-lookup"><span data-stu-id="91253-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="91253-411">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="91253-411">Get all to-do items</span></span> | <span data-ttu-id="91253-412">없음</span><span class="sxs-lookup"><span data-stu-id="91253-412">None</span></span> | <span data-ttu-id="91253-413">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="91253-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="91253-414">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="91253-414">Get an item by ID</span></span> | <span data-ttu-id="91253-415">없음</span><span class="sxs-lookup"><span data-stu-id="91253-415">None</span></span> | <span data-ttu-id="91253-416">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="91253-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="91253-417">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="91253-417">Add a new item</span></span> | <span data-ttu-id="91253-418">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="91253-418">To-do item</span></span> | <span data-ttu-id="91253-419">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="91253-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="91253-420">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="91253-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="91253-421">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="91253-421">To-do item</span></span> | <span data-ttu-id="91253-422">없음</span><span class="sxs-lookup"><span data-stu-id="91253-422">None</span></span> |
|<span data-ttu-id="91253-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="91253-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="91253-424">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="91253-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="91253-425">없음</span><span class="sxs-lookup"><span data-stu-id="91253-425">None</span></span> | <span data-ttu-id="91253-426">없음</span><span class="sxs-lookup"><span data-stu-id="91253-426">None</span></span>|

<span data-ttu-id="91253-427">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="91253-427">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="91253-433">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="91253-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="91253-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91253-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91253-436">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="91253-437">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="91253-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91253-439">**파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="91253-440">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="91253-441">프로젝트 이름을 *TodoApi* 로 지정하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="91253-442">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.1** 이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="91253-443">**API** 템플릿을 선택하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-443">Select the **API** template and click **Create**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="91253-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91253-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91253-446">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="91253-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="91253-447">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="91253-448">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="91253-449">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="91253-450">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="91253-450">The preceding commands:</span></span>

  * <span data-ttu-id="91253-451">새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="91253-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="91253-452">다음 섹션에서 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91253-453">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91253-454">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-454">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="91253-456">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="91253-457">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 템플릿 선택](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="91253-459">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 3.x **대상 프레임워크** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="91253-460">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-460">Select **Next**.</span></span>

* <span data-ttu-id="91253-461">**프로젝트 이름** 으로 *TodoApi* 를 입력한 다음, **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="91253-463">프로젝트 폴더에서 명령 터미널을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="91253-464">API 테스트</span><span class="sxs-lookup"><span data-stu-id="91253-464">Test the API</span></span>

<span data-ttu-id="91253-465">프로젝트 템플릿은 `WeatherForecast` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="91253-466">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91253-468">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="91253-469">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/WeatherForecast`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="91253-470">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="91253-471">다음으로, **보안 경고** 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91253-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91253-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91253-473">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="91253-474">브라우저에서 `https://localhost:5001/WeatherForecast` URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91253-475">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="91253-476">**실행** > **디버깅 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="91253-477">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="91253-478">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="91253-479">`/WeatherForecast`를 URL에 추가합니다(URL을 `https://localhost:<port>/WeatherForecast`로 변경).</span><span class="sxs-lookup"><span data-stu-id="91253-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="91253-480">다음과 비슷한 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="91253-481">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="91253-481">Add a model class</span></span>

<span data-ttu-id="91253-482">*모델* 은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="91253-483">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91253-485">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="91253-486">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="91253-487">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="91253-488">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="91253-489">클래스 이름을 *TodoItem* 으로 지정하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="91253-490">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="91253-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91253-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91253-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91253-492">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="91253-493">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91253-494">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91253-495">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-495">Right-click the project.</span></span> <span data-ttu-id="91253-496">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="91253-497">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-497">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="91253-499">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="91253-500">클래스 이름을 *TodoItem* 으로 지정한 다음, **새로 만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="91253-501">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="91253-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="91253-502">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="91253-503">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="91253-504">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="91253-504">Add a database context</span></span>

<span data-ttu-id="91253-505">*데이터베이스 컨텍스트* 는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="91253-506">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="91253-508">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="91253-508">Add NuGet packages</span></span>

* <span data-ttu-id="91253-509">**도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="91253-510">**찾아보기** 탭을 선택한 다음 검색 상자에 **Microsoft.EntityFrameworkCore.SqlServer** 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="91253-511">왼쪽 창에서 **Microsoft.EntityFrameworkCore.SqlServer** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="91253-512">오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="91253-513">앞의 지침을 사용하여 **Microsoft.EntityFrameworkCore.InMemory** NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet 패키지 관리자](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="91253-515">TodoContext 데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="91253-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="91253-516">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="91253-517">클래스 이름을 *TodoContext* 로 지정하고 **추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="91253-518">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="91253-519">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="91253-520">다음 코드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="91253-521">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="91253-521">Register the database context</span></span>

<span data-ttu-id="91253-522">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="91253-523">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="91253-524">*Startup.cs* 를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="91253-525">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="91253-525">The preceding code:</span></span>

* <span data-ttu-id="91253-526">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="91253-527">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="91253-528">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="91253-529">컨트롤러 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="91253-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-530">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91253-531">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="91253-532">**추가** > **스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="91253-533">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러** 를 선택하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="91253-534">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="91253-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="91253-535">**모델 클래스** 에서 **TodoItem(TodoApi.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="91253-536">**데이터 컨텍스트 클래스** 에서 **TodoContext(TodoApi.Models)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="91253-537">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="91253-538">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="91253-539">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="91253-540">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="91253-540">The preceding commands:</span></span>

* <span data-ttu-id="91253-541">스캐폴딩에 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="91253-542">스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다</span><span class="sxs-lookup"><span data-stu-id="91253-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="91253-543">`TodoItemsController`를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="91253-544">생성된 코드는:</span><span class="sxs-lookup"><span data-stu-id="91253-544">The generated code:</span></span>

* <span data-ttu-id="91253-545">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="91253-546">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="91253-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="91253-547">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="91253-548">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="91253-549">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="91253-550">ASP.NET Core 템플릿과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="91253-551">뷰가 있는 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="91253-552">API 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="91253-553">`[action]` 토큰이 경로 템플릿에 없는 경우 경로에서 [작업](xref:mvc/controllers/routing#action) 이름이 제외됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="91253-554">즉, 일치하는 경로에서 작업과 연결된 메서드 이름이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="91253-555">PostTodoItem 만들기 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="91253-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="91253-556">`PostTodoItem`의 return 문이 [nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="91253-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="91253-557">위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="91253-558">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="91253-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="91253-559">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="91253-560"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="91253-561">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="91253-562">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="91253-563">응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="91253-564">`Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="91253-565">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="91253-566">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="91253-567">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="91253-568">Postman을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-568">Install Postman</span></span>

<span data-ttu-id="91253-569">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="91253-570">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="91253-571">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-571">Start the web app.</span></span>
* <span data-ttu-id="91253-572">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-572">Start Postman.</span></span>
* <span data-ttu-id="91253-573">**SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="91253-574">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="91253-575">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="91253-576">Postman을 사용하여 PostTodoItem 테스트</span><span class="sxs-lookup"><span data-stu-id="91253-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="91253-577">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-577">Create a new request.</span></span>
* <span data-ttu-id="91253-578">HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="91253-579">URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="91253-580">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="91253-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="91253-581">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="91253-582">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="91253-583">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="91253-584">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="91253-585">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-585">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="91253-587">Postman을 사용하여 위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="91253-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="91253-588">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="91253-589">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-589">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* <span data-ttu-id="91253-591">HTTP 메서드를 `GET`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="91253-592">URI를 `https://localhost:<port>/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="91253-593">예: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="91253-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="91253-594">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="91253-595">GET 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="91253-595">Examine the GET methods</span></span>

<span data-ttu-id="91253-596">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="91253-597">브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="91253-598">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="91253-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="91253-599">`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="91253-600">Postman을 사용하여 Get 테스트</span><span class="sxs-lookup"><span data-stu-id="91253-600">Test Get with Postman</span></span>

* <span data-ttu-id="91253-601">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-601">Create a new request.</span></span>
* <span data-ttu-id="91253-602">HTTP 메서드를 **GET** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="91253-603">요청 URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="91253-604">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="91253-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="91253-605">Postman에서 **두 개의 창 보기** 를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="91253-606">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-606">Select **Send**.</span></span>

<span data-ttu-id="91253-607">이 앱은 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-607">This app uses an in-memory database.</span></span> <span data-ttu-id="91253-608">앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="91253-609">데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="91253-610">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="91253-610">Routing and URL paths</span></span>

<span data-ttu-id="91253-611">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="91253-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="91253-612">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="91253-613">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="91253-614">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="91253-615">이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems** Controller이므로 컨트롤러 이름은 “TodoItems”입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="91253-616">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="91253-617">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="91253-618">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-618">This sample doesn't use a template.</span></span> <span data-ttu-id="91253-619">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="91253-620">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="91253-621">`GetTodoItem`이 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="91253-622">반환 값</span><span class="sxs-lookup"><span data-stu-id="91253-622">Return values</span></span> 

<span data-ttu-id="91253-623">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="91253-624">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="91253-625">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="91253-626">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="91253-627">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="91253-628">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="91253-629">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="91253-630">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="91253-631">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="91253-632">PutTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="91253-632">The PutTodoItem method</span></span>

<span data-ttu-id="91253-633">다음과 같이 `PutTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="91253-634">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="91253-635">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="91253-636">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="91253-637">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="91253-638">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="91253-639">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="91253-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="91253-640">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="91253-641">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="91253-642">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="91253-643">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="91253-644">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="91253-644">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="91253-646">DeleteTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="91253-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="91253-647">다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="91253-648">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="91253-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="91253-649">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="91253-650">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="91253-651">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="91253-652">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="91253-653">과도한 게시 방지</span><span class="sxs-lookup"><span data-stu-id="91253-653">Prevent over-posting</span></span>

<span data-ttu-id="91253-654">현재 샘플 앱은 전체 `TodoItem` 개체를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="91253-655">일반적으로 프로덕션 앱은 모델의 하위 집합을 사용하여 입력 및 반환되는 데이터를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="91253-656">이 동작에는 여러 가지 이유가 있으며, 보안이 주요 이유 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="91253-657">일반적으로 모델의 하위 집합을 DTO(데이터 전송 개체), 입력 모델 또는 뷰 모델이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="91253-658">이 문서에서는 **DTO** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="91253-659">DTO는 다음과 같은 용도로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-659">A DTO may be used to:</span></span>

* <span data-ttu-id="91253-660">과도한 게시를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-660">Prevent over-posting.</span></span>
* <span data-ttu-id="91253-661">클라이언트에 표시되지 않아야 하는 속성을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="91253-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="91253-662">페이로드 크기를 줄이기 위해 일부 속성을 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="91253-663">중첩된 개체를 포함하는 개체 그래프를 평면화합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="91253-664">클라이언트에는 평면화된 개체 그래프가 더 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="91253-665">DTO 방법을 설명하려면 비밀 필드를 포함하도록 `TodoItem` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="91253-666">이 앱에서는 숨겨진 필드를 숨겨야 하지만, 관리 앱은 숨겨진 필드를 공개할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="91253-667">비밀 필드를 게시하고 가져올 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="91253-668">DTO 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="91253-669">`TodoItemDTO`를 사용하도록 `TodoItemsController`를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="91253-670">비밀 필드를 게시하거나 가져올 수 없음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="91253-671">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="91253-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="91253-672">[자습서: JavaScript로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-javascript)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="91253-673">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="91253-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="91253-674">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-674">Create a web API project.</span></span>
> * <span data-ttu-id="91253-675">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="91253-676">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="91253-676">Add a controller.</span></span>
> * <span data-ttu-id="91253-677">CRUD 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="91253-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="91253-678">라우팅 및 URL 경로 구성</span><span class="sxs-lookup"><span data-stu-id="91253-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="91253-679">반환 값 지정</span><span class="sxs-lookup"><span data-stu-id="91253-679">Specify return values.</span></span>
> * <span data-ttu-id="91253-680">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="91253-681">JavaScript를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="91253-682">작업을 완료하면 웹 API는 관계형 데이터베이스에 저장된 "할 일" 항목을 관리할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="91253-683">개요 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-683">Overview 2.1</span></span>

<span data-ttu-id="91253-684">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="91253-685">API</span><span class="sxs-lookup"><span data-stu-id="91253-685">API</span></span> | <span data-ttu-id="91253-686">설명</span><span class="sxs-lookup"><span data-stu-id="91253-686">Description</span></span> | <span data-ttu-id="91253-687">요청 본문</span><span class="sxs-lookup"><span data-stu-id="91253-687">Request body</span></span> | <span data-ttu-id="91253-688">응답 본문</span><span class="sxs-lookup"><span data-stu-id="91253-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="91253-689">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="91253-689">GET /api/TodoItems</span></span> | <span data-ttu-id="91253-690">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="91253-690">Get all to-do items</span></span> | <span data-ttu-id="91253-691">없음</span><span class="sxs-lookup"><span data-stu-id="91253-691">None</span></span> | <span data-ttu-id="91253-692">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="91253-692">Array of to-do items</span></span>|
|<span data-ttu-id="91253-693">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="91253-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="91253-694">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="91253-694">Get an item by ID</span></span> | <span data-ttu-id="91253-695">없음</span><span class="sxs-lookup"><span data-stu-id="91253-695">None</span></span> | <span data-ttu-id="91253-696">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="91253-696">To-do item</span></span>|
|<span data-ttu-id="91253-697">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="91253-697">POST /api/TodoItems</span></span> | <span data-ttu-id="91253-698">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="91253-698">Add a new item</span></span> | <span data-ttu-id="91253-699">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="91253-699">To-do item</span></span> | <span data-ttu-id="91253-700">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="91253-700">To-do item</span></span> |
|<span data-ttu-id="91253-701">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="91253-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="91253-702">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="91253-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="91253-703">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="91253-703">To-do item</span></span> | <span data-ttu-id="91253-704">없음</span><span class="sxs-lookup"><span data-stu-id="91253-704">None</span></span> |
|<span data-ttu-id="91253-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="91253-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="91253-706">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="91253-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="91253-707">없음</span><span class="sxs-lookup"><span data-stu-id="91253-707">None</span></span> | <span data-ttu-id="91253-708">없음</span><span class="sxs-lookup"><span data-stu-id="91253-708">None</span></span>|

<span data-ttu-id="91253-709">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="91253-709">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="91253-715">필수 구성 요소 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-716">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="91253-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91253-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91253-718">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="91253-719">웹 프로젝트 만들기 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-720">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91253-721">**파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="91253-722">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="91253-723">프로젝트 이름을 *TodoApi* 로 지정하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="91253-724">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 2.2** 가 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="91253-725">**API** 템플릿을 선택하고 **만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="91253-726">**Docker 지원 사용** 을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="91253-726">**Don't** select **Enable Docker Support**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="91253-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91253-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91253-729">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="91253-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="91253-730">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="91253-731">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="91253-732">이러한 명령은 새 웹 API 프로젝트를 만들고 새 프로젝트 폴더에서 Visual Studio Code의 새 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="91253-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="91253-733">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91253-734">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91253-735">**파일** > **새 솔루션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-735">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="91253-737">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="91253-738">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="91253-739">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 2.x **대상 프레임워크** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="91253-740">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-740">Select **Next**.</span></span>

* <span data-ttu-id="91253-741">**프로젝트 이름** 으로 *TodoApi* 를 입력한 다음, **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="91253-743">API 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-743">Test the API 2.1</span></span>

<span data-ttu-id="91253-744">프로젝트 템플릿은 `values` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-744">The project template creates a `values` API.</span></span> <span data-ttu-id="91253-745">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-746">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91253-747">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="91253-748">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/api/values`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="91253-749">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="91253-750">다음으로, **보안 경고** 대화 상자가 나타나면 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91253-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91253-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91253-752">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="91253-753">브라우저에서 `https://localhost:5001/api/values` URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91253-754">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="91253-755">**실행** > **디버깅 시작** 을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="91253-756">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="91253-757">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="91253-758">`/api/values`를 URL에 추가합니다(URL을 `https://localhost:<port>/api/values`로 변경).</span><span class="sxs-lookup"><span data-stu-id="91253-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="91253-759">다음 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="91253-760">모델 클래스 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-760">Add a model class 2.1</span></span>

<span data-ttu-id="91253-761">*모델* 은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="91253-762">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-763">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91253-764">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="91253-765">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="91253-766">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="91253-767">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="91253-768">클래스 이름을 *TodoItem* 으로 지정하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="91253-769">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="91253-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91253-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91253-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91253-771">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="91253-772">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91253-773">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91253-774">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-774">Right-click the project.</span></span> <span data-ttu-id="91253-775">**추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="91253-776">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-776">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="91253-778">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="91253-779">클래스 이름을 *TodoItem* 으로 지정한 다음, **새로 만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="91253-780">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="91253-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="91253-781">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="91253-782">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="91253-783">데이터베이스 컨텍스트 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-783">Add a database context 2.1</span></span>

<span data-ttu-id="91253-784">*데이터베이스 컨텍스트* 는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="91253-785">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-786">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91253-787">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="91253-788">클래스 이름을 *TodoContext* 로 지정하고 **추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="91253-789">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="91253-790">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="91253-791">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="91253-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="91253-792">데이터베이스 컨텍스트 등록 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-792">Register the database context 2.1</span></span>

<span data-ttu-id="91253-793">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="91253-794">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="91253-795">*Startup.cs* 를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="91253-796">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="91253-796">The preceding code:</span></span>

* <span data-ttu-id="91253-797">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="91253-798">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="91253-799">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="91253-800">컨트롤러 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-801">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91253-802">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="91253-803">**추가** > **새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="91253-804">**새 항목 추가** 대화 상자에서 **API 컨트롤러 클래스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="91253-805">클래스 이름을 *TodoController* 로 지정하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![검색 상자의 컨트롤러 및 웹 API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="91253-807">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="91253-808">*Controllers* 폴더에 `TodoController`라는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="91253-809">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="91253-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="91253-810">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="91253-810">The preceding code:</span></span>

* <span data-ttu-id="91253-811">메서드 없이 API 컨트롤러 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="91253-812">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="91253-813">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="91253-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="91253-814">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="91253-815">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="91253-816">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="91253-817">데이터베이스가 비어 있는 경우 데이터베이스에 `Item1`이라는 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="91253-818">이 코드는 생성자에 위치하므로 새 HTTP 요청이 발생할 때마다 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="91253-819">모든 항목을 삭제하면 생성자는 다음에 API가 호출될 경우 `Item1`을 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="91253-820">따라서 실제로 작동되는 경우 삭제가 작동하지 않는 것처럼 보일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="91253-821">GET 메서드 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-821">Add Get methods 2.1</span></span>

<span data-ttu-id="91253-822">할 일 항목을 가져오는 API를 제공하려면 다음 메서드를 `TodoController` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="91253-823">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="91253-824">앱이 계속 실행되고 있으면 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-824">Stop the app if it's still running.</span></span> <span data-ttu-id="91253-825">그런 다음, 다시 실행하여 최신 변경 내용을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="91253-826">브라우저에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="91253-827">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="91253-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="91253-828">`GetTodoItems`를 호출하여 다음 HTTP 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="91253-829">라우팅 및 URL 경로 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="91253-830">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="91253-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="91253-831">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="91253-832">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="91253-833">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="91253-834">이 샘플의 경우 컨트롤러 클래스 이름은 **Todo** Controller이므로 컨트롤러 이름은 "todo"입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="91253-835">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="91253-836">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="91253-837">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-837">This sample doesn't use a template.</span></span> <span data-ttu-id="91253-838">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="91253-839">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="91253-840">`GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="91253-841">반환 값 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-841">Return values 2.1</span></span>

<span data-ttu-id="91253-842">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="91253-843">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="91253-844">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="91253-845">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="91253-846">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="91253-847">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="91253-848">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="91253-849">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="91253-850">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="91253-851">GetTodoItems 메서드 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="91253-852">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="91253-853">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="91253-854">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-854">Start the web app.</span></span>
* <span data-ttu-id="91253-855">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-855">Start Postman.</span></span>
* <span data-ttu-id="91253-856">**SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91253-857">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91253-858">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="91253-859">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91253-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="91253-860">**Postman** > **기본 설정**(**일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="91253-861">또는 렌치를 선택하고 **설정** 을 선택한 다음 SSL 인증서 확인을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="91253-862">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="91253-863">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-863">Create a new request.</span></span>
  * <span data-ttu-id="91253-864">HTTP 메서드를 **GET** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="91253-865">요청 URI를 `https://localhost:<port>/api/todo`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="91253-866">예: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="91253-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="91253-867">Postman에서 **두 개의 창 보기** 를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="91253-868">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-868">Select **Send**.</span></span>

![Get 요청이 있는 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="91253-870">Create 메서드 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-870">Add a Create method 2.1</span></span>

<span data-ttu-id="91253-871">*Controllers/TodoController.cs* 내부에 다음 `PostTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="91253-872">위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="91253-873">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="91253-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="91253-874">`CreatedAtAction` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="91253-875">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="91253-876">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="91253-877">`Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="91253-878">`Location` 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="91253-879">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="91253-880">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="91253-881">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="91253-882">PostTodoItem 메서드 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="91253-883">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-883">Build the project.</span></span>
* <span data-ttu-id="91253-884">Postman에서 HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="91253-885">URI를 `https://localhost:<port>/api/Todo`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="91253-886">예: `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="91253-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="91253-887">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="91253-888">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="91253-889">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="91253-890">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="91253-891">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-891">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="91253-893">405 메서드가 허용되지 않음 오류가 발생할 경우 `PostTodoItem` 메서드를 추가한 후에 프로젝트를 컴파일하지 않아서 발생한 결과일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="91253-894">위치 헤더 URI 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="91253-895">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="91253-896">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-896">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/pmc2.png)

* <span data-ttu-id="91253-898">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-898">Set the method to GET.</span></span>
* <span data-ttu-id="91253-899">URI를 `https://localhost:<port>/api/TodoItems/2`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="91253-900">예: `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="91253-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="91253-901">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="91253-902">PutTodoItem 메서드 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="91253-903">다음 `PutTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="91253-904">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="91253-905">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="91253-906">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="91253-907">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="91253-908">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="91253-909">PutTodoItem 메서드 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="91253-910">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="91253-911">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="91253-912">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="91253-913">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="91253-914">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="91253-914">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="91253-916">DeleteTodoItem 메서드 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="91253-917">다음 `DeleteTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="91253-918">`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="91253-919">DeleteTodoItem 메서드 테스트 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="91253-920">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="91253-921">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="91253-922">삭제할 개체의 URI를 설정합니다(예: `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="91253-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="91253-923">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-923">Select **Send**.</span></span>

<span data-ttu-id="91253-924">샘플 앱을 사용하면 모든 항목을 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="91253-925">하지만 마지막 항목이 삭제되면 다음에 API를 호출하는 경우 모델 클래스 생성자에서 새로운 항목이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="91253-926">JavaScript를 사용하여 웹 API 호출 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="91253-927">이 섹션에는 JavaScript를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="91253-928">jQuery가 요청을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-928">jQuery initiates the request.</span></span> <span data-ttu-id="91253-929">JavaScript는 웹 API 응답의 세부 정보를 토대로 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="91253-930">다음 강조 표시된 코드로 *Startup.cs* 를 업데이트하여 앱이 [정적 파일을 제공](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)하고 [기본 파일 매핑을 사용](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="91253-931">프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91253-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="91253-932">*index.html* 이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="91253-933">다음 표시로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="91253-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="91253-934">*site.js* 라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="91253-935">다음 코드로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="91253-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="91253-936">HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="91253-937">*Properties\launchSettings.json* 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="91253-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="91253-938">`launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="91253-939">이 샘플은 웹 API의 CRUD 메서드를 모두 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="91253-940">API 호출에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="91253-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="91253-941">할 일 항목의 목록 가져오기 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="91253-942">jQuery는 할 일 항목의 배열을 나타내는 JSON을 반환하는 웹 API에 HTTP GET 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="91253-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="91253-943">요청이 성공하면 `success` 콜백 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="91253-944">콜백에서 DOM은 할 일 정보로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="91253-945">할 일 항목 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="91253-946">jQuery는 요청 본문에 있는 할 일 항목을 사용하여 HTTP POST 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="91253-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="91253-947">`accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="91253-948">[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="91253-949">API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="91253-950">할 일 항목 업데이트 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="91253-951">할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="91253-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="91253-952">`url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.</span><span class="sxs-lookup"><span data-stu-id="91253-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="91253-953">할 일 항목 삭제 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="91253-954">할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91253-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="91253-955">웹 API에 인증 지원 추가 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="91253-956">추가 리소스 2.1</span><span class="sxs-lookup"><span data-stu-id="91253-956">Additional resources 2.1</span></span>

<span data-ttu-id="91253-957">[이 자습서에서 샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="91253-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="91253-958">[다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="91253-959">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91253-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="91253-960">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="91253-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
