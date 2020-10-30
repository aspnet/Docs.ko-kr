---
title: ASP.NET Core MVC에서 컨트롤러를 사용한 요청 처리
author: ardalis
description: ''
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/controllers/actions
ms.openlocfilehash: a9319e74d0213b178c2a71be69a0332270d9446c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061459"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a><span data-ttu-id="c5526-102">ASP.NET Core MVC에서 컨트롤러를 사용한 요청 처리</span><span class="sxs-lookup"><span data-stu-id="c5526-102">Handle requests with controllers in ASP.NET Core MVC</span></span>

<span data-ttu-id="c5526-103">작성자: [Steve Smith](https://ardalis.com/) 및 [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="c5526-103">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="c5526-104">컨트롤러, 작업 및 작업 결과는 개발자가 ASP.NET Core MVC를 사용하여 앱을 구축하는 방법의 기본적인 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-104">Controllers, actions, and action results are a fundamental part of how developers build apps using ASP.NET Core MVC.</span></span>

## <a name="what-is-a-controller"></a><span data-ttu-id="c5526-105">컨트롤러란?</span><span class="sxs-lookup"><span data-stu-id="c5526-105">What is a Controller?</span></span>

<span data-ttu-id="c5526-106">컨트롤러는 일련의 작업을 정의하고 그룹화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-106">A controller is used to define and group a set of actions.</span></span> <span data-ttu-id="c5526-107">작업(또는 *작업 메서드* )은 요청을 처리하는 컨트롤러의 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-107">An action (or *action method* ) is a method on a controller which handles requests.</span></span> <span data-ttu-id="c5526-108">컨트롤러는 논리적으로 유사한 작업을 함께 그룹화합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-108">Controllers logically group similar actions together.</span></span> <span data-ttu-id="c5526-109">이 작업의 집합체를 통해 라우팅, 캐싱 및 권한 부여와 같은 일반적인 규칙 모음을 일괄적으로 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-109">This aggregation of actions allows common sets of rules, such as routing, caching, and authorization, to be applied collectively.</span></span> <span data-ttu-id="c5526-110">요청은 [라우팅](xref:mvc/controllers/routing)을 통해 작업에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-110">Requests are mapped to actions through [routing](xref:mvc/controllers/routing).</span></span>

<span data-ttu-id="c5526-111">규칙에 따라 컨트롤러 클래스는:</span><span class="sxs-lookup"><span data-stu-id="c5526-111">By convention, controller classes:</span></span>

* <span data-ttu-id="c5526-112">프로젝트의 루트 수준 *Controllers* 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-112">Reside in the project's root-level *Controllers* folder.</span></span>
* <span data-ttu-id="c5526-113">`Microsoft.AspNetCore.Mvc.Controller`에서 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-113">Inherit from `Microsoft.AspNetCore.Mvc.Controller`.</span></span>

<span data-ttu-id="c5526-114">컨트롤러는 다음 조건 중 하나 이상이 참인 인스턴스화할 수 있는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-114">A controller is an instantiable class in which at least one of the following conditions is true:</span></span>

* <span data-ttu-id="c5526-115">클래스 이름에 접미사 `Controller`가 붙습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-115">The class name is suffixed with `Controller`.</span></span>
* <span data-ttu-id="c5526-116">클래스가 이름에 접미사 `Controller`가 붙는 클래스를 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-116">The class inherits from a class whose name is suffixed with `Controller`.</span></span>
* <span data-ttu-id="c5526-117">`[Controller]` 특성이 클래스에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-117">The `[Controller]` attribute is applied to the class.</span></span>

<span data-ttu-id="c5526-118">컨트롤러 클래스에는 연결된 `[NonController]` 특성이 없어야만 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-118">A controller class must not have an associated `[NonController]` attribute.</span></span>

<span data-ttu-id="c5526-119">컨트롤러는 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 따라야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-119">Controllers should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span> <span data-ttu-id="c5526-120">이 원칙을 구현하는 방법에는 몇 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-120">There are a couple of approaches to implementing this principle.</span></span> <span data-ttu-id="c5526-121">여러 컨트롤러 작업에서 동일한 서비스가 필요한 경우 해당 종속성을 요청하는 데 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 사용하는 것을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-121">If multiple controller actions require the same service, consider using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to request those dependencies.</span></span> <span data-ttu-id="c5526-122">서비스가 단일 작업 메서드에서만 필요한 경우에는 종속성을 요청하는 데 [작업 주입](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)의 사용을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="c5526-122">If the service is needed by only a single action method, consider using [Action Injection](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) to request the dependency.</span></span>

<span data-ttu-id="c5526-123">**M** odel- **V** iew- **C** ontroller(모델-뷰-컨트롤러) 패턴에서 컨트롤러는 초기 요청 처리 및 모델의 인스턴스화를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-123">Within the **M** odel- **V** iew- **C** ontroller pattern, a controller is responsible for the initial processing of the request and instantiation of the model.</span></span> <span data-ttu-id="c5526-124">일반적으로 비즈니스 의사 결정은 모델 내에서 수행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-124">Generally, business decisions should be performed within the model.</span></span>

<span data-ttu-id="c5526-125">컨트롤러는 모델의 처리 결과(있는 경우)를 사용하고, 적절한 보기와 관련된 보기 데이터 또는 API 호출의 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-125">The controller takes the result of the model's processing (if any) and returns either the proper view and its associated view data or the result of the API call.</span></span> <span data-ttu-id="c5526-126">[ASP.NET Core MVC 개요](xref:mvc/overview) 및 [ASP.NET Core MVC 및 Visual Studio 시작](xref:tutorials/first-mvc-app/start-mvc)에서 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-126">Learn more at [Overview of ASP.NET Core MVC](xref:mvc/overview) and [Get started with ASP.NET Core MVC and Visual Studio](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="c5526-127">컨트롤러는 *UI 수준* 추상화입니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-127">The controller is a *UI-level* abstraction.</span></span> <span data-ttu-id="c5526-128">컨트롤러의 책임은 요청 데이터가 올바른지 확인하고 어떤 보기(또는 API에 대한 결과)를 반환해야 하는지 선택하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-128">Its responsibilities are to ensure request data is valid and to choose which view (or result for an API) should be returned.</span></span> <span data-ttu-id="c5526-129">잘 구성된 앱에서는 데이터 액세스 또는 비즈니스 논리를 컨트롤러에 직접 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-129">In well-factored apps, it doesn't directly include data access or business logic.</span></span> <span data-ttu-id="c5526-130">대신, 컨트롤러는 이러한 책임을 처리하는 서비스에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-130">Instead, the controller delegates to services handling these responsibilities.</span></span>

## <a name="defining-actions"></a><span data-ttu-id="c5526-131">작업 정의</span><span class="sxs-lookup"><span data-stu-id="c5526-131">Defining Actions</span></span>

<span data-ttu-id="c5526-132">컨트롤러의 공용 메서드는 `[NonAction]` 특성이 있는 경우 이외에는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-132">Public methods on a controller, except those with the `[NonAction]` attribute, are actions.</span></span> <span data-ttu-id="c5526-133">작업의 매개 변수는 요청 데이터에 바인딩되며 [모델 바인딩](xref:mvc/models/model-binding)을 사용하여 유효성 검사가 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-133">Parameters on actions are bound to request data and are validated using [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="c5526-134">모델 유효성 검사는 모델 바인딩되는 모든 작업에 대해 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-134">Model validation occurs for everything that's model-bound.</span></span> <span data-ttu-id="c5526-135">`ModelState.IsValid` 속성 값은 모델 바인딩 및 유효성 검사의 성공 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-135">The `ModelState.IsValid` property value indicates whether model binding and validation succeeded.</span></span>

<span data-ttu-id="c5526-136">작업 메서드는 비즈니스 문제에 요청을 매핑하기 위한 논리를 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-136">Action methods should contain logic for mapping a request to a business concern.</span></span> <span data-ttu-id="c5526-137">비즈니스 문제는 일반적으로 컨트롤러가 [종속성 주입](xref:mvc/controllers/dependency-injection)을 통해 액세스하는 서비스로 표현되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-137">Business concerns should typically be represented as services that the controller accesses through [dependency injection](xref:mvc/controllers/dependency-injection).</span></span> <span data-ttu-id="c5526-138">그런 다음 작업은 비즈니스 작업의 결과를 애플리케이션 상태에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-138">Actions then map the result of the business action to an application state.</span></span>

<span data-ttu-id="c5526-139">작업은 무엇이든 반환할 수 있지만, 흔히 응답을 생성하는 `IActionResult`(또는 비동기 메서드에 대한 `Task<IActionResult>`)의 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-139">Actions can return anything, but frequently return an instance of `IActionResult` (or `Task<IActionResult>` for async methods) that produces a response.</span></span> <span data-ttu-id="c5526-140">작업 메서드는 *응답의 종류* 를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-140">The action method is responsible for choosing *what kind of response* .</span></span> <span data-ttu-id="c5526-141">작업 결과는 *응답을 수행* 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-141">The action result *does the responding* .</span></span>

### <a name="controller-helper-methods"></a><span data-ttu-id="c5526-142">컨트롤러 도우미 메서드</span><span class="sxs-lookup"><span data-stu-id="c5526-142">Controller Helper Methods</span></span>

<span data-ttu-id="c5526-143">컨트롤러는 일반적으로 [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller)에서 상속합니다. 단, 이는 필수가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-143">Controllers usually inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), although this isn't required.</span></span> <span data-ttu-id="c5526-144">`Controller`에서의 파생은 도우미 메서드의 세 범주에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-144">Deriving from `Controller` provides access to three categories of helper methods:</span></span>

#### <a name="1-methods-resulting-in-an-empty-response-body"></a><span data-ttu-id="c5526-145">1. 메서드는 빈 응답 본문을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-145">1. Methods resulting in an empty response body</span></span>

<span data-ttu-id="c5526-146">응답 본문에 설명할 콘텐츠가 없으므로 `Content-Type` HTTP 응답 헤더가 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-146">No `Content-Type` HTTP response header is included, since the response body lacks content to describe.</span></span>

<span data-ttu-id="c5526-147">이 범주 내에는 리디렉션 및 HTTP 상태 코드의 두 가지 결과 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-147">There are two result types within this category: Redirect and HTTP Status Code.</span></span>

* <span data-ttu-id="c5526-148">**HTTP 상태 코드**</span><span class="sxs-lookup"><span data-stu-id="c5526-148">**HTTP Status Code**</span></span>

    <span data-ttu-id="c5526-149">이 형식은 HTTP 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-149">This type returns an HTTP status code.</span></span> <span data-ttu-id="c5526-150">이러한 형식의 몇 가지 도우미 메서드는 `BadRequest`, `NotFound` 및 `Ok`입니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-150">A couple of helper methods of this type are `BadRequest`, `NotFound`, and `Ok`.</span></span> <span data-ttu-id="c5526-151">예를 들어 `return BadRequest();`가 실행되면 400 상태 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-151">For example, `return BadRequest();` produces a 400 status code when executed.</span></span> <span data-ttu-id="c5526-152">`BadRequest`, `NotFound` 및 `Ok`와 같은 메서드가 오버로드되는 경우 콘텐츠 협상이 수행되므로 더 이상 HTTP 상태 코드 응답자로서의 자격이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-152">When methods such as `BadRequest`, `NotFound`, and `Ok` are overloaded, they no longer qualify as HTTP Status Code responders, since content negotiation is taking place.</span></span>

* <span data-ttu-id="c5526-153">**리디렉션**</span><span class="sxs-lookup"><span data-stu-id="c5526-153">**Redirect**</span></span>

    <span data-ttu-id="c5526-154">이 형식은 작업 또는 대상에 리디렉션을 반환합니다(`Redirect`, `LocalRedirect`, `RedirectToAction` 또는 `RedirectToRoute` 를 사용하여).</span><span class="sxs-lookup"><span data-stu-id="c5526-154">This type returns a redirect to an action or destination (using `Redirect`, `LocalRedirect`, `RedirectToAction`, or `RedirectToRoute`).</span></span> <span data-ttu-id="c5526-155">예를 들어 `return RedirectToAction("Complete", new {id = 123});`은 `Complete`로 리디렉션하고 익명 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-155">For example, `return RedirectToAction("Complete", new {id = 123});` redirects to `Complete`, passing an anonymous object.</span></span>

    <span data-ttu-id="c5526-156">리디렉션 결과 형식은 주로 `Location` HTTP 응답 헤더와 더불어 HTTP 상태 코드 형식에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-156">The Redirect result type differs from the HTTP Status Code type primarily in the addition of a `Location` HTTP response header.</span></span>

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a><span data-ttu-id="c5526-157">2. 메서드를 사용 하면 미리 정의 된 콘텐츠 형식을 사용 하는 비어 있지 않은 응답 본문이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-157">2. Methods resulting in a non-empty response body with a predefined content type</span></span>

<span data-ttu-id="c5526-158">이 범주의 도우미 메서드 대부분은 `ContentType` 속성을 포함하므로 응답 본문을 설명하도록 `Content-Type` 응답 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-158">Most helper methods in this category include a `ContentType` property, allowing you to set the `Content-Type` response header to describe the response body.</span></span>

<span data-ttu-id="c5526-159">이 범주 내의 결과 형식은 [보기](xref:mvc/views/overview) 및 [서식 있는 응답](xref:web-api/advanced/formatting)의 두 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-159">There are two result types within this category: [View](xref:mvc/views/overview) and [Formatted Response](xref:web-api/advanced/formatting).</span></span>

* <span data-ttu-id="c5526-160">**보기**</span><span class="sxs-lookup"><span data-stu-id="c5526-160">**View**</span></span>

    <span data-ttu-id="c5526-161">이 형식은 HTML을 렌더링하는 모델을 사용하는 보기를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-161">This type returns a view which uses a model to render HTML.</span></span> <span data-ttu-id="c5526-162">예를 들어 `return View(customer);`는 보기에 데이터 바인딩을 위한 모델을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-162">For example, `return View(customer);` passes a model to the view for data-binding.</span></span>

* <span data-ttu-id="c5526-163">**서식화된 응답**</span><span class="sxs-lookup"><span data-stu-id="c5526-163">**Formatted Response**</span></span>

    <span data-ttu-id="c5526-164">이 형식은 JSON 또는 비슷한 데이터 교환 형식을 반환하여 개체를 특정 방식으로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-164">This type returns JSON or a similar data exchange format to represent an object in a specific manner.</span></span> <span data-ttu-id="c5526-165">예를 들어 `return Json(customer);`은 JSON 형식에 제공된 개체를 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-165">For example, `return Json(customer);` serializes the provided object into JSON format.</span></span>
    
    <span data-ttu-id="c5526-166">이 형식의 다른 일반적인 메서드에는 `File` 및 `PhysicalFile`이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-166">Other common methods of this type include `File` and `PhysicalFile`.</span></span> <span data-ttu-id="c5526-167">예를 들어 `return PhysicalFile(customerFilePath, "text/xml");`은 [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult)를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-167">For example, `return PhysicalFile(customerFilePath, "text/xml");` returns [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult).</span></span>

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a><span data-ttu-id="c5526-168">3. 메서드는 클라이언트와 협상 된 콘텐츠 형식으로 비어 있지 않은 응답 본문의 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-168">3. Methods resulting in a non-empty response body formatted in a content type negotiated with the client</span></span>

<span data-ttu-id="c5526-169">이 범주는 **콘텐츠 협상** 으로 더 잘 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-169">This category is better known as **Content Negotiation** .</span></span> <span data-ttu-id="c5526-170">[콘텐츠 협상](xref:web-api/advanced/formatting#content-negotiation)은 작업이 [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) 형식 또는 [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) 구현 이외의 형식을 반환할 때마다 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-170">[Content negotiation](xref:web-api/advanced/formatting#content-negotiation) applies whenever an action returns an [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) type or something other than an [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) implementation.</span></span> <span data-ttu-id="c5526-171">비 `IActionResult` 구현을 반환하는 작업(예: `object`)도 서식화된 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-171">An action that returns a non-`IActionResult` implementation (for example, `object`) also returns a Formatted Response.</span></span>

<span data-ttu-id="c5526-172">이 형식의 몇 가지 도우미 메서드에는 `BadRequest`, `CreatedAtRoute` 및 `Ok`가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-172">Some helper methods of this type include `BadRequest`, `CreatedAtRoute`, and `Ok`.</span></span> <span data-ttu-id="c5526-173">이러한 메서드의 예로는 각각 `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);` 및 `return Ok(value);`가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-173">Examples of these methods include `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);`, and `return Ok(value);`, respectively.</span></span> <span data-ttu-id="c5526-174">`BadRequest` 및 `Ok`는 값이 전달될 때만 콘텐츠 협상을 수행합니다. 값을 전달하지 않으면 대신 HTTP 상태 코드 결과 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-174">Note that `BadRequest` and `Ok` perform content negotiation only when passed a value; without being passed a value, they instead serve as HTTP Status Code result types.</span></span> <span data-ttu-id="c5526-175">반면, `CreatedAtRoute` 메서드는 해당 오버로드에서 모두 값이 전달되어야 하므로 항상 콘텐츠 협상을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-175">The `CreatedAtRoute` method, on the other hand, always performs content negotiation since its overloads all require that a value be passed.</span></span>

### <a name="cross-cutting-concerns"></a><span data-ttu-id="c5526-176">횡단 관심사</span><span class="sxs-lookup"><span data-stu-id="c5526-176">Cross-Cutting Concerns</span></span>

<span data-ttu-id="c5526-177">응용 프로그램은 일반적으로 해당 워크플로의 일부를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-177">Applications typically share parts of their workflow.</span></span> <span data-ttu-id="c5526-178">예를 들어 쇼핑 카트 액세스를 위해 인증이 필요한 앱이나 일부 페이지에서 데이터를 캐시하는 앱이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-178">Examples include an app that requires authentication to access the shopping cart, or an app that caches data on some pages.</span></span> <span data-ttu-id="c5526-179">작업 메서드 전후로 논리를 수행하려면 *필터* 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-179">To perform logic before or after an action method, use a *filter* .</span></span> <span data-ttu-id="c5526-180">횡단 관심사에서 [필터](xref:mvc/controllers/filters)를 사용하면 중복을 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-180">Using [Filters](xref:mvc/controllers/filters) on cross-cutting concerns can reduce duplication.</span></span>

<span data-ttu-id="c5526-181">`[Authorize]`와 같은 대부분의 필터 특성은 원하는 세분성 수준에 따라 컨트롤러 또는 작업 수준에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-181">Most filter attributes, such as `[Authorize]`, can be applied at the controller or action level depending upon the desired level of granularity.</span></span>

<span data-ttu-id="c5526-182">오류 처리 및 응답 캐시는 종종 교차 편집 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-182">Error handling and response caching are often cross-cutting concerns:</span></span>
* [<span data-ttu-id="c5526-183">오류 처리</span><span class="sxs-lookup"><span data-stu-id="c5526-183">Handle errors</span></span>](xref:mvc/controllers/filters#exception-filters)
* [<span data-ttu-id="c5526-184">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="c5526-184">Response Caching</span></span>](xref:performance/caching/response)

<span data-ttu-id="c5526-185">많은 교차 편집 문제는 필터 또는 사용자 지정 [미들웨어](xref:fundamentals/middleware/index)를 사용하여 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5526-185">Many cross-cutting concerns can be handled using filters or custom [middleware](xref:fundamentals/middleware/index).</span></span>
