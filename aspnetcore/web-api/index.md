---
title: ASP.NET Core로 Web API 만들기
author: scottaddie
description: ASP.NET Core에서 Web API 만들기에 대한 기본 사항을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/20/2020
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
uid: web-api/index
ms.openlocfilehash: fb7dfa53be1fee9904b4539a5c9da0700c2aa884
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585736"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="8b236-103">ASP.NET Core로 Web API 만들기</span><span class="sxs-lookup"><span data-stu-id="8b236-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="8b236-104">작성자: [Scott Addie](https://github.com/scottaddie), [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="8b236-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="8b236-105">ASP.NET Core는 C#를 사용하여 Web API라고도 하는 RESTful 서비스 만들기를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="8b236-106">요청을 처리하기 위해 Web API는 컨트롤러를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="8b236-107">Web API의 *컨트롤러* 는 `ControllerBase`에서 파생되는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="8b236-108">이 문서에서는 웹 API 요청을 처리하기 위해 컨트롤러를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="8b236-109">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/index/samples)</span><span class="sxs-lookup"><span data-stu-id="8b236-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="8b236-110">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b236-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="8b236-111">ControllerBase 클래스</span><span class="sxs-lookup"><span data-stu-id="8b236-111">ControllerBase class</span></span>

<span data-ttu-id="8b236-112">Web API는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>에서 파생되는 하나 이상의 컨트롤러 클래스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="8b236-113">웹 API 프로젝트 템플릿은 시작 컨트롤러를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="8b236-114"><xref:Microsoft.AspNetCore.Mvc.Controller> 클래스에서 파생된 Web API 컨트롤러를 만들지 마세요.</span><span class="sxs-lookup"><span data-stu-id="8b236-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="8b236-115">`Controller`는 `ControllerBase`에서 파생되며 뷰에 대한 지원을 추가하므로 Web API 요청이 아닌 웹 페이지를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="8b236-116">이 규칙에 대한 예외가 있습니다. 뷰와 웹 API 모두에 동일한 컨트롤러를 사용하려는 경우 `Controller`에서 파생합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="8b236-117">`ControllerBase` 클래스는 HTTP 요청을 처리하는 데 유용한 많은 속성 및 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="8b236-118">예를 들어, `ControllerBase.CreatedAtAction`은 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="8b236-119">다음은 `ControllerBase`가 제공하는 메서드의 몇 가지 예입니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="8b236-120">메서드</span><span class="sxs-lookup"><span data-stu-id="8b236-120">Method</span></span>   |<span data-ttu-id="8b236-121">참고</span><span class="sxs-lookup"><span data-stu-id="8b236-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="8b236-122">400 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="8b236-123">404 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="8b236-124">파일을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="8b236-125">[모델 바인딩](xref:mvc/models/model-binding)을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="8b236-126">[모델 유효성 검사](xref:mvc/models/validation)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="8b236-127">사용 가능한 모든 메서드 및 속성 목록을 보려면 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8b236-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="8b236-128">특성</span><span class="sxs-lookup"><span data-stu-id="8b236-128">Attributes</span></span>

<span data-ttu-id="8b236-129"><xref:Microsoft.AspNetCore.Mvc> 네임스페이스는 Web API 컨트롤러 및 작업 메서드의 동작을 구성하는 데 사용할 수 있는 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="8b236-130">다음 예제에서는 특성을 사용하여 지원되는 HTTP 작업 동사와 반환될 수 있는 알려진 HTTP 상태 코드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="8b236-131">다음은 사용 가능한 특성의 몇 가지 예입니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="8b236-132">특성</span><span class="sxs-lookup"><span data-stu-id="8b236-132">Attribute</span></span>|<span data-ttu-id="8b236-133">참고</span><span class="sxs-lookup"><span data-stu-id="8b236-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="8b236-134">컨트롤러 또는 작업의 URL 패턴을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="8b236-135">모델 바인딩에 포함할 접두사 및 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="8b236-136">HTTP GET 작업 동사를 지원하는 작업을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="8b236-137">작업에서 허용하는 데이터 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="8b236-138">작업에서 반환하는 데이터 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="8b236-139">사용 가능한 특성이 포함된 목록은 <xref:Microsoft.AspNetCore.Mvc> 네임스페이스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8b236-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="8b236-140">ApiController 특성</span><span class="sxs-lookup"><span data-stu-id="8b236-140">ApiController attribute</span></span>

<span data-ttu-id="8b236-141">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성을 컨트롤러 클래스에 적용하여 다음과 같이 독자적인 API 관련 동작을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="8b236-142">특성 라우팅 요구 사항</span><span class="sxs-lookup"><span data-stu-id="8b236-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="8b236-143">자동 HTTP 400 응답</span><span class="sxs-lookup"><span data-stu-id="8b236-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="8b236-144">바인딩 소스 매개 변수 유추</span><span class="sxs-lookup"><span data-stu-id="8b236-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="8b236-145">다중 파트/폼 데이터 요청 유추</span><span class="sxs-lookup"><span data-stu-id="8b236-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="8b236-146">오류 상태 코드에 대한 문제 세부 정보</span><span class="sxs-lookup"><span data-stu-id="8b236-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="8b236-147">*오류 상태 코드에 대한 문제 세부 정보* 기능을 사용하려면 2.2 이상의 [호환성 버전](xref:mvc/compatibility-version)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-147">The *Problem details for error status codes* feature requires a [compatibility version](xref:mvc/compatibility-version) of 2.2 or later.</span></span> <span data-ttu-id="8b236-148">기타 기능을 사용하려면 2.1 이상의 호환성 버전이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-148">The other features require a compatibility version of 2.1 or later.</span></span>

::: moniker-end

* [<span data-ttu-id="8b236-149">특성 라우팅 요구 사항</span><span class="sxs-lookup"><span data-stu-id="8b236-149">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="8b236-150">자동 HTTP 400 응답</span><span class="sxs-lookup"><span data-stu-id="8b236-150">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="8b236-151">바인딩 소스 매개 변수 유추</span><span class="sxs-lookup"><span data-stu-id="8b236-151">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="8b236-152">다중 파트/폼 데이터 요청 유추</span><span class="sxs-lookup"><span data-stu-id="8b236-152">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)

<span data-ttu-id="8b236-153">이러한 기능을 사용하려면 [호환성 버전](xref:mvc/compatibility-version) 2.1 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-153">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="8b236-154">특정 컨트롤러의 특성</span><span class="sxs-lookup"><span data-stu-id="8b236-154">Attribute on specific controllers</span></span>

<span data-ttu-id="8b236-155">프로젝트 템플릿의 다음 예제에서처럼 `[ApiController]` 특성을 특정 컨트롤러에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-155">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="8b236-156">여러 컨트롤러의 특성</span><span class="sxs-lookup"><span data-stu-id="8b236-156">Attribute on multiple controllers</span></span>

<span data-ttu-id="8b236-157">둘 이상의 컨트롤러에서 특성을 사용하는 방법으로 `[ApiController]` 특성으로 주석이 추가된 사용자 지정 기본 컨트롤러 클래스를 만드는 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-157">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="8b236-158">다음 예제는 사용자 지정 기본 클래스와 여기에서 파생되는 컨트롤러를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-158">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="8b236-159">어셈블리의 특성</span><span class="sxs-lookup"><span data-stu-id="8b236-159">Attribute on an assembly</span></span>

<span data-ttu-id="8b236-160">[호환성 버전](xref:mvc/compatibility-version)이 2.2 이상으로 설정된 경우 `[ApiController]` 특성을 어셈블리에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-160">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="8b236-161">이 방식의 주석은 웹 API 동작을 어셈블리의 모든 컨트롤러에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-161">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="8b236-162">개별 컨트롤러를 대상으로 옵트아웃하는 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-162">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="8b236-163">어셈블리 수준 특성을 `Startup` 클래스 주위의 네임스페이스 선언에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-163">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a><span data-ttu-id="8b236-164">특성 라우팅 요구 사항</span><span class="sxs-lookup"><span data-stu-id="8b236-164">Attribute routing requirement</span></span>

<span data-ttu-id="8b236-165">`[ApiController]` 특성은 특성 라우팅을 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-165">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="8b236-166">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="8b236-166">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="8b236-167">작업은 `Startup.Configure`의 `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> 또는 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A>에 의해 정의된 [규칙 기반 경로](xref:mvc/controllers/routing#conventional-routing)를 통해 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="8b236-168">작업은 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>에 의해 정의된 [규칙 기반 경로](xref:mvc/controllers/routing#conventional-routing)를 통해 또는 `Startup.Configure`의 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A>에서 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-168">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="8b236-169">자동 HTTP 400 응답</span><span class="sxs-lookup"><span data-stu-id="8b236-169">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="8b236-170">`[ApiController]` 특성은 HTTP 400 응답을 자동으로 트리거하는 모델 유효성 검사 오류를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-170">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="8b236-171">따라서 다음 코드는 작업 메서드에서 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-171">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="8b236-172">ASP.NET Core MVC는 <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> 작업 필터를 사용하여 위의 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-172">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="8b236-173">기본 BadRequest 응답</span><span class="sxs-lookup"><span data-stu-id="8b236-173">Default BadRequest response</span></span>

<span data-ttu-id="8b236-174">호환성 버전이 2.1이면 HTTP 400 응답에 대한 기본 응답 형식은 <xref:Microsoft.AspNetCore.Mvc.SerializableError>입니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-174">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="8b236-175">다음 요청 본문은 직렬화된 형식의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-175">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8b236-176">호환성 버전이 2.2 이상이면 HTTP 400 응답에 대한 기본 응답 형식은 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>입니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-176">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="8b236-177">다음 요청 본문은 직렬화된 형식의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-177">The following request body is an example of the serialized type:</span></span>

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

<span data-ttu-id="8b236-178">`ValidationProblemDetails` 형식:</span><span class="sxs-lookup"><span data-stu-id="8b236-178">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="8b236-179">웹 API 응답에서 오류를 지정하기 위해 머신이 읽을 수 있는 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-179">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="8b236-180">[RFC 7807 사양](https://tools.ietf.org/html/rfc7807)을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-180">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

<span data-ttu-id="8b236-181">자동 및 사용자 지정 응답을 일관성 있게 설정하려면 <xref:System.Web.Http.ApiController.BadRequest%2A> 대신 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-181">To make automatic and custom responses consistent, call the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A> method instead of <xref:System.Web.Http.ApiController.BadRequest%2A>.</span></span> <span data-ttu-id="8b236-182">`ValidationProblem`은 자동 응답뿐만 아니라 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 개체를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-182">`ValidationProblem` returns a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> object as well as the automatic response.</span></span>

### <a name="log-automatic-400-responses"></a><span data-ttu-id="8b236-183">자동 400 응답 기록</span><span class="sxs-lookup"><span data-stu-id="8b236-183">Log automatic 400 responses</span></span>

<span data-ttu-id="8b236-184">[How to log automatic 400 responses on model validation errors (dotnet/AspNetCore.Docs#12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157)(모델 유효성 검사 오류에서 자동 400 응답을 기록하는 방법(dotnet/AspNetCore.Docs#12157))를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8b236-184">See [How to log automatic 400 responses on model validation errors (dotnet/AspNetCore.Docs#12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="8b236-185">자동 400 응답 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="8b236-185">Disable automatic 400 response</span></span>

<span data-ttu-id="8b236-186">자동 400 동작을 사용하지 않으려면 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-186">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="8b236-187">`Startup.ConfigureServices`에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-187">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="8b236-188">바인딩 소스 매개 변수 유추</span><span class="sxs-lookup"><span data-stu-id="8b236-188">Binding source parameter inference</span></span>

<span data-ttu-id="8b236-189">바인딩 소스 특성은 작업 매개 변수 값이 발견되는 위치를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-189">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="8b236-190">다음 바인딩 소스 특성이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-190">The following binding source attributes exist:</span></span>

|<span data-ttu-id="8b236-191">특성</span><span class="sxs-lookup"><span data-stu-id="8b236-191">Attribute</span></span>|<span data-ttu-id="8b236-192">바인딩 원본</span><span class="sxs-lookup"><span data-stu-id="8b236-192">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="8b236-193">요청 본문</span><span class="sxs-lookup"><span data-stu-id="8b236-193">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="8b236-194">요청 본문에서 양식 데이터</span><span class="sxs-lookup"><span data-stu-id="8b236-194">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="8b236-195">요청 헤더</span><span class="sxs-lookup"><span data-stu-id="8b236-195">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="8b236-196">요청 쿼리 문자열 매개 변수</span><span class="sxs-lookup"><span data-stu-id="8b236-196">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="8b236-197">현재 요청의 경로 데이터</span><span class="sxs-lookup"><span data-stu-id="8b236-197">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="8b236-198">작업 매개 변수로 삽입된 요청 서비스</span><span class="sxs-lookup"><span data-stu-id="8b236-198">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="8b236-199">값에 `%2f`(즉, `/`)이 포함될 수 있는 경우 `[FromRoute]`를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-199">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="8b236-200">`%2f`는 `/`로 이스케이프가 해제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-200">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="8b236-201">값에 `%2f`가 포함될 수 있으면 `[FromQuery]`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-201">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="8b236-202">`[ApiController]` 특성 또는 `[FromQuery]` 같은 다른 바인딩 소스 특성 없이, ASP.NET Core 런타임에서 복합 개체 모델 바인더 사용을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-202">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="8b236-203">복합 개체 모델 바인더는 정의된 순서로 값 공급자로부터 데이터를 풀합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-203">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="8b236-204">다음 예제에서 `[FromQuery]` 특성은 `discontinuedOnly` 매개 변수 값이 요청 URL의 쿼리 문자열에 제공됨을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-204">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="8b236-205">`[ApiController]` 특성은 작업 매개 변수의 기본 데이터 원본에 유추 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-205">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="8b236-206">이러한 규칙을 통해 작업 매개 변수에 특성을 적용하여 바인딩 소스를 수동으로 식별할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-206">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="8b236-207">바인딩 소스 유추 규칙은 다음과 같이 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-207">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="8b236-208">`[FromBody]`는 복합 형식 매개 변수에 대해 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-208">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="8b236-209">`[FromBody]` 유추 규칙은 <xref:Microsoft.AspNetCore.Http.IFormCollection> 및 <xref:System.Threading.CancellationToken> 같이 특별한 의미를 지닌 복합 기본 제공 형식에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-209">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="8b236-210">바인딩 소스 유추 코드는 이러한 특별한 형식을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-210">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="8b236-211">`[FromForm]`은 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 <xref:Microsoft.AspNetCore.Http.IFormFileCollection> 형식의 작업 매개 변수에 대해 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-211">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="8b236-212">단순 또는 사용자 정의 형식에 대해서는 유추되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-212">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="8b236-213">`[FromRoute]`는 경로 템플릿에서 매개 변수와 일치하는 작업 매개 변수 이름에 대해 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-213">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="8b236-214">둘 이상의 경로가 작업 매개 변수와 일치하는 경우 모든 경로 값은 `[FromRoute]`로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-214">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="8b236-215">`[FromQuery]`는 다른 작업 매개 변수에 대해 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-215">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="8b236-216">FromBody 유추 메모</span><span class="sxs-lookup"><span data-stu-id="8b236-216">FromBody inference notes</span></span>

<span data-ttu-id="8b236-217">`[FromBody]`는 `string` 또는 `int` 같은 단순 형식에 대해 유추되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-217">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="8b236-218">따라서 해당 기능이 필요한 경우 단순 형식에 `[FromBody]` 특성을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-218">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="8b236-219">작업에 요청 본문에서 바인딩된 매개 변수가 두 개 이상 있는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-219">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="8b236-220">예를 들어 다음 모든 작업 메서드 시그니처로 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-220">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="8b236-221">복합 형식이므로 둘 다에서 유추된 `[FromBody]`</span><span class="sxs-lookup"><span data-stu-id="8b236-221">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="8b236-222">하나의 `[FromBody]` 특성, 복합 형식이므로 다른 하나에서 유추됨</span><span class="sxs-lookup"><span data-stu-id="8b236-222">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="8b236-223">둘 다의 `[FromBody]` 특성</span><span class="sxs-lookup"><span data-stu-id="8b236-223">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="8b236-224">ASP.NET Core 2.1에서 목록, 배열 등의 컬렉션 형식 매개 변수를 `[FromQuery]`로 잘못 유추합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-224">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="8b236-225">이러한 매개 변수가 요청 본문에서 바인딩되는 경우 해당 매개 변수에 `[FromBody]` 특성을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-225">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="8b236-226">이 문제는 ASP.NET Core 2.2 이상에서 수정됩니다. 즉, 기본적으로 컬렉션 형식 매개 변수를 본문에서 바인딩되도록 유추합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-226">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="8b236-227">유추 규칙 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="8b236-227">Disable inference rules</span></span>

<span data-ttu-id="8b236-228">바인딩 소스 유추를 사용하지 않으려면 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters>를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-228">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="8b236-229">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-229">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="8b236-230">다중 파트/폼 데이터 요청 유추</span><span class="sxs-lookup"><span data-stu-id="8b236-230">Multipart/form-data request inference</span></span>

<span data-ttu-id="8b236-231">작업 매개 변수를 [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) 특성으로 주석 처리하는 경우 `[ApiController]` 특성이 유추 규칙에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-231">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="8b236-232">`multipart/form-data` 요청 콘텐츠 형식이 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-232">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="8b236-233">기본 동작을 사용하지 않으려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-233">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="8b236-234">오류 상태 코드에 대한 문제 세부 정보</span><span class="sxs-lookup"><span data-stu-id="8b236-234">Problem details for error status codes</span></span>

<span data-ttu-id="8b236-235">호환성 버전이 2.2 이상인 경우 MVC는 오류 결과(상태 코드 400 이상의 결과)를 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>가 있는 결과로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-235">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="8b236-236">`ProblemDetails` 형식은 HTTP 응답에 머신에서 읽을 수 있는 오류 세부 정보를 제공하기 위해 [RFC 7807 사양](https://tools.ietf.org/html/rfc7807)을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-236">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="8b236-237">다음 컨트롤러 작업에서 다음 코드를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="8b236-237">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="8b236-238">`NotFound` 메서드는 `ProblemDetails` 본문이 포함된 HTTP 404 상태 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-238">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="8b236-239">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="8b236-239">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="8b236-240">ProblemDetails 응답 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="8b236-240">Disable ProblemDetails response</span></span>

<span data-ttu-id="8b236-241"><xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> 속성이 `true`로 설정된 경우 오류 상태 코드에 대한 `ProblemDetails` 자동 생성이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-241">The automatic creation of a `ProblemDetails` for error status codes is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="8b236-242">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-242">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a><span data-ttu-id="8b236-243">[사용] 특성을 사용하여 지원되는 요청 콘텐츠 형식 정의</span><span class="sxs-lookup"><span data-stu-id="8b236-243">Define supported request content types with the [Consumes] attribute</span></span>

<span data-ttu-id="8b236-244">기본적으로 작업은 사용 가능한 모든 요청 콘텐츠 형식을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-244">By default, an action supports all available request content types.</span></span> <span data-ttu-id="8b236-245">예를 들어 앱이 JSON 및 XML [입력 포맷터](xref:mvc/models/model-binding#input-formatters)를 모두 지원하도록 구성된 경우 작업은 `application/json`과 `application/xml`을 비롯한 여러 콘텐츠 형식을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-245">For example, if an app is configured to support both JSON and XML [input formatters](xref:mvc/models/model-binding#input-formatters), an action supports multiple content types, including `application/json` and `application/xml`.</span></span>

<span data-ttu-id="8b236-246">[[사용]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 특성을 사용하면 작업에서 지원되는 요청 콘텐츠 형식을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-246">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="8b236-247">하나 이상의 콘텐츠 형식을 지정하여 작업 또는 컨트롤러에 `[Consumes]` 특성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-247">Apply the `[Consumes]` attribute to an action or controller, specifying one or more content types:</span></span>

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

<span data-ttu-id="8b236-248">위의 코드에서 `CreateProduct` 동작은 콘텐츠 형식을 `application/xml`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-248">In the preceding code, the `CreateProduct` action specifies the content type `application/xml`.</span></span> <span data-ttu-id="8b236-249">이 작업으로 라우팅되는 요청은 `application/xml`의 `Content-Type` 헤더를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-249">Requests routed to this action must specify a `Content-Type` header of `application/xml`.</span></span> <span data-ttu-id="8b236-250">`application/xml`의 `Content-Type` 헤더를 지정하지 않는 요청은 [415 지원되지 않는 미디어 유형](https://developer.mozilla.org/docs/Web/HTTP/Status/415) 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-250">Requests that don't specify a `Content-Type` header of `application/xml` result in a [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) response.</span></span>

<span data-ttu-id="8b236-251">또한 `[Consumes]` 특성을 사용하면 형식 제약 조건을 적용함으로써 들어오는 요청의 콘텐츠 형식에 따라 작업의 선택에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-251">The `[Consumes]` attribute also allows an action to influence its selection based on an incoming request's content type by applying a type constraint.</span></span> <span data-ttu-id="8b236-252">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8b236-252">Consider the following example:</span></span>

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

<span data-ttu-id="8b236-253">위의 코드에서 `ConsumesController`는 `https://localhost:5001/api/Consumes` URL에 전송된 요청을 처리하도록 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-253">In the preceding code, `ConsumesController` is configured to handle requests sent to the `https://localhost:5001/api/Consumes` URL.</span></span> <span data-ttu-id="8b236-254">컨트롤러의 작업 `PostJson` 및 `PostForm` 모두 동일한 URL을 사용하여 POST 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-254">Both of the controller's actions, `PostJson` and `PostForm`, handle POST requests with the same URL.</span></span> <span data-ttu-id="8b236-255">형식 제약 조건을 적용하는 `[Consumes]` 특성이 없으면 모호한 일치 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-255">Without the `[Consumes]` attribute applying a type constraint, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="8b236-256">`[Consumes]` 특성이 두 작업 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-256">The `[Consumes]` attribute is applied to both actions.</span></span> <span data-ttu-id="8b236-257">`PostJson` 작업은 `application/json`의 `Content-Type` 헤더와 함께 전송된 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-257">The `PostJson` action handles requests sent with a `Content-Type` header of `application/json`.</span></span> <span data-ttu-id="8b236-258">`PostForm` 작업은 `application/x-www-form-urlencoded`의 `Content-Type` 헤더와 함께 전송된 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8b236-258">The `PostForm` action handles requests sent with a `Content-Type` header of `application/x-www-form-urlencoded`.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="8b236-259">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8b236-259">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
* [<span data-ttu-id="8b236-260">Microsoft Learn: ASP.NET Core를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="8b236-260">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
