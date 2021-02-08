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
ms.openlocfilehash: 03debcb211a8cb8f0ebd6d5e67fbc8de7b574e27
ms.sourcegitcommit: c1839f2992b003c92cd958244a2e0771ae928786
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551663"
---
# <a name="create-web-apis-with-aspnet-core"></a>ASP.NET Core로 Web API 만들기

작성자: [Scott Addie](https://github.com/scottaddie), [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core는 C#를 사용하여 Web API라고도 하는 RESTful 서비스 만들기를 지원합니다. 요청을 처리하기 위해 Web API는 컨트롤러를 사용합니다. Web API의 *컨트롤러* 는 `ControllerBase`에서 파생되는 클래스입니다. 이 문서에서는 웹 API 요청을 처리하기 위해 컨트롤러를 사용하는 방법을 보여 줍니다.

[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples) ([다운로드하는 방법](xref:index#how-to-download-a-sample))

## <a name="controllerbase-class"></a>ControllerBase 클래스

Web API는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>에서 파생되는 하나 이상의 컨트롤러 클래스로 구성됩니다. 웹 API 프로젝트 템플릿은 시작 컨트롤러를 제공합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<xref:Microsoft.AspNetCore.Mvc.Controller> 클래스에서 파생된 Web API 컨트롤러를 만들지 마세요. `Controller`는 `ControllerBase`에서 파생되며 뷰에 대한 지원을 추가하므로 Web API 요청이 아닌 웹 페이지를 처리할 수 있습니다. 이 규칙에 대한 예외가 있습니다. 뷰와 웹 API 모두에 동일한 컨트롤러를 사용하려는 경우 `Controller`에서 파생합니다.

`ControllerBase` 클래스는 HTTP 요청을 처리하는 데 유용한 많은 속성 및 메서드를 제공합니다. 예를 들어, `ControllerBase.CreatedAtAction`은 201 상태 코드를 반환합니다.

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

다음은 `ControllerBase`가 제공하는 메서드의 몇 가지 예입니다.

|메서드   |참고    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| 400 상태 코드를 반환합니다.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|404 상태 코드를 반환합니다.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|파일을 반환합니다.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|[모델 바인딩](xref:mvc/models/model-binding)을 호출합니다.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|[모델 유효성 검사](xref:mvc/models/validation)를 호출합니다.|

사용 가능한 모든 메서드 및 속성 목록을 보려면 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>를 참조하세요.

## <a name="attributes"></a>특성

<xref:Microsoft.AspNetCore.Mvc> 네임스페이스는 Web API 컨트롤러 및 작업 메서드의 동작을 구성하는 데 사용할 수 있는 특성을 제공합니다. 다음 예제에서는 특성을 사용하여 지원되는 HTTP 작업 동사와 반환될 수 있는 알려진 HTTP 상태 코드를 지정합니다.

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

다음은 사용 가능한 특성의 몇 가지 예입니다.

|특성|참고|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |컨트롤러 또는 작업의 URL 패턴을 지정합니다.|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |모델 바인딩에 포함할 접두사 및 속성을 지정합니다.|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |HTTP GET 작업 동사를 지원하는 작업을 식별합니다.|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|작업에서 허용하는 데이터 형식을 지정합니다.|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|작업에서 반환하는 데이터 형식을 지정합니다.|

사용 가능한 특성이 포함된 목록은 <xref:Microsoft.AspNetCore.Mvc> 네임스페이스를 참조하세요.

## <a name="apicontroller-attribute"></a>ApiController 특성

[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성을 컨트롤러 클래스에 적용하여 다음과 같이 독자적인 API 관련 동작을 사용할 수 있습니다.

::: moniker range=">= aspnetcore-2.2"

* [특성 라우팅 요구 사항](#attribute-routing-requirement)
* [자동 HTTP 400 응답](#automatic-http-400-responses)
* [바인딩 소스 매개 변수 유추](#binding-source-parameter-inference)
* [다중 파트/폼 데이터 요청 유추](#multipartform-data-request-inference)
* [오류 상태 코드에 대한 문제 세부 정보](#problem-details-for-error-status-codes)

*오류 상태 코드에 대한 문제 세부 정보* 기능을 사용하려면 2.2 이상의 [호환성 버전](xref:mvc/compatibility-version)이 필요합니다. 기타 기능을 사용하려면 2.1 이상의 호환성 버전이 필요합니다.

::: moniker-end

* [특성 라우팅 요구 사항](#attribute-routing-requirement)
* [자동 HTTP 400 응답](#automatic-http-400-responses)
* [바인딩 소스 매개 변수 유추](#binding-source-parameter-inference)
* [다중 파트/폼 데이터 요청 유추](#multipartform-data-request-inference)

이러한 기능을 사용하려면 [호환성 버전](xref:mvc/compatibility-version) 2.1 이상이 필요합니다.

### <a name="attribute-on-specific-controllers"></a>특정 컨트롤러의 특성

프로젝트 템플릿의 다음 예제에서처럼 `[ApiController]` 특성을 특정 컨트롤러에 적용할 수 있습니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>여러 컨트롤러의 특성

둘 이상의 컨트롤러에서 특성을 사용하는 방법으로 `[ApiController]` 특성으로 주석이 추가된 사용자 지정 기본 컨트롤러 클래스를 만드는 방법이 있습니다. 다음 예제는 사용자 지정 기본 클래스와 여기에서 파생되는 컨트롤러를 보여 줍니다.

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>어셈블리의 특성

[호환성 버전](xref:mvc/compatibility-version)이 2.2 이상으로 설정된 경우 `[ApiController]` 특성을 어셈블리에 적용할 수 있습니다. 이 방식의 주석은 웹 API 동작을 어셈블리의 모든 컨트롤러에 적용합니다. 개별 컨트롤러를 대상으로 옵트아웃하는 방법은 없습니다. 어셈블리 수준 특성을 `Startup` 클래스 주위의 네임스페이스 선언에 적용합니다.

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

## <a name="attribute-routing-requirement"></a>특성 라우팅 요구 사항

`[ApiController]` 특성은 특성 라우팅을 요구합니다. 예를 들어:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

작업은 `Startup.Configure`의 `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> 또는 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A>에 의해 정의된 [규칙 기반 경로](xref:mvc/controllers/routing#conventional-routing)를 통해 액세스할 수 없습니다.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

작업은 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>에 의해 정의된 [규칙 기반 경로](xref:mvc/controllers/routing#conventional-routing)를 통해 또는 `Startup.Configure`의 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A>에서 액세스할 수 없습니다.

::: moniker-end

## <a name="automatic-http-400-responses"></a>자동 HTTP 400 응답

`[ApiController]` 특성은 HTTP 400 응답을 자동으로 트리거하는 모델 유효성 검사 오류를 만듭니다. 따라서 다음 코드는 작업 메서드에서 불필요합니다.

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

ASP.NET Core MVC는 <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> 작업 필터를 사용하여 위의 검사를 수행합니다.

### <a name="default-badrequest-response"></a>기본 BadRequest 응답

호환성 버전이 2.1이면 HTTP 400 응답에 대한 기본 응답 형식은 <xref:Microsoft.AspNetCore.Mvc.SerializableError>입니다. 다음 요청 본문은 직렬화된 형식의 예제입니다.

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

호환성 버전이 2.2 이상이면 HTTP 400 응답에 대한 기본 응답 형식은 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>입니다. 다음 요청 본문은 직렬화된 형식의 예제입니다.

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

`ValidationProblemDetails` 형식:

* 웹 API 응답에서 오류를 지정하기 위해 머신이 읽을 수 있는 형식을 제공합니다.
* [RFC 7807 사양](https://tools.ietf.org/html/rfc7807)을 준수합니다.

::: moniker-end

자동 및 사용자 지정 응답을 일관성 있게 설정하려면 <xref:System.Web.Http.ApiController.BadRequest%2A> 대신 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A> 메서드를 호출합니다. `ValidationProblem`은 자동 응답뿐만 아니라 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 개체를 반환합니다.

### <a name="log-automatic-400-responses"></a>자동 400 응답 기록

[How to log automatic 400 responses on model validation errors (dotnet/AspNetCore.Docs#12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157)(모델 유효성 검사 오류에서 자동 400 응답을 기록하는 방법(dotnet/AspNetCore.Docs#12157))를 참조하세요.

### <a name="disable-automatic-400-response"></a>자동 400 응답 사용 안 함

자동 400 동작을 사용하지 않으려면 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> 속성을 `true`로 설정합니다. `Startup.ConfigureServices`에 다음 강조 표시된 코드를 추가합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a>바인딩 소스 매개 변수 유추

바인딩 소스 특성은 작업 매개 변수 값이 발견되는 위치를 정의합니다. 다음 바인딩 소스 특성이 존재합니다.

|특성|바인딩 원본 |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | 요청 본문 |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | 요청 본문에서 양식 데이터 |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | 요청 헤더 |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | 요청 쿼리 문자열 매개 변수 |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | 현재 요청의 경로 데이터 |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | 작업 매개 변수로 삽입된 요청 서비스 |

> [!WARNING]
> 값에 `%2f`(즉, `/`)이 포함될 수 있는 경우 `[FromRoute]`를 사용하지 않습니다. `%2f`는 `/`로 이스케이프가 해제되지 않습니다. 값에 `%2f`가 포함될 수 있으면 `[FromQuery]`를 사용합니다.

`[ApiController]` 특성 또는 `[FromQuery]` 같은 다른 바인딩 소스 특성 없이, ASP.NET Core 런타임에서 복합 개체 모델 바인더 사용을 시도합니다. 복합 개체 모델 바인더는 정의된 순서로 값 공급자로부터 데이터를 풀합니다.

다음 예제에서 `[FromQuery]` 특성은 `discontinuedOnly` 매개 변수 값이 요청 URL의 쿼리 문자열에 제공됨을 나타냅니다.

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

`[ApiController]` 특성은 작업 매개 변수의 기본 데이터 원본에 유추 규칙을 적용합니다. 이러한 규칙을 통해 작업 매개 변수에 특성을 적용하여 바인딩 소스를 수동으로 식별할 필요가 없습니다. 바인딩 소스 유추 규칙은 다음과 같이 동작합니다.

* `[FromBody]`는 복합 형식 매개 변수에 대해 유추됩니다. `[FromBody]` 유추 규칙은 <xref:Microsoft.AspNetCore.Http.IFormCollection> 및 <xref:System.Threading.CancellationToken> 같이 특별한 의미를 지닌 복합 기본 제공 형식에는 적용되지 않습니다. 바인딩 소스 유추 코드는 이러한 특별한 형식을 무시합니다.
* `[FromForm]`은 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 <xref:Microsoft.AspNetCore.Http.IFormFileCollection> 형식의 작업 매개 변수에 대해 유추됩니다. 단순 또는 사용자 정의 형식에 대해서는 유추되지 않습니다.
* `[FromRoute]`는 경로 템플릿에서 매개 변수와 일치하는 작업 매개 변수 이름에 대해 유추됩니다. 둘 이상의 경로가 작업 매개 변수와 일치하는 경우 모든 경로 값은 `[FromRoute]`로 간주됩니다.
* `[FromQuery]`는 다른 작업 매개 변수에 대해 유추됩니다.

### <a name="frombody-inference-notes"></a>FromBody 유추 메모

`[FromBody]`는 `string` 또는 `int` 같은 단순 형식에 대해 유추되지 않습니다. 따라서 해당 기능이 필요한 경우 단순 형식에 `[FromBody]` 특성을 사용해야 합니다.

작업에 요청 본문에서 바인딩된 매개 변수가 두 개 이상 있는 경우 예외가 throw됩니다. 예를 들어 다음 모든 작업 메서드 시그니처로 예외가 발생합니다.

* 복합 형식이므로 둘 다에서 유추된 `[FromBody]`

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* 하나의 `[FromBody]` 특성, 복합 형식이므로 다른 하나에서 유추됨

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* 둘 다의 `[FromBody]` 특성

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> ASP.NET Core 2.1에서 목록, 배열 등의 컬렉션 형식 매개 변수를 `[FromQuery]`로 잘못 유추합니다. 이러한 매개 변수가 요청 본문에서 바인딩되는 경우 해당 매개 변수에 `[FromBody]` 특성을 사용해야 합니다. 이 문제는 ASP.NET Core 2.2 이상에서 수정됩니다. 즉, 기본적으로 컬렉션 형식 매개 변수를 본문에서 바인딩되도록 유추합니다.

::: moniker-end

### <a name="disable-inference-rules"></a>유추 규칙 사용 안 함

바인딩 소스 유추를 사용하지 않으려면 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters>를 `true`로 설정합니다. 다음 코드를 `Startup.ConfigureServices`에 추가합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a>다중 파트/폼 데이터 요청 유추

작업 매개 변수를 [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) 특성으로 주석 처리하는 경우 `[ApiController]` 특성이 유추 규칙에 적용됩니다. `multipart/form-data` 요청 콘텐츠 형식이 유추됩니다.

기본 동작을 사용하지 않으려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> 속성을 `true`로 설정합니다.

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

## <a name="problem-details-for-error-status-codes"></a>오류 상태 코드에 대한 문제 세부 정보

호환성 버전이 2.2 이상인 경우 MVC는 오류 결과(상태 코드 400 이상의 결과)를 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>가 있는 결과로 변환합니다. `ProblemDetails` 형식은 HTTP 응답에 머신에서 읽을 수 있는 오류 세부 정보를 제공하기 위해 [RFC 7807 사양](https://tools.ietf.org/html/rfc7807)을 기반으로 합니다.

다음 컨트롤러 작업에서 다음 코드를 고려하세요.

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

`NotFound` 메서드는 `ProblemDetails` 본문이 포함된 HTTP 404 상태 코드를 생성합니다. 예를 들어:

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>ProblemDetails 응답 사용 안 함

<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> 속성이 `true`로 설정된 경우 오류 상태 코드에 대한 `ProblemDetails` 자동 생성이 사용되지 않습니다. 다음 코드를 `Startup.ConfigureServices`에 추가합니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a>[사용] 특성을 사용하여 지원되는 요청 콘텐츠 형식 정의

기본적으로 작업은 사용 가능한 모든 요청 콘텐츠 형식을 지원합니다. 예를 들어 앱이 JSON 및 XML [입력 포맷터](xref:mvc/models/model-binding#input-formatters)를 모두 지원하도록 구성된 경우 작업은 `application/json`과 `application/xml`을 비롯한 여러 콘텐츠 형식을 지원합니다.

[[사용]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 특성을 사용하면 작업에서 지원되는 요청 콘텐츠 형식을 제한할 수 있습니다. 하나 이상의 콘텐츠 형식을 지정하여 작업 또는 컨트롤러에 `[Consumes]` 특성을 적용합니다.

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

위의 코드에서 `CreateProduct` 동작은 콘텐츠 형식을 `application/xml`로 지정합니다. 이 작업으로 라우팅되는 요청은 `application/xml`의 `Content-Type` 헤더를 지정해야 합니다. `application/xml`의 `Content-Type` 헤더를 지정하지 않는 요청은 [415 지원되지 않는 미디어 유형](https://developer.mozilla.org/docs/Web/HTTP/Status/415) 응답을 반환합니다.

또한 `[Consumes]` 특성을 사용하면 형식 제약 조건을 적용함으로써 들어오는 요청의 콘텐츠 형식에 따라 작업의 선택에 영향을 줄 수 있습니다. 다음 예제를 참조하세요.

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

위의 코드에서 `ConsumesController`는 `https://localhost:5001/api/Consumes` URL에 전송된 요청을 처리하도록 구성되어 있습니다. 컨트롤러의 작업 `PostJson` 및 `PostForm` 모두 동일한 URL을 사용하여 POST 요청을 처리합니다. 형식 제약 조건을 적용하는 `[Consumes]` 특성이 없으면 모호한 일치 예외가 throw됩니다.

`[Consumes]` 특성이 두 작업 모두에 적용됩니다. `PostJson` 작업은 `application/json`의 `Content-Type` 헤더와 함께 전송된 요청을 처리합니다. `PostForm` 작업은 `application/x-www-form-urlencoded`의 `Content-Type` 헤더와 함께 전송된 요청을 처리합니다. 

## <a name="additional-resources"></a>추가 자료

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
* [Microsoft Learn: ASP.NET Core를 사용하여 웹 API 만들기](/learn/modules/build-web-api-aspnet-core/)
