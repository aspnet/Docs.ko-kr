---
title: ASP.NET Core 웹 API에서 오류 처리
author: pranavkm
description: ASP.NET Core 웹 API를 사용한 오류 처리에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 1/11/2021
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
uid: web-api/handle-errors
ms.openlocfilehash: f9cc38f444bd3db826595b7de64db05792915a23
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585723"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="07c70-103">ASP.NET Core 웹 API에서 오류 처리</span><span class="sxs-lookup"><span data-stu-id="07c70-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="07c70-104">이 문서에서는 ASP.NET Core 웹 API를 사용하여 오류를 처리하고 사용자 지정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="07c70-105">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/handle-errors/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07c70-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="07c70-106">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="07c70-106">Developer Exception Page</span></span>

<span data-ttu-id="07c70-107">[개발자 예외 페이지](xref:fundamentals/error-handling)는 서버 오류에 대한 자세한 스택 추적을 가져오는 데 유용한 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="07c70-108">이것은 <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware>를 사용하여 HTTP 파이프라인에서 동기 및 비동기 예외를 캡처하고 오류 응답을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="07c70-109">설명하려면 다음 컨트롤러 작업을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="07c70-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="07c70-110">다음 `curl` 명령을 실행하여 이전 작업을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07c70-111">ASP.NET Core 3.0 이전에서 클라이언트가 HTML 형식의 출력을 요청하지 않는 경우 개발자 예외 페이지에 일반 텍스트 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="07c70-112">다음과 같은 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="07c70-113">HTML 형식의 응답을 대신 표시하려면 `Accept` HTTP 요청 헤더를 `text/html` 미디어 유형으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="07c70-114">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="07c70-115">HTTP 응답에서 다음 발췌 내용을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="07c70-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="07c70-116">ASP.NET Core 2.2 이전 버전에서 개발자 예외 페이지에는 HTML 형식 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="07c70-117">예를 들어 HTTP 응답에서 다음 발췌 내용을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="07c70-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07c70-118">HTML 형식 응답은 Postman과 같은 도구를 사용하여 테스트하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="07c70-119">다음 화면 캡처는 Postman에 표시되는 일반 텍스트와 HTML 형식 응답을 모두 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Postman에서 개발자 예외 페이지 테스트](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="07c70-121">**앱이 개발 환경에서 실행 중인 경우에만** 개발자 예외 페이지를 사용하도록 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="07c70-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="07c70-122">앱이 프로덕션 환경에서 실행 되는 경우 자세한 예외 정보를 공개적으로 공유 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="07c70-122">Don't share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="07c70-123">환경 구성 방법에 대한 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07c70-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>
>
> <span data-ttu-id="07c70-124">오류 처리기 작업 메서드를 `HttpGet`와 같은 HTTP 메서드 특성을 사용하여 표시하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="07c70-124">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="07c70-125">명시적 동사는 일부 요청이 작업 메서드에 도달하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-125">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="07c70-126">인증 되지 않은 사용자에 게 오류가 표시 되는 경우 메서드에 대 한 익명 액세스를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-126">Allow anonymous access to the method if unauthenticated users should see the error.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="07c70-127">예외 처리기</span><span class="sxs-lookup"><span data-stu-id="07c70-127">Exception handler</span></span>

<span data-ttu-id="07c70-128">비개발 환경에서는 [예외 처리 미들웨어](xref:fundamentals/error-handling)를 사용하여 오류 페이로드를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-128">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="07c70-129">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>를 호출하여 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-129">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="07c70-130">`/error` 경로에 응답하도록 컨트롤러 작업을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-130">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="07c70-131">위의 `Error` 작업은 [RFC 7807](https://tools.ietf.org/html/rfc7807) 호환 페이로드를 클라이언트에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-131">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="07c70-132">예외 처리 미들웨어는 로컬 개발 환경에서 보다 자세한 콘텐츠 협상 출력을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-132">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="07c70-133">다음 단계를 사용하여 개발 및 프로덕션 환경에서 일관된 페이로드 형식을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-133">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="07c70-134">`Startup.Configure`에서 환경별 예외 처리 미들웨어 인스턴스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-134">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="07c70-135">위의 코드에서 미들웨어는 다음에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-135">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="07c70-136">개발 환경에서 `/error-local-development`의 경로.</span><span class="sxs-lookup"><span data-stu-id="07c70-136">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="07c70-137">개발되지 않은 환경에서 `/error`의 경로.</span><span class="sxs-lookup"><span data-stu-id="07c70-137">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="07c70-138">컨트롤러 작업에 특성 라우팅 적용:</span><span class="sxs-lookup"><span data-stu-id="07c70-138">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    <span data-ttu-id="07c70-139">위의 코드는 [Controllerbase. 문제](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) 를 호출 하 여 응답을 만듭니다. <xref:Microsoft.AspNetCore.Mvc.ProblemDetails></span><span class="sxs-lookup"><span data-stu-id="07c70-139">The preceding code calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response.</span></span>

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="07c70-140">예외를 사용하여 응답 수정</span><span class="sxs-lookup"><span data-stu-id="07c70-140">Use exceptions to modify the response</span></span>

