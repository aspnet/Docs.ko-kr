---
title: 2부. ASP.NET Core MVC 앱에 컨트롤러 추가
author: rick-anderson
description: ASP.NET Core MVC에 대한 자습서 시리즈의 2부입니다.
ms.author: riande
ms.date: 01/23/2021
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
uid: tutorials/first-mvc-app/adding-controller
ms.custom: contperf-fy21q3
ms.openlocfilehash: 47bb9b96bd5565a3a67f3cbdf9a4b6bc1f987447
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975263"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="18e66-103">2부. ASP.NET Core MVC 앱에 컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="18e66-103">Part 2, add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="18e66-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="18e66-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18e66-105">MVC( Model-View-Controller) 아키텍처 패턴은 다음 세 가지 주요 구성 요소로 앱을 구분합니다. **M** odel, **V** iew 및 **C** ontroller.</span><span class="sxs-lookup"><span data-stu-id="18e66-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="18e66-106">MVC 패턴을 통해 기존 모놀리식 응용 프로그램보다 쉽게 테스트 가능하고 업데이트할 수 있는 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span>

<span data-ttu-id="18e66-107">MVC 기반 앱에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="18e66-108">**M** odels: 앱의 데이터를 나타내는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-108">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="18e66-109">모델 클래스는 유효성 검사 논리를 사용하여 해당 데이터에 대한 비즈니스 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="18e66-110">일반적으로 모델 개체는 데이터베이스에서 모델 상태를 검색하고 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="18e66-111">이 자습서에서 `Movie` 모델은 데이터베이스에서 영화 데이터를 검색하고 이를 뷰에 제공하거나 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="18e66-112">수정된 데이터는 데이터베이스에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-112">Updated data is written to a database.</span></span>
* <span data-ttu-id="18e66-113">**V** iews: 보기는 앱의 UI(사용자 인터페이스)를 표시하는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-113">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="18e66-114">일반적으로 UI는 모델 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-114">Generally, this UI displays the model data.</span></span>
* <span data-ttu-id="18e66-115">**C** ontrollers(컨트롤러): 다음을 수행하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-115">**C** ontrollers: Classes that:</span></span>
  * <span data-ttu-id="18e66-116">브라우저 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-116">Handle browser requests.</span></span>
  * <span data-ttu-id="18e66-117">모델 데이터를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-117">Retrieve model data.</span></span>
  * <span data-ttu-id="18e66-118">응답을 반환하는 뷰 템플릿을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-118">Call view templates that return a response.</span></span>

<span data-ttu-id="18e66-119">MVC 앱에서는 뷰에 정보만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-119">In an MVC app, the view only displays information.</span></span> <span data-ttu-id="18e66-120">컨트롤러는 사용자 입력 및 상호 작용을 처리하고 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-120">The controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="18e66-121">예를 들어 컨트롤러는 URL 세그먼트 및 쿼리 문자열 값을 처리하고 이러한 값을 모델에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-121">For example, the controller handles URL segments and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="18e66-122">모델은 이러한 값을 사용하여 데이터베이스를 쿼리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-122">The model might use these values to query the database.</span></span> <span data-ttu-id="18e66-123">다음은 그 예입니다. </span><span class="sxs-lookup"><span data-stu-id="18e66-123">For example:</span></span>

* <span data-ttu-id="18e66-124">`Https://localhost:5001/Home/Privacy`: `Home` 컨트롤러 및 `Privacy` 작업을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-124">`Https://localhost:5001/Home/Privacy`: specifies the `Home`  controller  and the `Privacy` action.</span></span>
* <span data-ttu-id="18e66-125">`Https://localhost:5001/Movies/Edit/5`: `Movies` 컨트롤러 및 `Edit` 작업을 사용하여 ID가 5인 동영상을 편집하는 요청으로, 자습서의 뒷부분에서 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-125">`Https://localhost:5001/Movies/Edit/5`: is a request to edit the movie with ID=5 using the `Movies` controller and the `Edit` action, which are detailed later in the tutorial.</span></span>

