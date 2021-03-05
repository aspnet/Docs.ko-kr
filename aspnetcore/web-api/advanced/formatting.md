---
title: ASP.NET Core Web API에서 응답 데이터 서식 지정
author: rick-anderson
description: ASP.NET Core Web API에서 응답 데이터의 서식을 지정하는 방법을 알아봅니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 1/28/2021
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
uid: web-api/advanced/formatting
ms.openlocfilehash: 5fa7bc15bac2609c89fea54f8a788aaf9f5ad055
ms.sourcegitcommit: 3982ff9dabb5b12aeb0a61cde2686b5253364f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119034"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>ASP.NET Core Web API에서 응답 데이터 서식 지정

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC는 응답 데이터 서식 지정을 지원합니다. 응답 데이터는 특정 형식 또는 클라이언트 요청 형식에 대한 응답으로 형식을 지정할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>형식별 작업 결과

일부 작업 결과 형식은 <xref:Microsoft.AspNetCore.Mvc.JsonResult> 및 <xref:Microsoft.AspNetCore.Mvc.ContentResult>와 같이 특정 형식과 관련됩니다. 작업은 클라이언트 기본 설정에 관계 없이 특정 형식으로 서식이 지정된 결과를 반환할 수 있습니다. 예를 들어 `JsonResult`를 반환하면 JSON 형식의 데이터가 반환됩니다. `ContentResult` 또는 문자열 하나를 반환하면 일반 텍스트 형식의 문자열 데이터가 반환됩니다.

특정 형식을 반환하는 작업은 필요하지 않습니다. ASP.NET Core는 모든 개체 반환 값을 지원합니다.  <xref:Microsoft.AspNetCore.Mvc.IActionResult> 형식이 아닌 개체를 반환하는 작업의 결과는 적절한 <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> 구현을 사용하여 직렬화됩니다. 자세한 내용은 <xref:web-api/action-return-types>를 참조하세요.

