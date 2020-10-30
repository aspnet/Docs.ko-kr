---
title: ASP.NET Web API에서 ASP.NET Core로 마이그레이션
author: ardalis
description: ASP.NET 4.x Web API에서 ASP.NET Core MVC로 웹 API 구현을 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: migration/webapi
ms.openlocfilehash: 320805c0d40bf06cee384e6d98caea5c420d45bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061472"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="17d3f-103">ASP.NET Web API에서 ASP.NET Core로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="17d3f-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="17d3f-104">[Scott Addie](https://twitter.com/scott_addie) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="17d3f-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="17d3f-105">ASP.NET 4.x Web API는 브라우저 및 모바일 장치를 비롯 한 광범위 한 클라이언트에 도달 하는 HTTP 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="17d3f-106">ASP.NET Core ASP.NET 4.x의 MVC 및 Web API 앱 모델을 ASP.NET Core MVC 라는 단일 프로그래밍 모델로 결합 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-106">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="17d3f-107">이 문서에서는 ASP.NET 4.x Web API에서 ASP.NET Core MVC로 마이그레이션하는 데 필요한 단계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="17d3f-108">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="17d3f-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="17d3f-109">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="17d3f-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="17d3f-110">ASP.NET 4.x 웹 API 프로젝트를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="17d3f-111">이 문서에서는 [ASP.NET Web API 2 시작](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)에서 만든 *ProductsApp* 프로젝트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-111">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="17d3f-112">해당 프로젝트에서 기본 ASP.NET 4.x 웹 API 프로젝트는 다음과 같이 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-112">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="17d3f-113">*Global.asax.cs* 에서 다음을 호출 합니다 `WebApiConfig.Register` .</span><span class="sxs-lookup"><span data-stu-id="17d3f-113">In *Global.asax.cs* , a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="17d3f-114">`WebApiConfig`클래스는 *App_Start* 폴더에 있으며 정적 메서드를 포함 합니다 `Register` .</span><span class="sxs-lookup"><span data-stu-id="17d3f-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="17d3f-115">이전 클래스:</span><span class="sxs-lookup"><span data-stu-id="17d3f-115">The preceding class:</span></span>

* <span data-ttu-id="17d3f-116">실제로 사용 되 고 있지는 않지만 [특성 라우팅을](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-116">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="17d3f-117">라우팅 테이블을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-117">Configures the routing table.</span></span>
<span data-ttu-id="17d3f-118">샘플 코드에서는 Url을 선택적으로 사용 하 여 Url과 일치 하는 Url을 예상 합니다 `/api/{controller}/{id}` `{id}` .</span><span class="sxs-lookup"><span data-stu-id="17d3f-118">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="17d3f-119">다음 섹션에서는 ASP.NET Core MVC로 웹 API 프로젝트를 마이그레이션하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-119">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="17d3f-120">대상 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="17d3f-120">Create the destination project</span></span>

<span data-ttu-id="17d3f-121">Visual Studio에서 비어 있는 새 솔루션을 만들고 마이그레이션할 ASP.NET 4.x 웹 API 프로젝트를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-121">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="17d3f-122">**파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-122">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="17d3f-123">**빈 솔루션** 템플릿을 선택 하 고 **다음** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-123">Select the **Blank Solution** template and select **Next** .</span></span>
1. <span data-ttu-id="17d3f-124">솔루션 이름을 *WebAPIMigration* 로 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-124">Name the solution *WebAPIMigration* .</span></span> <span data-ttu-id="17d3f-125">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-125">Select **Create** .</span></span>
1. <span data-ttu-id="17d3f-126">기존 *ProductsApp* 프로젝트를 솔루션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-126">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="17d3f-127">마이그레이션할 새 API 프로젝트를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-127">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="17d3f-128">새 **ASP.NET Core 웹 응용 프로그램** 프로젝트를 솔루션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="17d3f-129">**새 프로젝트 구성** 대화 상자에서 프로젝트의 이름을 *제품 점수* 로 하 고 **만들기** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-129">In the **Configure your new project** dialog, Name the project *ProductsCore* , and select **Create** .</span></span>
1. <span data-ttu-id="17d3f-130">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.1** 이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="17d3f-131">**API** 프로젝트 템플릿을 선택하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-131">Select the **API** project template, and select **Create** .</span></span>
1. <span data-ttu-id="17d3f-132">새 *제품 점수* 프로젝트에서 *WeatherForecast.cs* 및 *Controllers/WeatherForecastController* 예제 파일을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-132">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="17d3f-133">이제 솔루션에는 두 개의 프로젝트가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-133">The solution now contains two projects.</span></span> <span data-ttu-id="17d3f-134">다음 섹션에서는 *ProductsApp* 프로젝트의 콘텐츠를 *제품 점수* 프로젝트로 마이그레이션하는 방법에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-134">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="17d3f-135">구성 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="17d3f-135">Migrate configuration</span></span>

<span data-ttu-id="17d3f-136">ASP.NET Core *App_Start* 폴더 또는 *global.asax* 파일을 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-136">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="17d3f-137">또한 *web.config* 파일은 게시할 때 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-137">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="17d3f-138">`Startup` 클래스는:</span><span class="sxs-lookup"><span data-stu-id="17d3f-138">The `Startup` class:</span></span>

* <span data-ttu-id="17d3f-139">*Global.asax* 를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-139">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="17d3f-140">모든 앱 시작 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="17d3f-141">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17d3f-141">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="17d3f-142">모델 및 컨트롤러 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="17d3f-142">Migrate models and controllers</span></span>

<span data-ttu-id="17d3f-143">다음 코드에서는 `ProductsController` ASP.NET Core에 대해 업데이트 되는을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-143">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="17d3f-144">`ProductsController`ASP.NET Core에 대 한를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-144">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="17d3f-145">*Controllers/ProductsController* 및 *모델* 폴더를 원본 프로젝트에서 새 프로젝트로 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-145">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="17d3f-146">복사한 파일의 루트 네임 스페이스를로 변경 `ProductsCore` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-146">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="17d3f-147">`using ProductsApp.Models;`문을로 업데이트 `using ProductsCore.Models;` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-147">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="17d3f-148">ASP.NET Core에는 다음 구성 요소가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-148">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="17d3f-149">`ApiController` 클래스</span><span class="sxs-lookup"><span data-stu-id="17d3f-149">`ApiController` class</span></span>
* <span data-ttu-id="17d3f-150">`System.Web.Http` 네임스페이스</span><span class="sxs-lookup"><span data-stu-id="17d3f-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="17d3f-151">`IHttpActionResult` 인터페이스</span><span class="sxs-lookup"><span data-stu-id="17d3f-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="17d3f-152">다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-152">Make the following changes:</span></span>

1. <span data-ttu-id="17d3f-153">`ApiController`을 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="17d3f-154">`using Microsoft.AspNetCore.Mvc;`를 추가 하 여 `ControllerBase` 참조를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="17d3f-155">`using System.Web.Http;`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="17d3f-156">`GetProduct`작업의 반환 형식을에서로 변경 `IHttpActionResult` 합니다 `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="17d3f-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="17d3f-157">`GetProduct`작업의 `return` 문을 다음과 같이 단순화 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="17d3f-158">라우팅 구성</span><span class="sxs-lookup"><span data-stu-id="17d3f-158">Configure routing</span></span>

<span data-ttu-id="17d3f-159">ASP.NET Core *API* 프로젝트 템플릿에는 생성 된 코드의 끝점 라우팅 구성이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-159">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="17d3f-160">다음은 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> 을 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-160">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="17d3f-161">[미들웨어](xref:fundamentals/middleware/index) 파이프라인에서 경로 일치 및 끝점 실행을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-161">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="17d3f-162">*ProductsApp* 프로젝트의 *App_Start/Webapicon.cs* 파일을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-162">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="17d3f-163">다음과 같이 라우팅을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-163">Configure routing as follows:</span></span>

1. <span data-ttu-id="17d3f-164">클래스에 `ProductsController` 다음 특성을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-164">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="17d3f-165">위의 [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 특성은 컨트롤러의 특성 라우팅 패턴을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-165">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="17d3f-166">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)특성은이 컨트롤러의 모든 동작에 대 한 요구 사항을 라우팅하는 특성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-166">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="17d3f-167">특성 라우팅은 및와 같은 토큰을 `[controller]` 지원 `[action]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-167">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="17d3f-168">런타임에 각 토큰은 특성이 적용 되는 각각의 컨트롤러나 동작의 이름으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-168">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="17d3f-169">토큰:</span><span class="sxs-lookup"><span data-stu-id="17d3f-169">The tokens:</span></span>
    * <span data-ttu-id="17d3f-170">프로젝트의 매직 문자열 수를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-170">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="17d3f-171">자동 이름 바꾸기 리팩터링 적용 될 때 경로가 해당 컨트롤러 및 작업과 동기화 된 상태로 유지 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-171">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="17d3f-172">작업에 대 한 HTTP Get 요청을 사용 하도록 설정 합니다 `ProductController` .</span><span class="sxs-lookup"><span data-stu-id="17d3f-172">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="17d3f-173">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)작업에 특성을 적용 `GetAllProducts` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-173">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="17d3f-174">`[HttpGet("{id}")]`작업에 특성을 적용 `GetProduct` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-174">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="17d3f-175">마이그레이션된 프로젝트를 실행 하 고로 이동 `/api/products` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="17d3f-176">세 제품의 전체 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-176">A full list of three products appears.</span></span> <span data-ttu-id="17d3f-177">[https://www.microsoft.com]\(`/api/products/1`) 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="17d3f-178">첫 번째 제품이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-178">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17d3f-179">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="17d3f-179">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="17d3f-180">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="17d3f-180">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="17d3f-181">ASP.NET 4.x 웹 API 프로젝트를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-181">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="17d3f-182">이 문서에서는 [ASP.NET Web API 2 시작](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)에서 만든 *ProductsApp* 프로젝트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-182">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="17d3f-183">해당 프로젝트에서 기본 ASP.NET 4.x 웹 API 프로젝트는 다음과 같이 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-183">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="17d3f-184">*Global.asax.cs* 에서 다음을 호출 합니다 `WebApiConfig.Register` .</span><span class="sxs-lookup"><span data-stu-id="17d3f-184">In *Global.asax.cs* , a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="17d3f-185">`WebApiConfig`클래스는 *App_Start* 폴더에 있으며 정적 메서드를 포함 합니다 `Register` .</span><span class="sxs-lookup"><span data-stu-id="17d3f-185">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="17d3f-186">이 클래스는 실제로 프로젝트에서 사용 되지 않지만 [특성 라우팅을](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-186">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="17d3f-187">또한 ASP.NET Web API에서 사용 하는 라우팅 테이블을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-187">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="17d3f-188">이 경우 ASP.NET 4.x Web API는 Url과 일치 하는 Url을 `/api/{controller}/{id}` 선택 사항으로 예상 `{id}` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-188">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="17d3f-189">다음 섹션에서는 ASP.NET Core MVC로 웹 API 프로젝트를 마이그레이션하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-189">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="17d3f-190">대상 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="17d3f-190">Create the destination project</span></span>

<span data-ttu-id="17d3f-191">Visual Studio에서 다음 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-191">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="17d3f-192">**파일**  >  **새로 만들기**  >  **프로젝트**  >  **기타 프로젝트 형식**  >  **Visual Studio 솔루션** 으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-192">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions** .</span></span> <span data-ttu-id="17d3f-193">**비어 있는 솔루션** 을 선택 하 고 솔루션의 이름을 *WebAPIMigration* 로 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-193">Select **Blank Solution** , and name the solution *WebAPIMigration* .</span></span> <span data-ttu-id="17d3f-194">**확인** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-194">Click the **OK** button.</span></span>
* <span data-ttu-id="17d3f-195">기존 *ProductsApp* 프로젝트를 솔루션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-195">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="17d3f-196">새 **ASP.NET Core 웹 응용 프로그램** 프로젝트를 솔루션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-196">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="17d3f-197">드롭다운에서 **.Net Core** 대상 프레임 워크를 선택 하 고 **API** 프로젝트 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-197">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="17d3f-198">프로젝트의 이름을 *제품 점수* 로 매기고 **확인** 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-198">Name the project *ProductsCore* , and click the **OK** button.</span></span>

<span data-ttu-id="17d3f-199">이제 솔루션에는 두 개의 프로젝트가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-199">The solution now contains two projects.</span></span> <span data-ttu-id="17d3f-200">다음 섹션에서는 *ProductsApp* 프로젝트의 콘텐츠를 *제품 점수* 프로젝트로 마이그레이션하는 방법에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-200">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="17d3f-201">구성 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="17d3f-201">Migrate configuration</span></span>

<span data-ttu-id="17d3f-202">사용 하지 않는 ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="17d3f-202">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="17d3f-203">*App_Start* 폴더 또는 *global.asax* 파일</span><span class="sxs-lookup"><span data-stu-id="17d3f-203">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="17d3f-204">*web.config* 파일은 게시할 때 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-204">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="17d3f-205">`Startup` 클래스는:</span><span class="sxs-lookup"><span data-stu-id="17d3f-205">The `Startup` class:</span></span>

* <span data-ttu-id="17d3f-206">*Global.asax* 를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-206">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="17d3f-207">모든 앱 시작 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-207">Handles all app startup tasks.</span></span>

<span data-ttu-id="17d3f-208">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17d3f-208">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="17d3f-209">ASP.NET Core MVC에서는가에서 호출 될 때 특성 라우팅이 기본적으로 포함 됩니다 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="17d3f-209">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="17d3f-210">다음 `UseMvc` 호출은 *ProductsApp* 프로젝트의 *App_Start/Webapicon.cs* 파일을 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-210">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="17d3f-211">모델 및 컨트롤러 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="17d3f-211">Migrate models and controllers</span></span>

<span data-ttu-id="17d3f-212">다음 코드에서는 `ProductsController` ASP.NET Core에 대 한 업데이트를 보여 줍니다. [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="17d3f-212">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="17d3f-213">`ProductsController`ASP.NET Core에 대 한를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-213">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="17d3f-214">원본 프로젝트의 *Controllers/ProductsController* 를 새 프로젝트에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-214">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="17d3f-215">원본 프로젝트의 *모델* 폴더를 새 프로젝트에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-215">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="17d3f-216">복사한 파일의 루트 네임 스페이스를로 변경 `ProductsCore` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-216">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="17d3f-217">`using ProductsApp.Models;`문을로 업데이트 `using ProductsCore.Models;` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-217">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="17d3f-218">ASP.NET Core에는 다음 구성 요소가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-218">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="17d3f-219">`ApiController` 클래스</span><span class="sxs-lookup"><span data-stu-id="17d3f-219">`ApiController` class</span></span>
* <span data-ttu-id="17d3f-220">`System.Web.Http` 네임스페이스</span><span class="sxs-lookup"><span data-stu-id="17d3f-220">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="17d3f-221">`IHttpActionResult` 인터페이스</span><span class="sxs-lookup"><span data-stu-id="17d3f-221">`IHttpActionResult` interface</span></span>

<span data-ttu-id="17d3f-222">다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-222">Make the following changes:</span></span>

1. <span data-ttu-id="17d3f-223">`ApiController`을 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-223">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="17d3f-224">`using Microsoft.AspNetCore.Mvc;`를 추가 하 여 `ControllerBase` 참조를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-224">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="17d3f-225">`using System.Web.Http;`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-225">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="17d3f-226">`GetProduct`작업의 반환 형식을에서로 변경 `IHttpActionResult` 합니다 `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="17d3f-226">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="17d3f-227">`GetProduct`작업의 `return` 문을 다음과 같이 단순화 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-227">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="17d3f-228">라우팅 구성</span><span class="sxs-lookup"><span data-stu-id="17d3f-228">Configure routing</span></span>

<span data-ttu-id="17d3f-229">다음과 같이 라우팅을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-229">Configure routing as follows:</span></span>

1. <span data-ttu-id="17d3f-230">클래스에 `ProductsController` 다음 특성을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-230">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="17d3f-231">위의 [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 특성은 컨트롤러의 특성 라우팅 패턴을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-231">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="17d3f-232">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)특성은이 컨트롤러의 모든 동작에 대 한 요구 사항을 라우팅하는 특성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-232">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="17d3f-233">특성 라우팅은 및와 같은 토큰을 `[controller]` 지원 `[action]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-233">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="17d3f-234">런타임에 각 토큰은 특성이 적용 되는 각각의 컨트롤러나 동작의 이름으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-234">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="17d3f-235">토큰은 프로젝트의 매직 문자열 수를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-235">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="17d3f-236">또한 토큰은 자동 이름 바꾸기 리팩터링 적용 될 때 해당 컨트롤러 및 작업과 동기화 된 상태로 유지 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-236">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="17d3f-237">프로젝트의 호환성 모드를 ASP.NET Core 2.2으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-237">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="17d3f-238">이전 변경 내용:</span><span class="sxs-lookup"><span data-stu-id="17d3f-238">The preceding change:</span></span>

    * <span data-ttu-id="17d3f-239">는 컨트롤러 수준에서 특성을 사용 하는 데 필요 `[ApiController]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-239">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="17d3f-240">ASP.NET Core 2.2에 도입 된 잠재적으로 Opts 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-240">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="17d3f-241">작업에 대 한 HTTP Get 요청을 사용 하도록 설정 합니다 `ProductController` .</span><span class="sxs-lookup"><span data-stu-id="17d3f-241">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="17d3f-242">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)작업에 특성을 적용 `GetAllProducts` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-242">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="17d3f-243">`[HttpGet("{id}")]`작업에 특성을 적용 `GetProduct` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-243">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="17d3f-244">마이그레이션된 프로젝트를 실행 하 고로 이동 `/api/products` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-244">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="17d3f-245">세 제품의 전체 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-245">A full list of three products appears.</span></span> <span data-ttu-id="17d3f-246">[https://www.microsoft.com]\(`/api/products/1`) 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-246">Browse to `/api/products/1`.</span></span> <span data-ttu-id="17d3f-247">첫 번째 제품이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-247">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="17d3f-248">호환성 shim</span><span class="sxs-lookup"><span data-stu-id="17d3f-248">Compatibility shim</span></span>

<span data-ttu-id="17d3f-249">[AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) 라이브러리는 ASP.NET 4.X 웹 API 프로젝트를 ASP.NET Core로 이동 하는 호환성 shim을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-249">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="17d3f-250">호환성 shim은 ASP.NET 4.x Web API 2의 다양 한 규칙을 지원 하도록 ASP.NET Core를 확장 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-250">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="17d3f-251">이 문서에서 이전에 이식 한 샘플은 호환성 shim이 필요 하지 않은 경우에만 기본입니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-251">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="17d3f-252">대규모 프로젝트의 경우 호환성 shim을 사용 하면 ASP.NET Core와 ASP.NET 4.x Web API 2 간의 API 격차를 일시적으로 브리징 하는 데 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-252">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="17d3f-253">Web API 호환성 shim은 ASP.NET Core로의 large ASP.NET 4.x 웹 API 프로젝트 마이그레이션을 지원 하기 위한 임시 조치로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-253">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="17d3f-254">시간이 지남에 따라 프로젝트는 호환성 shim을 사용 하는 대신 ASP.NET Core 패턴을 사용 하도록 업데이트 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-254">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="17d3f-255">에 포함 된 호환성 기능은 `Microsoft.AspNetCore.Mvc.WebApiCompatShim` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-255">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="17d3f-256">`ApiController`컨트롤러의 기본 형식을 업데이트할 필요가 없도록 형식을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-256">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="17d3f-257">웹 API 스타일 모델 바인딩을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-257">Enables Web API-style model binding.</span></span> <span data-ttu-id="17d3f-258">ASP.NET Core MVC 모델 바인딩 함수는 기본적으로 ASP.NET 4.x MVC 5와 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-258">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="17d3f-259">호환성 shim은 모델 바인딩을 ASP.NET 4.x Web API 2 모델 바인딩 규칙과 더 유사 하 게 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-259">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="17d3f-260">예를 들어 복합 형식은 요청 본문에서 자동으로 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-260">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="17d3f-261">컨트롤러 작업에서 형식의 매개 변수를 사용할 수 있도록 모델 바인딩을 확장 `HttpRequestMessage` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-261">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="17d3f-262">형식의 결과를 반환 하는 작업을 허용 하는 메시지 포맷터를 추가 `HttpResponseMessage` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-262">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="17d3f-263">웹 API 2 작업에서 응답을 제공 하는 데 사용할 수 있는 추가 응답 메서드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-263">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="17d3f-264">`HttpResponseMessage` 생성기</span><span class="sxs-lookup"><span data-stu-id="17d3f-264">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="17d3f-265">작업 결과 메서드:</span><span class="sxs-lookup"><span data-stu-id="17d3f-265">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="17d3f-266">앱의 추가 `IContentNegotiator` DI (종속성 주입) 컨테이너에 인스턴스를 추가 하 고 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)의 콘텐츠 협상 관련 형식을 사용 가능 하 게 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-266">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="17d3f-267">이러한 형식의 예로는 `DefaultContentNegotiator` 및가 `MediaTypeFormatter` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-267">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="17d3f-268">호환성 shim을 사용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-268">To use the compatibility shim:</span></span>

1. <span data-ttu-id="17d3f-269">[AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-269">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="17d3f-270">에서를 호출 하 여 앱의 DI 컨테이너에 호환성 shim의 서비스를 등록 `services.AddMvc().AddWebApiConventions()` `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-270">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="17d3f-271">앱 호출의에서를 사용 하 여 웹 API 관련 경로를 정의 `MapWebApiRoute` `IRouteBuilder` `IApplicationBuilder.UseMvc` 합니다.</span><span class="sxs-lookup"><span data-stu-id="17d3f-271">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17d3f-272">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="17d3f-272">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
