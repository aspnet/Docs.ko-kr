---
title: ASP.NET Core의 기본 JSON Api Route-to-code
author: jamesnk
description: Route-to-code및 json 확장 메서드를 사용 하 여 간단한 json 웹 api를 만드는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: f8a3804a887ebfa0f5284d8991e903c978b18208
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556608"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="79978-103">ASP.NET Core의 기본 JSON Api Route-to-code</span><span class="sxs-lookup"><span data-stu-id="79978-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="79978-104">작성자: [James Newton-King](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="79978-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="79978-105">ASP.NET Core은 JSON 웹 Api를 만드는 다양 한 방법을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="79978-106">[ASP.NET Core WEB api](xref:web-api/index) 는 api를 만들기 위한 전체 프레임 워크를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="79978-107">에서 상속 하 여 서비스를 만듭니다 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="79978-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="79978-108">프레임 워크에서 제공 하는 일부 기능에는 모델 바인딩, 유효성 검사, 콘텐츠 협상, 입력 및 출력 형식 및 OpenAPI가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="79978-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="79978-109">Route-to-code 는 web API를 ASP.NET Core 하는 프레임 워크가 아닌 대안입니다.</span><span class="sxs-lookup"><span data-stu-id="79978-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="79978-110">Route-to-code[ASP.NET Core 라우팅을](xref:fundamentals/routing) 코드에 직접 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="79978-111">코드에서 요청을 읽고 응답을 씁니다.</span><span class="sxs-lookup"><span data-stu-id="79978-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="79978-112">Route-to-code 에는 web API의 고급 기능이 없지만이 기능을 사용 하는 데 필요한 구성도 없습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="79978-113">Route-to-code 는 작은 및 기본 JSON 웹 Api를 빌드할 때 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="79978-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="79978-114">JSON 웹 Api 만들기</span><span class="sxs-lookup"><span data-stu-id="79978-114">Create JSON web APIs</span></span>

<span data-ttu-id="79978-115">ASP.NET Core은 JSON 웹 Api를 쉽게 만들 도우미 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="79978-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A>`Content-Type`JSON 콘텐츠 형식의 헤더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="79978-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> 요청에서 JSON을 읽고 지정 된 형식으로 deserialize 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="79978-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> 지정 된 값을 JSON으로 응답 본문에 쓰고 응답 콘텐츠 형식을로 설정 합니다 `application/json` .</span><span class="sxs-lookup"><span data-stu-id="79978-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="79978-119">*Startup.cs* 에는 간단한 경로 기반 JSON api가 지정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="79978-120">경로 및 API 논리는 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 앱의 요청 파이프라인의 일부로에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="79978-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="79978-121">JSON 응답 쓰기</span><span class="sxs-lookup"><span data-stu-id="79978-121">Write JSON response</span></span>

<span data-ttu-id="79978-122">앱에 대 한 JSON API를 구성 하는 다음 코드를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="79978-123">앞의 코드가 하는 역할은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-123">The preceding code:</span></span>

* <span data-ttu-id="79978-124">을 경로 템플릿으로 사용 하 여 HTTP GET API 끝점을 추가 `/hello/{name:alpha}` 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="79978-125">경로가 일치 하는 경우 API는 `name` 요청에서 경로 값을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="79978-126">를 사용 하 여 익명 형식을 JSON 응답으로 씁니다 `WriteAsJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="79978-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="79978-127">JSON 요청 읽기</span><span class="sxs-lookup"><span data-stu-id="79978-127">Read JSON request</span></span>

<span data-ttu-id="79978-128">`HasJsonContentType` 및 `ReadFromJsonAsync` 는 경로 기반 JSON API에서 json 응답을 deserialize 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="79978-129">앞의 코드가 하는 역할은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-129">The preceding code:</span></span>

* <span data-ttu-id="79978-130">을 경로 템플릿으로 사용 하 여 HTTP POST API 끝점을 추가 `/weather` 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="79978-131">경로가 일치 하면는 `HasJsonContentType` 요청 콘텐츠 형식의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="79978-132">비 JSON 콘텐츠 형식이 415 상태 코드를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="79978-133">콘텐츠 형식이 JSON 인 경우 요청 콘텐츠는에서 deserialize 됩니다 `ReadFromJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="79978-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="79978-134">JSON serialization 구성</span><span class="sxs-lookup"><span data-stu-id="79978-134">Configure JSON serialization</span></span>

<span data-ttu-id="79978-135">JSON serialization을 사용자 지정 하는 방법에는 다음 두 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="79978-136">메서드에서 기본 직렬화 옵션을 구성할 수 있습니다 <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="79978-136">Default serialization options can be configured with <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="79978-137">`WriteAsJsonAsync` 및에는 `ReadFromJsonAsync` 개체를 허용 하는 오버 로드가 있습니다 <xref:System.Text.Json.JsonSerializerOptions> .</span><span class="sxs-lookup"><span data-stu-id="79978-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a <xref:System.Text.Json.JsonSerializerOptions> object.</span></span> <span data-ttu-id="79978-138">이 옵션 개체는 기본 옵션을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-138">This options object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="79978-139">인증 및 권한 부여</span><span class="sxs-lookup"><span data-stu-id="79978-139">Authentication and authorization</span></span>

<span data-ttu-id="79978-140">Route-to-code 인증 및 권한 부여를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="79978-141">및와 같은 특성 `[Authorize]` `[AllowAnonymous]` 은 요청 대리자에 매핑되는 끝점에 배치할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="79978-142">대신 및 확장 메서드를 사용 하 여 권한 부여 메타 데이터를 추가 <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-142">Instead, authorization metadata is added using the <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="79978-143">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="79978-143">Dependency injection</span></span>

<span data-ttu-id="79978-144">생성자를 사용 하는 [DI (종속성 주입)](xref:fundamentals/dependency-injection) 는와 함께 사용할 수 없습니다 Route-to-code .</span><span class="sxs-lookup"><span data-stu-id="79978-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="79978-145">Web API는 생성자에 삽입 된 서비스를 사용 하 여 컨트롤러를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="79978-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="79978-146">끝점을 실행할 때는 형식이 생성 되지 않으므로 서비스를 수동으로 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="79978-147">경로 기반 Api는를 사용 <xref:System.IServiceProvider> 하 여 서비스를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="79978-148">와 같은 임시 및 범위가 지정 된 수명 서비스 `DbContext` 는 끝점의 요청 대리자 내에서 [HttpContext 서비스](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 에서 확인 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="79978-149">`ILogger` [IEndpointRouteBuilder. ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)에서와 같은 Singleton 수명 서비스를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="79978-150">서비스는 요청 위임 외부에서 확인 되 고 끝점 간에 공유 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="79978-151">DI를 광범위 하 게 사용 하는 Api는 DI를 지 원하는 ASP.NET Core 앱 유형을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="79978-152">예를 들어 웹 API를 ASP.NET Core 합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="79978-153">컨트롤러의 생성자를 사용 하는 서비스 주입은 수동으로 서비스를 해결 하는 것 보다 더 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="79978-154">API 프로젝트 구조</span><span class="sxs-lookup"><span data-stu-id="79978-154">API project structure</span></span>

<span data-ttu-id="79978-155">경로 기반 Api는 *Startup.cs* 에서 찾을 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="79978-156">Api는 다른 파일에 배치 하 고 시작 시에 매핑할 수 있습니다 `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="79978-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="79978-157">이 방법은 시작 구성 파일 크기를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="79978-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="79978-158">`UserApi`메서드를 정의 하는 다음 정적 클래스를 살펴보겠습니다 `Map` .</span><span class="sxs-lookup"><span data-stu-id="79978-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="79978-159">메서드는 경로 기반 Api를 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="79978-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="79978-160">`Startup.Configure`메서드에서 `Map` 메서드 및 다른 클래스의 정적 메서드는에서 호출 됩니다 `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="79978-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a><span data-ttu-id="79978-161">Web API와 비교 하 여 주목할 만한 누락 기능</span><span class="sxs-lookup"><span data-stu-id="79978-161">Notable missing features compared to Web API</span></span>

<span data-ttu-id="79978-162">Route-to-code 는 기본 JSON Api 용으로 설계 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-162">Route-to-code is designed for basic JSON APIs.</span></span> <span data-ttu-id="79978-163">ASP.NET Core Web API에서 제공 하는 다양 한 고급 기능을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-163">It doesn't have support for many of the advanced features provided by ASP.NET Core Web API.</span></span>

<span data-ttu-id="79978-164">에서 제공 하지 않는 기능에 Route-to-code 는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="79978-164">Features not provided by Route-to-code include:</span></span>

* <span data-ttu-id="79978-165">모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="79978-165">Model binding</span></span>
* <span data-ttu-id="79978-166">모델 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="79978-166">Model validation</span></span>
* <span data-ttu-id="79978-167">OpenAPI/Swagger</span><span class="sxs-lookup"><span data-stu-id="79978-167">OpenAPI/Swagger</span></span>
* <span data-ttu-id="79978-168">콘텐츠 협상</span><span class="sxs-lookup"><span data-stu-id="79978-168">Content negotiation</span></span>
* <span data-ttu-id="79978-169">생성자 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="79978-169">Constructor dependency injection</span></span>
* <span data-ttu-id="79978-170">`ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))</span><span class="sxs-lookup"><span data-stu-id="79978-170">`ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))</span></span>

<span data-ttu-id="79978-171">[ASP.NET Core WEB api](xref:web-api/index) 를 사용 하 여 위의 목록에 있는 일부 기능이 필요한 경우 api를 만드는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="79978-171">Consider using [ASP.NET Core web API](xref:web-api/index) to create an API if it requires some of the features in the preceding list.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="79978-172">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="79978-172">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