기본 제공 도우미 메서드 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>는 다음 JSON 형식 데이터를 반환합니다. [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

샘플 다운로드는 작성자 목록을 반환합니다. F12 브라우저 개발자 도구 또는 이전 코드의 [Postman](https://www.getpostman.com/tools) 사용:

* **content-type:이 포함된 응답 헤더** `application/json; charset=utf-8`이 표시됩니다.
* 요청 헤더가 표시됩니다. 예를 들어 `Accept` 헤더가 있습니다. `Accept` 헤더는 앞의 코드에서 무시됩니다.

서식 있는 일반 텍스트 데이터를 반환하려면 <xref:Microsoft.AspNetCore.Mvc.ContentResult> 및 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> 도우미를 사용합니다.

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

위의 코드에서 반환된 `Content-Type`은 `text/plain`입니다. 문자열을 반환하면 `text/plain`의 `Content-Type`이 제공됩니다.

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

반환 형식이 여러 개인 작업의 경우 `IActionResult`를 반환합니다. 예를 들어 수행된 작업의 결과에 따라 서로 다른 HTTP 상태 코드를 반환합니다.

## <a name="content-negotiation"></a>콘텐츠 협상

콘텐츠 협상은 클라이언트가 [헤더 수락](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)을 지정하는 경우 발생합니다. ASP.NET Core MVC에서 사용하는 기본 형식은 [JSON](https://json.org/)입니다. 콘텐츠 협상은 다음과 같은 특징을 가집니다.

* <xref:Microsoft.AspNetCore.Mvc.ObjectResult>에 의해 구현됩니다.
* 도우미 메서드에서 반환된 상태 코드별 작업 결과 상태 코드에 기본적으로 제공됩니다. 작업 결과 도우미 메서드는 `ObjectResult`를 기반으로 합니다.

모델 형식이 반환되는 경우 반환 형식은 `ObjectResult`입니다.

다음 작업 메서드는 `Ok` 및 `NotFound` 도우미 메서드를 사용합니다.

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

기본적으로 ASP.NET Core는 `application/json`, `text/json`, `text/plain` 미디어 형식을 지원합니다. [Fiddler](https://www.telerik.com/fiddler) 또는 [Postman](https://www.getpostman.com/tools)과 같은 도구는 반환 형식을 지정하도록 `Accept` 요청 헤더를 설정할 수 있습니다. `Accept` 헤더에 서버가 지원하는 형식이 포함되어 있으면 해당 형식이 반환됩니다. 다음 섹션에서는 추가적인 포맷터를 추가하는 방법을 보여줍니다.

컨트롤러 작업은 POCO(Plain Old CLR Objects)를 반환할 수 있습니다. POCO가 반환되면 런타임에서는 개체를 래핑하는`ObjectResult`를 자동으로 만듭니다. 클라이언트는 형식이 지정된 직렬화된 개체를 가져옵니다. 반환되는 개체가 `null`이면 `204 No Content` 응답이 반환됩니다.

개체 형식 반환하기:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

위 코드에서는 유효한 작성자 별칭에 대한 요청이 작성자의 데이터와 함께 `200 OK` 응답을 반환합니다. 잘못된 별칭에 대한 요청은 `204 No Content` 응답을 반환합니다.

### <a name="the-accept-header"></a>Accept 헤더

콘텐츠 *협상* 은 `Accept` 헤더가 요청에 나타나는 경우에 발생합니다. 요청에 Accept 헤더가 포함되어 있으면 ASP.NET Core는 다음을 수행합니다.

* 기본 설정 순서에 따라 Accept 헤더의 미디어 형식을 열거합니다.
* 지정된 형식 중 하나로 응답을 생성할 수 있는 포맷터를 찾으려고 시도합니다.

클라이언트의 요청을 충족할 수 있는 포맷터가 없는 경우 ASP.NET Core는 다음을 수행합니다.

* `406 Not Acceptable` <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> 가 또는로 설정 된 경우를 반환 합니다. `true`
* 응답을 생성할 수 있는 첫 번째 포맷터를 찾으려고 시도합니다.

요청된 형식에 대해 포맷터가 구성되지 않은 경우에는 개체의 서식을 지정할 수 있는 첫 번째 포맷터가 사용됩니다. 요청에 `Accept` 헤더가 표시되지 않을 경우 다음이 수행됩니다.

* 개체를 처리할 수 있는 첫 번째 포맷터는 응답을 직렬화하는 데 사용됩니다.
* 발생하는 협상이 없습니다. 서버에서 반환할 형식을 결정합니다.

Accept 헤더에 `*/*`가 포함되는 경우, `RespectBrowserAcceptHeader`가 <xref:Microsoft.AspNetCore.Mvc.MvcOptions>에서 true로 설정되지 않으면 헤더는 무시됩니다.

### <a name="browsers-and-content-negotiation"></a>브라우저 및 콘텐츠 협상

일반적인 API 클라이언트와 달리 웹 브라우저는 `Accept` 헤더를 제공합니다. 웹 브라우저는 와일드카드를 비롯한 다양한 형식을 지정합니다. 기본적으로 프레임워크가 브라우저에서 요청이 수신되는 것을 감지할 경우 다음이 수행됩니다.

* `Accept` 헤더는 무시됩니다.
* 달리 구성되지 않은 경우 콘텐츠가 JSON에 반환됩니다.

이것은 API를 소비할 때 브라우저에서 보다 일관된 환경을 제공합니다.

브라우저 Accept 헤더를 적용하도록 앱을 구성하려면 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader>를 `true`로 설정합니다.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>포맷터 구성

추가 형식을 지원해야 하는 앱은 적절한 NuGet 패키지를 추가하고 지원을 구성할 수 있습니다. 입력 및 출력에 대한 개별 포맷터가 있습니다. 입력 포맷터는 [모델 바인딩](xref:mvc/models/model-binding)에서 사용됩니다. 출력 포맷터는 응답의 형식을 지정하는 데 사용됩니다. 사용자 지정 포맷터 생성에 대한 정보는 [사용자 지정 포맷터](xref:web-api/advanced/custom-formatters)를 참조하세요.

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>XML 형식 지원 추가

<xref:System.Xml.Serialization.XmlSerializer>를 사용하여 구현된 XML 포맷터는 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>를 호출하여 구성됩니다.

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

위의 코드는 `XmlSerializer`를 사용하여 결과를 직렬화합니다.

위의 코드를 사용할 때 컨트롤러 메서드는 요청의 `Accept` 헤더에 따라 적절한 형식을 반환합니다.

### <a name="configure-systemtextjson-based-formatters"></a>`System.Text.Json`기반 포맷터 구성

기반 포맷터의 기능은를 `System.Text.Json` 사용 하 여 구성할 수 있습니다 <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName> . 기본 형식은 camelCase입니다. 다음 강조 표시 된 코드는 대/소문자 서식 지정을 설정 합니다.

[!code-csharp[](./formatting/5.0samples/WebAPI5PascalCase/Startup.cs?name=snippet&highlight=4-5)]

다음 동작 메서드는 [Controllerbase. 문제](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) 를 호출 하 여 응답을 만듭니다. <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>

[!code-csharp[](formatting/5.0samples/WebAPI5PascalCase/Controllers/WeatherForecastController.cs?name=snippet&highlight=4)]

이전 코드를 사용 하는 경우:

  * `https://localhost:5001/WeatherForecast/temperature` 는 대/소문자를 반환 합니다.
  * `https://localhost:5001/WeatherForecast/error` camelCase를 반환 합니다. 앱에서 형식을 camelCase로 설정 하는 경우에도 오류 응답은 항상 유효 하지 않습니다. `ProblemDetails` 는 소문자를 지정 하는 [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A)을 따릅니다.

다음 코드는 사용자 지정 변환기를 설정 하 고 사용자 지정 변환기를 추가 합니다.

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

`JsonResult`를 사용하여 동작 단위로 출력 serialization 옵션을 구성할 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Newtonsoft.Json 기반 JSON 형식 지원 추가

ASP.NET Core 3.0 이전에는 `Newtonsoft.Json` 패키지를 사용하여 구현된 JSON 포맷터를 기본적으로 사용했습니다. ASP.NET Core 3.0 이후의 기본 JSON 포맷터는 `System.Text.Json`을 기반으로 합니다. `Newtonsoft.Json`기반 포맷터 및 기능에 대 한 지원은 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 패키지를 설치 하 고에서 구성 하 여 사용할 수 있습니다 `Startup.ConfigureServices` .

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

위의 코드에서를 호출 하면 `AddNewtonsoftJson` 다음 WEB API, MVC 및 Razor Pages 기능을 사용 하도록 구성 됩니다 `Newtonsoft.Json` .

* JSON을 읽고 쓰는 입력 및 출력 포맷터
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [JSON 패치](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [TempData](xref:fundamentals/app-state#tempdata)

일부 기능은 `System.Text.Json` 기반 포맷터와 잘 호환되지 않고 `Newtonsoft.Json` 기반 포맷터에 대한 참조를 필요로 할 수 있습니다. 앱이 다음과 같은 경우 `Newtonsoft.Json` 기반 포맷터를 계속해서 사용합니다.

* `Newtonsoft.Json` 속성을 사용합니다. 예를 들어 `[JsonProperty]` 또는 `[JsonIgnore]`입니다.
* 직렬화 설정을 사용자 지정합니다.
* `Newtonsoft.Json`은 제공하는 기능에 의존합니다.
* `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`를 구성하는 경우. ASP.NET Core 3.0 이전의 `JsonResult.SerializerSettings`는 `Newtonsoft.Json` 고유의 `JsonSerializerSettings`의 인스턴스를 허용합니다.
* [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) 문서를 생성하는 경우.

`Newtonsoft.Json` 기반 포맷터의 기능은 `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`를 사용하여 구성할 수 있습니다.

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

`JsonResult`를 사용하여 동작 단위로 출력 serialization 옵션을 구성할 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>XML 형식 지원 추가

XML 형식 지정은 [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet 패키지를 필요로 합니다.

<xref:System.Xml.Serialization.XmlSerializer>를 사용하여 구현된 XML 포맷터는 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>를 호출하여 구성됩니다.

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

위의 코드는 `XmlSerializer`를 사용하여 결과를 직렬화합니다.

위의 코드를 사용할 때 컨트롤러 메서드는 요청의 `Accept` 헤더에 따라 적절한 형식을 반환해야 합니다.

::: moniker-end

### <a name="specify-a-format"></a>형식 지정

응답 형식을 제한 하려면 필터를 적용 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) 합니다. 대부분의 [필터](xref:mvc/controllers/filters)와 마찬가지로, `[Produces]` 작업, 컨트롤러 또는 전역 범위에서 적용할 수 있습니다.

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

이전 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) 필터:

* 컨트롤러 내의 모든 작업이 JSON 형식 응답을 반환하도록 강제합니다.
* 다른 포맷터가 구성되고 클라이언트에서 다른 형식을 지정하는 경우 JSON이 반환됩니다.

자세한 내용은 [필터](xref:mvc/controllers/filters)를 참조하세요.

### <a name="special-case-formatters"></a>특수한 사례 포맷터

일부 특수한 경우 기본 제공 포맷터를 사용하여 구현됩니다. 기본적으로 `string` 반환 형식은 *text/plain*(`Accept` 헤더를 통해 요청된 경우 *text/html*)으로 형식이 지정됩니다. 이 동작은 <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>를 제거하여 삭제할 수 있습니다. 포맷터들은 `ConfigureServices` 방법에서 제거됩니다. 모델 개체 반환 형식이 있는 작업은 `null`을 반환하는 경우 `204 No Content`를 반환합니다. 이 동작은 <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>를 제거하여 삭제할 수 있습니다. 다음 코드를 `StringOutputFormatter` 및 `HttpNoContentOutputFormatter`를 제거합니다.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

`StringOutputFormatter`가 없으면 기본 제공 JSON 포맷터가 `string` 반환 형식의 형식을 지정합니다. 기본 제공 JSON 포맷터가 제거되고 XML 포맷터를 사용할 수 있는 경우, XML 포맷터가 `string` 반환 형식의 형식을 지정합니다. 그렇지 않으면 `string` 반환 형식이 `406 Not Acceptable`을 반환합니다.

`HttpNoContentOutputFormatter`가 없으면 null 개체는 구성된 포맷터를 사용하여 서식이 지정됩니다. 예를 들면 다음과 같습니다.

* JSON 포맷터는 `null`의 본문이 포함된 응답을 반환합니다.
* XML 포맷터는 `xsi:nil="true"`로 설정된 특성을 사용하여 빈 XML 요소를 반환합니다.

## <a name="response-format-url-mappings"></a>응답 형식 URL 매핑

클라이언트는 URL의 일부로 특정 형식을 요청할 수 있습니다. 예를 들면 다음과 같습니다.

* 쿼리 문자열 또는 경로의 부분에서.
* .Xml 또는 .json과 같은 서식 지정 파일 확장명을 사용하여.

요청 경로의 매핑은 API가 사용하는 경로에 지정해야 합니다. 예를 들면 다음과 같습니다.

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

위 경로를 사용하면 요청된 형식을 선택적 파일 확장명으로 지정할 수 있습니다. 특성은에 [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) 형식 값이 있는지 확인 `RouteData` 하 고 응답이 생성 될 때 응답 형식을 적절 한 포맷터에 매핑합니다.

|           경로        |             포맷터              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    기본 출력 포맷터    |
| `/api/products/5.json` | JSON 포맷터(구성된 경우) |
| `/api/products/5.xml`  | XML 포맷터(구성된 경우)  |
