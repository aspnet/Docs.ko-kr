---
title: '자습서: JavaScript로 ASP.NET Core 웹 API 호출하기'
author: rick-anderson
description: JavaScript를 사용하여 ASP.NET Core 웹 API를 호출하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: b41288bd63267a9aa7035e25ebc8d838eed5d93b
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690693"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="6b32d-103">자습서: JavaScript로 ASP.NET Core 웹 API 호출하기</span><span class="sxs-lookup"><span data-stu-id="6b32d-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="6b32d-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6b32d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6b32d-105">이 자습서에서는 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)를 사용하여 JavaScript로 ASP.NET Core 웹 API를 호출하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6b32d-106">ASP.NET Core 2.2의 경우에는 [JavaScript를 사용하여 웹 API 호출하기](xref:tutorials/first-web-api#call-the-web-api-with-javascript) 버전 2.2를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6b32d-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="6b32d-107">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="6b32d-107">Prerequisites</span></span>

* <span data-ttu-id="6b32d-108">[자습서: 웹 API 만들기](xref:tutorials/first-web-api) 완료</span><span class="sxs-lookup"><span data-stu-id="6b32d-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="6b32d-109">CSS, HTML, JavaScript에 대한 지식</span><span class="sxs-lookup"><span data-stu-id="6b32d-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="6b32d-110">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="6b32d-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="6b32d-111">이 섹션에서는 할 일 항목의 생성과 관리를 위한 양식이 포함된 HTML 페이지를 추가하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="6b32d-112">이벤트 처리기는 페이지의 요소에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="6b32d-113">이벤트 처리기에 의해 웹 API의 작업 메서드에 대한 HTTP 요청이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="6b32d-114">Fetch API의 `fetch` 함수는 각 HTTP 요청을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="6b32d-115">`fetch` 함수는 [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) 개체를 반환하는데, 해당 개체에는 `Response` 개체로 나타나는 HTTP 응답이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="6b32d-116">일반적인 패턴은 `Response` 개체에 대해 `json` 함수를 호출하여 JSON 응답 본문을 추출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="6b32d-117">JavaScript는 웹 API 응답의 세부 정보를 토대로 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="6b32d-118">가장 간단한 `fetch` 호출에서는 경로를 나타내는 단일 매개 변수가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="6b32d-119">`init` 개체라고 하는 두 번째 매개 변수는 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="6b32d-120">`init`은 HTTP 요청 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="6b32d-121">[정적 파일을 제공](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하고 [기본 파일 매핑을 사용](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="6b32d-122">다음의 강조 표시된 코드는 *Startup.cs* 의 `Configure` 메서드에서 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-122">The following highlighted code is needed in the `Configure` method of *Startup.cs* :</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="6b32d-123">프로젝트 루트에서 *wwwroot* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="6b32d-124">*wwwroot* 폴더 안에 *js* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="6b32d-125">*index.html* 이라는 HTML 파일을 *wwwroot* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="6b32d-126">*index.html* 의 콘텐츠를 다음 마크업으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="6b32d-127">*site.js* 라는 JavaScript 파일을 *wwwroot/js* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-127">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="6b32d-128">*site.js* 의 콘텐츠를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-128">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="6b32d-129">HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-129">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="6b32d-130">*Properties\launchSettings.json* 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-130">Open *Properties\launchSettings.json* .</span></span>
1. <span data-ttu-id="6b32d-131">`launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-131">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="6b32d-132">이 샘플은 웹 API의 CRUD 메서드를 모두 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-132">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="6b32d-133">웹 API 요청에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-133">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="6b32d-134">할 일 항목의 목록 가져오기</span><span class="sxs-lookup"><span data-stu-id="6b32d-134">Get a list of to-do items</span></span>

<span data-ttu-id="6b32d-135">다음 코드에서는 HTTP GET 요청이 *api/TodoItems* 경로로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-135">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="6b32d-136">웹 API가 성공한 상태 코드를 반환하면 `_displayItems` 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-136">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="6b32d-137">`_displayItems`에서 허용하는 배열 매개 변수의 각 할 일 항목은 **편집** 및 **삭제** 단추가 포함된 테이블로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-137">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="6b32d-138">웹 API 요청이 실패하는 경우 브라우저의 콘솔에 오류가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-138">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="6b32d-139">할 일 항목 추가</span><span class="sxs-lookup"><span data-stu-id="6b32d-139">Add a to-do item</span></span>

<span data-ttu-id="6b32d-140">다음 코드에서:</span><span class="sxs-lookup"><span data-stu-id="6b32d-140">In the following code:</span></span>

* <span data-ttu-id="6b32d-141">`item` 변수는 할 일 항목의 개체 리터럴 표현을 생성하기 위해 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-141">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="6b32d-142">Fetch 요청은 다음 옵션으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-142">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="6b32d-143">`method` - POST HTTP 작업 동사를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-143">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="6b32d-144">`body` - 요청 본문의 JSON 표현을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-144">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="6b32d-145">JSON은 `item`에 저장된 개체 리터럴을 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 함수로 전달하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-145">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="6b32d-146">`headers` - `Accept` 및 `Content-Type` HTTP 요청 헤더를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-146">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="6b32d-147">두 헤더 모두 `application/json`으로 설정되어 개별적으로 수신 및 전송되는 미디어 유형을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-147">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="6b32d-148">HTTP POST 요청은 *api/TodoItems* 경로에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-148">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="6b32d-149">웹 API가 성공적인 상태 코드를 반환하면 `getItems` 함수가 호출되어 HTML 테이블을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-149">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="6b32d-150">웹 API 요청이 실패하는 경우 브라우저의 콘솔에 오류가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-150">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="6b32d-151">할 일 항목 업데이트</span><span class="sxs-lookup"><span data-stu-id="6b32d-151">Update a to-do item</span></span>

<span data-ttu-id="6b32d-152">할 일 항목 업데이트는 항목 추가와 비슷하지만 크게 두 가지에서 차이를 보입니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-152">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="6b32d-153">경로는 업데이트할 항목의 고유 식별자를 접미사로 가집니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-153">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="6b32d-154">예를 들면 *api/TodoItems/1* 과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-154">For example, *api/TodoItems/1* .</span></span>
* <span data-ttu-id="6b32d-155">HTTP 작업 동사는 `method` 옵션으로 표시되는 바와 같이 PUT입니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-155">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="6b32d-156">할 일 항목 삭제</span><span class="sxs-lookup"><span data-stu-id="6b32d-156">Delete a to-do item</span></span>

<span data-ttu-id="6b32d-157">할 일 항목을 삭제하려면 요청의 `method` 옵션을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-157">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="6b32d-158">웹 API 도움말 페이지를 생성하는 방법을 알아보려면 다음 자습서로 계속 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="6b32d-158">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