<span data-ttu-id="18e66-126">경로 데이터는 자습서의 뒷 부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-126">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="18e66-127">MVC 아키텍처 패턴은 앱을 모델, 뷰 및 컨트롤러의 세 가지 주요 구성 요소로 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-127">The MVC architectural pattern separates an app into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="18e66-128">이 패턴은 문제의 분리를 달성하는 데 도움이 됩니다. UI 논리는 뷰에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-128">This pattern helps to achieve separation of concerns: The UI logic belongs in the view.</span></span> <span data-ttu-id="18e66-129">입력 논리는 컨트롤러에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-129">Input logic belongs in the controller.</span></span> <span data-ttu-id="18e66-130">비즈니스 논리는 모델에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-130">Business logic belongs in the model.</span></span> <span data-ttu-id="18e66-131">이렇게 분리하면 다른 코드에 영향을 주지 않고 한 번에 한 구현 측면에서 작업할 수 있기 때문에 앱을 작성할 때 복잡성을 관리하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-131">This separation helps manage complexity when building an app, because it enables work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="18e66-132">예를 들어 비즈니스 논리 코드와 무관하게 보기 코드를 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-132">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="18e66-133">이 자습서 시리즈에서는 동영상 앱을 빌드하면서 이러한 개념을 소개하고 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-133">These concepts are introduced and demonstrated in this tutorial series while building a movie app.</span></span> <span data-ttu-id="18e66-134">MVC 프로젝트는 *컨트롤러* 및 *보기* 를 위한 폴더를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-134">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="18e66-135">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="18e66-135">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="18e66-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18e66-136">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="18e66-137">**솔루션 탐색기** 에서 **컨트롤러 > 추가 > 컨트롤러** 를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-137">In the **Solution Explorer**, right-click **Controllers > Add > Controller**.</span></span>

![솔루션 탐색기, 컨트롤러 > 추가 > 컨트롤러를 마우스 오른쪽 단추로 클릭](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)

<span data-ttu-id="18e66-139">**스캐폴드 추가** 대화 상자에서 **MVC 컨트롤러 - 비어 있음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-139">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**.</span></span>

