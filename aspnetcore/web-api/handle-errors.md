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
# <a name="handle-errors-in-aspnet-core-web-apis"></a>ASP.NET Core 웹 API에서 오류 처리

이 문서에서는 ASP.NET Core 웹 API를 사용하여 오류를 처리하고 사용자 지정하는 방법을 설명합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/handle-errors/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>개발자 예외 페이지

[개발자 예외 페이지](xref:fundamentals/error-handling)는 서버 오류에 대한 자세한 스택 추적을 가져오는 데 유용한 도구입니다. 이것은 <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware>를 사용하여 HTTP 파이프라인에서 동기 및 비동기 예외를 캡처하고 오류 응답을 생성합니다. 설명하려면 다음 컨트롤러 작업을 고려하세요.

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

다음 `curl` 명령을 실행하여 이전 작업을 테스트합니다.

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 3.0 이전에서 클라이언트가 HTML 형식의 출력을 요청하지 않는 경우 개발자 예외 페이지에 일반 텍스트 응답이 표시됩니다. 다음과 같은 출력이 표시됩니다.

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

HTML 형식의 응답을 대신 표시하려면 `Accept` HTTP 요청 헤더를 `text/html` 미디어 유형으로 설정합니다. 예를 들면 다음과 같습니다.

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

HTTP 응답에서 다음 발췌 내용을 살펴보세요.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

ASP.NET Core 2.2 이전 버전에서 개발자 예외 페이지에는 HTML 형식 응답이 표시됩니다. 예를 들어 HTTP 응답에서 다음 발췌 내용을 살펴보세요.

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

HTML 형식 응답은 Postman과 같은 도구를 사용하여 테스트하는 경우에 유용합니다. 다음 화면 캡처는 Postman에 표시되는 일반 텍스트와 HTML 형식 응답을 모두 보여 줍니다.

![Postman에서 개발자 예외 페이지 테스트](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> **앱이 개발 환경에서 실행 중인 경우에만** 개발자 예외 페이지를 사용하도록 설정하세요. 앱이 프로덕션 환경에서 실행 되는 경우 자세한 예외 정보를 공개적으로 공유 하지 마세요. 환경 구성 방법에 대한 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.
>
> 오류 처리기 작업 메서드를 `HttpGet`와 같은 HTTP 메서드 특성을 사용하여 표시하지 마세요. 명시적 동사는 일부 요청이 작업 메서드에 도달하지 못하도록 합니다. 인증 되지 않은 사용자에 게 오류가 표시 되는 경우 메서드에 대 한 익명 액세스를 허용 합니다.

## <a name="exception-handler"></a>예외 처리기

비개발 환경에서는 [예외 처리 미들웨어](xref:fundamentals/error-handling)를 사용하여 오류 페이로드를 생성할 수 있습니다.

1. `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>를 호출하여 미들웨어를 사용합니다.

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. `/error` 경로에 응답하도록 컨트롤러 작업을 구성합니다.

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

위의 `Error` 작업은 [RFC 7807](https://tools.ietf.org/html/rfc7807) 호환 페이로드를 클라이언트에 보냅니다.

예외 처리 미들웨어는 로컬 개발 환경에서 보다 자세한 콘텐츠 협상 출력을 제공할 수도 있습니다. 다음 단계를 사용하여 개발 및 프로덕션 환경에서 일관된 페이로드 형식을 생성합니다.

1. `Startup.Configure`에서 환경별 예외 처리 미들웨어 인스턴스를 등록합니다.

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

    위의 코드에서 미들웨어는 다음에 등록됩니다.

    * 개발 환경에서 `/error-local-development`의 경로.
    * 개발되지 않은 환경에서 `/error`의 경로.
    
1. 컨트롤러 작업에 특성 라우팅 적용:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    위의 코드는 [Controllerbase. 문제](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) 를 호출 하 여 응답을 만듭니다. <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>

## <a name="use-exceptions-to-modify-the-response"></a>예외를 사용하여 응답 수정

응답의 내용은 컨트롤러 외부에서 수정할 수 있습니다. ASP.NET 4.x Web API에서 이것을 수행하는 한 가지 방법은 <xref:System.Web.Http.HttpResponseException> 형식을 사용하는 것입니다. ASP.NET Core에는 동일한 형식이 포함되지 않습니다. 다음 단계로 `HttpResponseException`에 대한 지원을 추가할 수 있습니다.

1. `HttpResponseException`이라는 잘 알려진 예외 형식을 만듭니다.

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. `HttpResponseExceptionFilter`이라는 작업 필터를 만듭니다.

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    위의 필터는 `Order` 최대 정수 값에서 10을 뺀 값을 지정 합니다. 이렇게 하면 파이프라인 끝에서 다른 필터를 실행할 수 있습니다.

1. `Startup.ConfigureServices`에서 필터 컬렉션에 작업 필터를 추가합니다.

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a>유효성 검사 실패 오류 응답

Web API 컨트롤러의 경우, 모델 유효성 검사에 실패하면 MVC는 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 응답 형식으로 응답합니다. MVC는 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>의 결과를 사용하여 유효성 검사 실패에 대한 오류 응답을 생성합니다. 다음 예제에서는 팩터리를 사용하여 `Startup.ConfigureServices`에서 기본 응답 형식을 <xref:Microsoft.AspNetCore.Mvc.SerializableError>로 변경합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a>클라이언트 오류 응답

*오류 결과* 는 400 이상의 HTTP 상태 코드를 가진 결과로 정의됩니다. Web API 컨트롤러의 경우, MVC는 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>의 결과로 오류 결과를 변환합니다.

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> ASP.NET Core 2.1은 RFC 7807을 거의 준수하는 문제 세부 정보 응답을 생성합니다. 100% 준수가 중요한 경우 프로젝트를 ASP.NET Core 2.2 이상으로 업그레이드하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

오류 응답은 다음 방법 중 하나로 구성할 수 있습니다.

1. [ProblemDetailsFactory 구현](#implement-problemdetailsfactory)
1. [ApiBehaviorOptions.ClientErrorMapping 사용](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>`ProblemDetailsFactory` 구현

MVC는 <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName>를 사용하여 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 및 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>의 모든 인스턴스를 생성합니다. 여기에는 클라이언트 오류 응답, 유효성 검사 실패 오류 응답, <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> 및 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> 도우미 메서드가 포함됩니다.

문제 세부 정보 응답을 사용자 지정하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory>의 사용자 지정 구현을 등록합니다.

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

오류 응답은 [ApiBehaviorOptions.ClientErrorMapping 사용](#use-apibehavioroptionsclienterrormapping) 섹션에 설명된 대로 구성할 수 있습니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a>ApiBehaviorOptions.ClientErrorMapping 사용

<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> 속성을 사용하여 `ProblemDetails` 응답의 내용을 구성합니다. 예를 들어 `Startup.ConfigureServices` 내의 다음 코드는 404 응답에 대해 `type` 속성을 업데이트합니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a>예외를 처리 하는 사용자 지정 미들웨어

예외 처리 미들웨어의 기본값은 대부분의 앱에서 잘 작동 합니다. 특수 한 예외 처리를 필요로 하는 앱의 경우 [예외 처리 미들웨어를 사용자 지정 하는](xref:fundamentals/error-handling#exception-handler-lambda)것이 좋습니다.