<span data-ttu-id="07c70-141">응답의 내용은 컨트롤러 외부에서 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-141">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="07c70-142">ASP.NET 4.x Web API에서 이것을 수행하는 한 가지 방법은 <xref:System.Web.Http.HttpResponseException> 형식을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-142">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="07c70-143">ASP.NET Core에는 동일한 형식이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-143">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="07c70-144">다음 단계로 `HttpResponseException`에 대한 지원을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-144">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="07c70-145">`HttpResponseException`이라는 잘 알려진 예외 형식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-145">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="07c70-146">`HttpResponseExceptionFilter`이라는 작업 필터를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-146">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    <span data-ttu-id="07c70-147">위의 필터는 `Order` 최대 정수 값에서 10을 뺀 값을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-147">The preceding filter specifies an `Order` of the maximum integer value minus 10.</span></span> <span data-ttu-id="07c70-148">이렇게 하면 파이프라인 끝에서 다른 필터를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-148">This allows other filters to run at the end of the pipeline.</span></span>

1. <span data-ttu-id="07c70-149">`Startup.ConfigureServices`에서 필터 컬렉션에 작업 필터를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-149">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="07c70-150">유효성 검사 실패 오류 응답</span><span class="sxs-lookup"><span data-stu-id="07c70-150">Validation failure error response</span></span>

<span data-ttu-id="07c70-151">Web API 컨트롤러의 경우, 모델 유효성 검사에 실패하면 MVC는 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 응답 형식으로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-151">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="07c70-152">MVC는 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>의 결과를 사용하여 유효성 검사 실패에 대한 오류 응답을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-152">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="07c70-153">다음 예제에서는 팩터리를 사용하여 `Startup.ConfigureServices`에서 기본 응답 형식을 <xref:Microsoft.AspNetCore.Mvc.SerializableError>로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-153">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="07c70-154">클라이언트 오류 응답</span><span class="sxs-lookup"><span data-stu-id="07c70-154">Client error response</span></span>

<span data-ttu-id="07c70-155">*오류 결과* 는 400 이상의 HTTP 상태 코드를 가진 결과로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-155">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="07c70-156">Web API 컨트롤러의 경우, MVC는 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>의 결과로 오류 결과를 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-156">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="07c70-157">ASP.NET Core 2.1은 RFC 7807을 거의 준수하는 문제 세부 정보 응답을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-157">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="07c70-158">100% 준수가 중요한 경우 프로젝트를 ASP.NET Core 2.2 이상으로 업그레이드하세요.</span><span class="sxs-lookup"><span data-stu-id="07c70-158">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07c70-159">오류 응답은 다음 방법 중 하나로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-159">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="07c70-160">ProblemDetailsFactory 구현</span><span class="sxs-lookup"><span data-stu-id="07c70-160">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="07c70-161">ApiBehaviorOptions.ClientErrorMapping 사용</span><span class="sxs-lookup"><span data-stu-id="07c70-161">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="07c70-162">`ProblemDetailsFactory` 구현</span><span class="sxs-lookup"><span data-stu-id="07c70-162">Implement `ProblemDetailsFactory`</span></span>

<span data-ttu-id="07c70-163">MVC는 <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName>를 사용하여 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 및 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>의 모든 인스턴스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-163">MVC uses <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="07c70-164">여기에는 클라이언트 오류 응답, 유효성 검사 실패 오류 응답, <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> 및 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> 도우미 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-164">This includes client error responses, validation failure error responses, and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> helper methods.</span></span>

<span data-ttu-id="07c70-165">문제 세부 정보 응답을 사용자 지정하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory>의 사용자 지정 구현을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-165">To customize the problem details response, register a custom implementation of <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="07c70-166">오류 응답은 [ApiBehaviorOptions.ClientErrorMapping 사용](#use-apibehavioroptionsclienterrormapping) 섹션에 설명된 대로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-166">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="07c70-167">ApiBehaviorOptions.ClientErrorMapping 사용</span><span class="sxs-lookup"><span data-stu-id="07c70-167">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="07c70-168"><xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> 속성을 사용하여 `ProblemDetails` 응답의 내용을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-168">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="07c70-169">예를 들어 `Startup.ConfigureServices` 내의 다음 코드는 404 응답에 대해 `type` 속성을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-169">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a><span data-ttu-id="07c70-170">예외를 처리 하는 사용자 지정 미들웨어</span><span class="sxs-lookup"><span data-stu-id="07c70-170">Custom Middleware to handle exceptions</span></span>

<span data-ttu-id="07c70-171">예외 처리 미들웨어의 기본값은 대부분의 앱에서 잘 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-171">The defaults in the exception handling middleware works well for most apps.</span></span> <span data-ttu-id="07c70-172">특수 한 예외 처리를 필요로 하는 앱의 경우 [예외 처리 미들웨어를 사용자 지정 하는](xref:fundamentals/error-handling#exception-handler-lambda)것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="07c70-172">For apps that require specialized exception handling, consider [customizing the exception handling middleware](xref:fundamentals/error-handling#exception-handler-lambda).</span></span>