![MVC 컨트롤러 추가 및 이름 지정](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

<span data-ttu-id="18e66-141">**새 항목 추가 - MvcMovie 대화 상자** 에서 **HelloWorldController.cs** 를 입력하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-141">In the **Add New Item - MvcMovie dialog**, enter **HelloWorldController.cs** and select **Add**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="18e66-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="18e66-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="18e66-143">**탐색기** 아이콘을 선택한 다음, **컨트롤러 > 새 파일** 을 컨트롤 클릭(마우스 오른쪽 단추로 클릭)하고 새 파일의 이름을 *HelloWorldController.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-143">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

![상황에 맞는 메뉴](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="18e66-145">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18e66-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="18e66-146">**솔루션 탐색기** 에서 **컨트롤러 > 추가 > 새 파일** 을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-146">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![상황에 맞는 메뉴](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="18e66-148">**ASP.NET Core** 및 **컨트롤러 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-148">Select **ASP.NET Core** and **Controller Class**.</span></span>

<span data-ttu-id="18e66-149">컨트롤러의 이름을 **HelloWorldController** 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-149">Name the controller **HelloWorldController**.</span></span>

![MVC 컨트롤러 추가 및 이름 지정](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="18e66-151">*Controllers/HelloWorldController.cs* 의 내용을 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-151">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="18e66-152">컨트롤러의 모든 `public` 메서드는 HTTP 엔드포인트로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-152">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="18e66-153">위의 샘플에서 메서드는 모두 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-153">In the sample above, both methods return a string.</span></span> <span data-ttu-id="18e66-154">각 메서드 앞의 주석에 주목하세요.</span><span class="sxs-lookup"><span data-stu-id="18e66-154">Note the comments preceding each method.</span></span>

<span data-ttu-id="18e66-155">HTTP 엔드포인트:</span><span class="sxs-lookup"><span data-stu-id="18e66-155">An HTTP endpoint:</span></span>

* <span data-ttu-id="18e66-156">웹 애플리케이션에서 대상으로 지정 가능한 URL(예: `https://localhost:5001/HelloWorld`)입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-156">Is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`.</span></span>
* <span data-ttu-id="18e66-157">다음을 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-157">Combines:</span></span>
  * <span data-ttu-id="18e66-158">사용된 프로토콜: `HTTPS`.</span><span class="sxs-lookup"><span data-stu-id="18e66-158">The protocol used: `HTTPS`.</span></span>
  * <span data-ttu-id="18e66-159">TCP 포트를 포함한 웹 서버의 네트워크 위치: `localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="18e66-159">The network location of the web server, including the TCP port: `localhost:5001`.</span></span>
  * <span data-ttu-id="18e66-160">대상 URI: `HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="18e66-160">The target URI: `HelloWorld`.</span></span>

<span data-ttu-id="18e66-161">첫 번째 주석은 이 항목이 기본 URL에 `/HelloWorld/`를 추가하여 호출되는 [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) 메서드라고 명시합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-161">The first comment states this is an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span>

<span data-ttu-id="18e66-162">두 번째 주석은 URL에 `/HelloWorld/Welcome/`을 추가하여 호출되는 [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) 메서드를 명시합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-162">The second comment specifies an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="18e66-163">자습서 뒷부분에서는 스캐폴딩 엔진을 사용하여 데이터를 업데이트하는 `HTTP POST` 메서드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-163">Later on in the tutorial, the scaffolding engine is used to generate `HTTP POST` methods, which update data.</span></span>

<span data-ttu-id="18e66-164">디버거 없이 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-164">Run the app without the debugger.</span></span>

<span data-ttu-id="18e66-165">주소 표시줄의 경로에 “HelloWorld”를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-165">Append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="18e66-166">`Index` 메서드가 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-166">The `Index` method returns a string.</span></span>

![This is my default action이라는 앱 응답을 보여 주는 브라우저 창](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="18e66-168">MVC는 들어오는 URL에 따라 컨트롤러 클래스와 해당 클래스 내의 작업 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-168">MVC invokes controller classes, and the action methods within them, depending on the incoming URL.</span></span> <span data-ttu-id="18e66-169">MVC에서 사용하는 기본 [URL 라우팅 논리](xref:mvc/controllers/routing)는 다음과 같은 형식을 사용하여 호출할 코드를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-169">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC, uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="18e66-170">라우팅 서식은 *Startup.cs* 파일의 `Configure` 메서드에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-170">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="18e66-171">앱으로 이동할 때 URL 세그먼트를 제공하지 않으면 위에 강조 표시된 템플릿 줄에 지정된 "Home" 컨트롤러 및 "Index" 메서드가 기본값으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-171">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>  <span data-ttu-id="18e66-172">위 URL 세그먼트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-172">In the preceding URL segments:</span></span>

* <span data-ttu-id="18e66-173">첫 번째 URL 세그먼트는 실행할 컨트롤러 클래스를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-173">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="18e66-174">따라서 `localhost:5001/HelloWorld`는 **HelloWorld** Controller 클래스에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-174">So `localhost:5001/HelloWorld` maps to the **HelloWorld** Controller class.</span></span>
* <span data-ttu-id="18e66-175">URL 세그먼트의 두 번째 부분은 클래스의 작업 메서드를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-175">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="18e66-176">따라서 `localhost:5001/HelloWorld/Index`는 `HelloWorldController` 클래스의 `Index` 메서드가 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-176">So `localhost:5001/HelloWorld/Index` causes the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="18e66-177">`localhost:5001/HelloWorld`로만 이동했음에도 기본적으로 `Index` 메서드가 호출되었음에 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="18e66-177">Notice that you only had to browse to `localhost:5001/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="18e66-178">`Index`는 메서드 이름이 명시적으로 지정되지 않은 경우 컨트롤러에서 호출되는 기본 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-178">`Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span>
* <span data-ttu-id="18e66-179">URL 세그먼트의 세 번째 부분(`id`)은 경로 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-179">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="18e66-180">경로 데이터는 자습서의 뒷 부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-180">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="18e66-181">`https://localhost:{PORT}/HelloWorld/Welcome`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-181">Browse to: `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="18e66-182">`{PORT}`를 포트 번호로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-182">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="18e66-183">`Welcome` 메서드가 실행되고 문자열 `This is the Welcome action method...`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-183">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="18e66-184">이 URL의 경우 컨트롤러는 `HelloWorld`이고 `Welcome`이 작업 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-184">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="18e66-185">아직 URL의 `[Parameters]` 부분을 사용하지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-185">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![브라우저 창에 This is the Welcome action method라는 애플리케이션 응답이 표시됩니다.](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="18e66-187">URL에서 컨트롤러에 일부 매개 변수 정보를 전달하도록 코드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-187">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="18e66-188">예: `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="18e66-188">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span>

<span data-ttu-id="18e66-189">다음 코드와 같이 `Welcome` 메서드가 두 개의 매개 변수를 포함하도록 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-189">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="18e66-190">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="18e66-190">The preceding code:</span></span>

* <span data-ttu-id="18e66-191">C#의 선택적 매개 변수 기능을 사용하여 `numTimes` 매개 변수에 대해 전달된 값이 없는 경우 해당 매개 변수의 기본값이 1임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-191">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span>
* <span data-ttu-id="18e66-192">`HtmlEncoder.Default.Encode`를 사용하여 JavaScript 사용과 같은 악의적인 입력으로부터 앱을 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-192">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input, such as through JavaScript.</span></span>
* <span data-ttu-id="18e66-193">`$"Hello {name}, NumTimes is: {numTimes}"`에서 [보간된 문자열](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-193">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span>

<span data-ttu-id="18e66-194">앱을 실행하고 `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-194">Run the app and browse to: `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="18e66-195">`{PORT}`를 포트 번호로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-195">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="18e66-196">URL에서 `name` 및 `numtimes`에 다른 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-196">Try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="18e66-197">MVC [모델 바인딩](xref:mvc/models/model-binding) 시스템은 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수를 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-197">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string to parameters in the method.</span></span> <span data-ttu-id="18e66-198">자세한 정보는 [모델 바인딩](xref:mvc/models/model-binding)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="18e66-198">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Hello Rick, NumTimes의 애플리케이션 응답을 보여주는 브라우저 창\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="18e66-200">위 이미지는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-200">In the previous image:</span></span>

* <span data-ttu-id="18e66-201">URL 세그먼트 `Parameters`가 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-201">The URL segment `Parameters` isn't used.</span></span>
* <span data-ttu-id="18e66-202">`name` 및 `numTimes` 매개 변수는 [쿼리 문자열](https://wikipedia.org/wiki/Query_string)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-202">The `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span>
* <span data-ttu-id="18e66-203">위 URL에서 `?`(물음표)는 구분 기호이며, 다음에 쿼리 문자열이 옵니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-203">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span>
* <span data-ttu-id="18e66-204">`&` 문자는 필드-값 쌍을 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-204">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="18e66-205">`Welcome` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-205">Replace the `Welcome` method with the following code:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="18e66-206">앱을 실행하고 `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick` URL을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-206">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="18e66-207">위 URL은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-207">In the preceding URL:</span></span>

* <span data-ttu-id="18e66-208">세 번째 URL 세그먼트는 경로 매개 변수 `id`와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-208">The third URL segment matched the route parameter `id`.</span></span> 
* <span data-ttu-id="18e66-209">`Welcome` 메서드는 `MapControllerRoute` 메서드의 URL 템플릿과 일치하는 `id` 매개 변수를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-209">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="18e66-210">후행 `?`(`id?`의)는 `id` 매개 변수가 선택 사항임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-210">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?name=snippet_route&highlight=5)]

<span data-ttu-id="18e66-211">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="18e66-211">In the preceding example:</span></span>

* <span data-ttu-id="18e66-212">세 번째 URL 세그먼트는 경로 매개 변수 `id`와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-212">The third URL segment matched the route parameter `id`.</span></span>
* <span data-ttu-id="18e66-213">`Welcome` 메서드는 `MapControllerRoute` 메서드의 URL 템플릿과 일치하는 `id` 매개 변수를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-213">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="18e66-214">후행 `?`(`id?`의)는 `id` 매개 변수가 선택 사항임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-214">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="18e66-215">[이전: 시작하기](start-mvc.md)
> [다음: 뷰 추가](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="18e66-215">[Previous: Get Started](start-mvc.md)
[Next: Add a View](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="18e66-216">MVC( Model-View-Controller) 아키텍처 패턴은 다음 세 가지 주요 구성 요소로 앱을 구분합니다. **M** odel, **V** iew 및 **C** ontroller.</span><span class="sxs-lookup"><span data-stu-id="18e66-216">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="18e66-217">MVC 패턴을 통해 기존 모놀리식 응용 프로그램보다 쉽게 테스트 가능하고 업데이트할 수 있는 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-217">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="18e66-218">MVC 기반 앱에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-218">MVC-based apps contain:</span></span>

* <span data-ttu-id="18e66-219">**M** odels: 앱의 데이터를 나타내는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-219">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="18e66-220">모델 클래스는 유효성 검사 논리를 사용하여 해당 데이터에 대한 비즈니스 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-220">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="18e66-221">일반적으로 모델 개체는 데이터베이스에서 모델 상태를 검색하고 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-221">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="18e66-222">이 자습서에서 `Movie` 모델은 데이터베이스에서 영화 데이터를 검색하고 이를 뷰에 제공하거나 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-222">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="18e66-223">수정된 데이터는 데이터베이스에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-223">Updated data is written to a database.</span></span>

* <span data-ttu-id="18e66-224">**V** iews: 보기는 앱의 UI(사용자 인터페이스)를 표시하는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-224">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="18e66-225">일반적으로 UI는 모델 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-225">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="18e66-226">**C** ontrollers: 브라우저 요청을 처리하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-226">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="18e66-227">모델 데이터를 검색하고 응답을 반환하는 보기 템플릿을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-227">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="18e66-228">MVC 앱에서 보기는 단지 정보만 표시하며, 사용자 입력 및 상호 작용은 컨트롤러가 처리하고 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-228">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="18e66-229">예를 들어 컨트롤러는 경로 데이터 및 쿼리 문자열 값을 처리하고 모델에 이러한 값을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-229">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="18e66-230">모델은 이러한 값을 사용하여 데이터베이스를 쿼리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-230">The model might use these values to query the database.</span></span> <span data-ttu-id="18e66-231">예를 들어 `https://localhost:5001/Home/About`에는 `Home`(컨트롤러) 및 `About`(호출할 홈 컨트롤러의 작업 메서드)라는 경로 데이터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-231">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="18e66-232">`https://localhost:5001/Movies/Edit/5`는 영화 컨트롤러를 사용하여 ID=5인 영화를 편집하기 위한 요청입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-232">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="18e66-233">경로 데이터는 자습서의 뒷 부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-233">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="18e66-234">MVC 패턴을 사용하면 앱의 다양한 측면(입력 논리, 비즈니스 논리 및 UI 논리)을 분리하면서 이러한 요소 간에 느슨한 결합을 제공하는 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-234">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="18e66-235">이 패턴은 각 종류의 논리가 앱에 있어야 하는 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-235">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="18e66-236">UI 논리는 보기에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-236">The UI logic belongs in the view.</span></span> <span data-ttu-id="18e66-237">입력 논리는 컨트롤러에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-237">Input logic belongs in the controller.</span></span> <span data-ttu-id="18e66-238">비즈니스 논리는 모델에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-238">Business logic belongs in the model.</span></span> <span data-ttu-id="18e66-239">이렇게 분리하면 다른 코드에 영향을 주지 않고 한 번에 한 구현 측면에서 작업할 수 있기 때문에 앱을 만들 때 복잡성을 관리하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-239">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="18e66-240">예를 들어 비즈니스 논리 코드와 무관하게 보기 코드를 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-240">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="18e66-241">이 자습서 시리즈에서는 이러한 개념을 설명하고 영화 앱을 만들기 위해 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-241">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="18e66-242">MVC 프로젝트는 *컨트롤러* 및 *보기* 를 위한 폴더를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-242">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="18e66-243">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="18e66-243">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="18e66-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18e66-244">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="18e66-245">**솔루션 탐색기** 에서 **컨트롤러 > 추가 > 컨트롤러** 를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-245">In **Solution Explorer**, right-click **Controllers > Add > Controller**</span></span>

  ![상황에 맞는 메뉴](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* <span data-ttu-id="18e66-247">**스캐폴드 추가** 대화 상자에서 **MVC 컨트롤러 - 비어 있음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-247">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![MVC 컨트롤러 추가 및 이름 지정](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* <span data-ttu-id="18e66-249">**빈 MVC 컨트롤러 추가 대화 상자** 에 **HelloWorldController** 를 입력하고 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-249">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="18e66-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="18e66-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="18e66-251">**탐색기** 아이콘을 선택한 다음, **컨트롤러 > 새 파일** 을 컨트롤 클릭(마우스 오른쪽 단추로 클릭)하고 새 파일의 이름을 *HelloWorldController.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-251">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![상황에 맞는 메뉴](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="18e66-253">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18e66-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="18e66-254">**솔루션 탐색기** 에서 **컨트롤러 > 추가 > 새 파일** 을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-254">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![상황에 맞는 메뉴](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="18e66-256">**ASP.NET Core** 및 **MVC 컨트롤러 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-256">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="18e66-257">컨트롤러의 이름을 **HelloWorldController** 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-257">Name the controller **HelloWorldController**.</span></span>

![MVC 컨트롤러 추가 및 이름 지정](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="18e66-259">*Controllers/HelloWorldController.cs* 의 내용을 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-259">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="18e66-260">컨트롤러의 모든 `public` 메서드는 HTTP 엔드포인트로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-260">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="18e66-261">위의 샘플에서 메서드는 모두 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-261">In the sample above, both methods return a string.</span></span> <span data-ttu-id="18e66-262">각 메서드 앞의 주석에 주목하세요.</span><span class="sxs-lookup"><span data-stu-id="18e66-262">Note the comments preceding each method.</span></span>

<span data-ttu-id="18e66-263">HTTP 엔드포인트는 웹 응용 프로그램에서 대상으로 지정 가능한 URL(예: `https://localhost:5001/HelloWorld`)로써, 사용되는 프로토콜(`HTTPS`), 웹 서버의 네트워크 위치(`localhost:5001`, TCP 포트 포함) 및 대상 URI인 `HelloWorld`를 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-263">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="18e66-264">첫 번째 주석은 이 항목이 기본 URL에 `/HelloWorld/`를 추가하여 호출되는 [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) 메서드라고 명시합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-264">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="18e66-265">두 번째 주석은 URL에 `/HelloWorld/Welcome/`을 추가하여 호출되는 [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 메서드를 명시합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-265">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="18e66-266">나중에 이 자습서에서는 스캐폴딩 엔진을 사용하여 데이터를 수정하는 `HTTP POST` 메서드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-266">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="18e66-267">비 디버그 모드로 앱을 실행하고 주소 표시줄의 경로에 "HelloWorld"를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-267">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="18e66-268">`Index` 메서드가 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-268">The `Index` method returns a string.</span></span>

![브라우저 창에 This is my default action이라는 애플리케이션 응답이 표시됩니다.](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="18e66-270">MVC는 들어오는 URL에 따라 컨트롤러 클래스(및 해당 클래스의 작업 메서드)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-270">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="18e66-271">MVC에서 사용하는 기본 [URL 라우팅 논리](xref:mvc/controllers/routing)는 다음과 같은 형식을 사용하여 호출할 코드를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-271">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="18e66-272">라우팅 서식은 *Startup.cs* 파일의 `Configure` 메서드에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-272">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="18e66-273">앱으로 이동할 때 URL 세그먼트를 제공하지 않으면 위에 강조 표시된 템플릿 줄에 지정된 "Home" 컨트롤러 및 "Index" 메서드가 기본값으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-273">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="18e66-274">첫 번째 URL 세그먼트는 실행할 컨트롤러 클래스를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-274">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="18e66-275">따라서 `localhost:{PORT}/HelloWorld`은 `HelloWorldController` 클래스에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-275">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="18e66-276">URL 세그먼트의 두 번째 부분은 클래스의 작업 메서드를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-276">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="18e66-277">따라서 `localhost:{PORT}/HelloWorld/Index`는 `HelloWorldController` 클래스의 `Index` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-277">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="18e66-278">`localhost:{PORT}/HelloWorld`로만 이동했음에도 기본적으로 `Index` 메서드가 호출되었음에 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="18e66-278">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="18e66-279">메서드 이름이 명시적으로 지정되지 않으면 `Index`가 컨트롤러에서 호출되는 기본 메서드이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-279">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="18e66-280">URL 세그먼트의 세 번째 부분(`id`)은 경로 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-280">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="18e66-281">경로 데이터는 자습서의 뒷 부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-281">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="18e66-282">`https://localhost:{PORT}/HelloWorld/Welcome`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-282">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="18e66-283">`Welcome` 메서드가 실행되고 문자열 `This is the Welcome action method...`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-283">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="18e66-284">이 URL의 경우 컨트롤러는 `HelloWorld`이고 `Welcome`이 작업 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-284">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="18e66-285">아직 URL의 `[Parameters]` 부분을 사용하지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-285">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![브라우저 창에 This is the Welcome action method라는 애플리케이션 응답이 표시됩니다.](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="18e66-287">URL에서 컨트롤러에 일부 매개 변수 정보를 전달하도록 코드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-287">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="18e66-288">예: `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="18e66-288">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="18e66-289">다음 코드와 같이 `Welcome` 메서드가 두 개의 매개 변수를 포함하도록 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-289">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="18e66-290">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="18e66-290">The preceding code:</span></span>

* <span data-ttu-id="18e66-291">C#의 선택적 매개 변수 기능을 사용하여 `numTimes` 매개 변수에 대해 전달된 값이 없는 경우 해당 매개 변수의 기본값이 1임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-291">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="18e66-292">`HtmlEncoder.Default.Encode`를 사용하여 악의적인 입력(예: JavaScript)으로부터 응용 프로그램을 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-292">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="18e66-293">`$"Hello {name}, NumTimes is: {numTimes}"`에서 [보간된 문자열](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-293">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="18e66-294">앱을 실행하고 다음으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-294">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="18e66-295">(`{PORT}`를 포트 번호로 바꿉니다.) URL에서 `name` 및 `numtimes`에 다른 값을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-295">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="18e66-296">MVC [모델 바인딩](xref:mvc/models/model-binding) 시스템은 주소 표시줄의 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수를 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-296">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="18e66-297">자세한 정보는 [모델 바인딩](xref:mvc/models/model-binding)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="18e66-297">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Hello Rick, NumTimes의 애플리케이션 응답을 보여주는 브라우저 창\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="18e66-299">위의 이미지에서는 URL 세그먼트(`Parameters`)를 사용하지 않고 `name` 및 `numTimes` 매개 변수가 [쿼리 문자열](https://wikipedia.org/wiki/Query_string)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-299">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="18e66-300">위 URL에서 `?`(물음표)는 구분 기호이며, 다음에 쿼리 문자열이 옵니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-300">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="18e66-301">`&` 문자는 필드-값 쌍을 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-301">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="18e66-302">`Welcome` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-302">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="18e66-303">앱을 실행하고 `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick` URL을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-303">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="18e66-304">이번에는 세 번째 URL 세그먼트가 `id` 경로 매개 변수와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-304">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="18e66-305">`Welcome` 메서드는 `MapRoute` 메서드의 URL 템플릿과 일치하는 `id` 매개 변수를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-305">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="18e66-306">후행 `?`(`id?`의)는 `id` 매개 변수가 선택 사항임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-306">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="18e66-307">이러한 예제에서 컨트롤러는 MVC의 "VC" 부분을 사용했습니다. 즉, 보기 및 컨트롤러 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-307">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="18e66-308">컨트롤러가 HTML을 직접 반환하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-308">The controller is returning HTML directly.</span></span> <span data-ttu-id="18e66-309">코드 및 유지 관리가 매우 복잡해지므로 일반적으로 컨트롤러에서 직접 HTML을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-309">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="18e66-310">대신 일반적으로 별도의 Razor 뷰 템플릿 파일을 사용하여 HTML 응답을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-310">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="18e66-311">다음 자습서에서는 해당 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="18e66-311">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="18e66-312">[이전](start-mvc.md)
> [다음](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="18e66-312">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end
