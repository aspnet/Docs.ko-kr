---
title: ASP.NET Web API에서 ASP.NET Core로 마이그레이션
author: ardalis
description: ASP.NET 4.x Web API에서 ASP.NET Core MVC로 웹 API 구현을 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: 320805c0d40bf06cee384e6d98caea5c420d45bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061472"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>ASP.NET Web API에서 ASP.NET Core로 마이그레이션

[Scott Addie](https://twitter.com/scott_addie) 및 [Steve Smith](https://ardalis.com/)

ASP.NET 4.x Web API는 브라우저 및 모바일 장치를 비롯 한 광범위 한 클라이언트에 도달 하는 HTTP 서비스입니다. ASP.NET Core ASP.NET 4.x의 MVC 및 Web API 앱 모델을 ASP.NET Core MVC 라는 단일 프로그래밍 모델로 결합 합니다. 이 문서에서는 ASP.NET 4.x Web API에서 ASP.NET Core MVC로 마이그레이션하는 데 필요한 단계를 보여 줍니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a>ASP.NET 4.x 웹 API 프로젝트를 검토 합니다.

이 문서에서는 [ASP.NET Web API 2 시작](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)에서 만든 *ProductsApp* 프로젝트를 사용 합니다. 해당 프로젝트에서 기본 ASP.NET 4.x 웹 API 프로젝트는 다음과 같이 구성 됩니다.

*Global.asax.cs* 에서 다음을 호출 합니다 `WebApiConfig.Register` .

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig`클래스는 *App_Start* 폴더에 있으며 정적 메서드를 포함 합니다 `Register` .

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

이전 클래스:

* 실제로 사용 되 고 있지는 않지만 [특성 라우팅을](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)구성 합니다.
* 라우팅 테이블을 구성 합니다.
샘플 코드에서는 Url을 선택적으로 사용 하 여 Url과 일치 하는 Url을 예상 합니다 `/api/{controller}/{id}` `{id}` .

다음 섹션에서는 ASP.NET Core MVC로 웹 API 프로젝트를 마이그레이션하는 방법을 보여 줍니다.

## <a name="create-the-destination-project"></a>대상 프로젝트 만들기

Visual Studio에서 비어 있는 새 솔루션을 만들고 마이그레이션할 ASP.NET 4.x 웹 API 프로젝트를 추가 합니다.

1. **파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.
1. **빈 솔루션** 템플릿을 선택 하 고 **다음** 을 선택 합니다.
1. 솔루션 이름을 *WebAPIMigration* 로 합니다. **만들기** 를 선택합니다.
1. 기존 *ProductsApp* 프로젝트를 솔루션에 추가 합니다.

마이그레이션할 새 API 프로젝트를 추가 합니다.

1. 새 **ASP.NET Core 웹 응용 프로그램** 프로젝트를 솔루션에 추가 합니다.
1. **새 프로젝트 구성** 대화 상자에서 프로젝트의 이름을 *제품 점수* 로 하 고 **만들기** 를 선택 합니다.
1. **새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.1** 이 선택되었는지 확인합니다. **API** 프로젝트 템플릿을 선택하고 **만들기** 를 선택합니다.
1. 새 *제품 점수* 프로젝트에서 *WeatherForecast.cs* 및 *Controllers/WeatherForecastController* 예제 파일을 제거 합니다.

이제 솔루션에는 두 개의 프로젝트가 포함 되어 있습니다. 다음 섹션에서는 *ProductsApp* 프로젝트의 콘텐츠를 *제품 점수* 프로젝트로 마이그레이션하는 방법에 대해 설명 합니다.

## <a name="migrate-configuration"></a>구성 마이그레이션

ASP.NET Core *App_Start* 폴더 또는 *global.asax* 파일을 사용 하지 않습니다. 또한 *web.config* 파일은 게시할 때 추가 됩니다.

`Startup` 클래스는:

* *Global.asax* 를 바꿉니다.
* 모든 앱 시작 작업을 처리 합니다.

자세한 내용은 <xref:fundamentals/startup>를 참조하세요.

## <a name="migrate-models-and-controllers"></a>모델 및 컨트롤러 마이그레이션

다음 코드에서는 `ProductsController` ASP.NET Core에 대해 업데이트 되는을 보여 줍니다.

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

`ProductsController`ASP.NET Core에 대 한를 업데이트 합니다.

1. *Controllers/ProductsController* 및 *모델* 폴더를 원본 프로젝트에서 새 프로젝트로 복사 합니다.
1. 복사한 파일의 루트 네임 스페이스를로 변경 `ProductsCore` 합니다.
1. `using ProductsApp.Models;`문을로 업데이트 `using ProductsCore.Models;` 합니다.

ASP.NET Core에는 다음 구성 요소가 없습니다.

* `ApiController` 클래스
* `System.Web.Http` 네임스페이스
* `IHttpActionResult` 인터페이스

다음과 같이 변경합니다.

1. `ApiController`을 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>으로 변경합니다. `using Microsoft.AspNetCore.Mvc;`를 추가 하 여 `ControllerBase` 참조를 확인 합니다.
1. `using System.Web.Http;`를 삭제합니다.
1. `GetProduct`작업의 반환 형식을에서로 변경 `IHttpActionResult` 합니다 `ActionResult<Product>` .
1. `GetProduct`작업의 `return` 문을 다음과 같이 단순화 합니다.

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>라우팅 구성

ASP.NET Core *API* 프로젝트 템플릿에는 생성 된 코드의 끝점 라우팅 구성이 포함 됩니다.

다음은 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> 을 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 호출 합니다.

* [미들웨어](xref:fundamentals/middleware/index) 파이프라인에서 경로 일치 및 끝점 실행을 등록 합니다.
* *ProductsApp* 프로젝트의 *App_Start/Webapicon.cs* 파일을 바꿉니다.

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

다음과 같이 라우팅을 구성 합니다.

1. 클래스에 `ProductsController` 다음 특성을 표시 합니다.

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    위의 [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 특성은 컨트롤러의 특성 라우팅 패턴을 구성 합니다. [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)특성은이 컨트롤러의 모든 동작에 대 한 요구 사항을 라우팅하는 특성을 만듭니다.

    특성 라우팅은 및와 같은 토큰을 `[controller]` 지원 `[action]` 합니다. 런타임에 각 토큰은 특성이 적용 되는 각각의 컨트롤러나 동작의 이름으로 바뀝니다. 토큰:
    * 프로젝트의 매직 문자열 수를 줄입니다.
    * 자동 이름 바꾸기 리팩터링 적용 될 때 경로가 해당 컨트롤러 및 작업과 동기화 된 상태로 유지 되는지 확인 합니다.
1. 작업에 대 한 HTTP Get 요청을 사용 하도록 설정 합니다 `ProductController` .
    * [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)작업에 특성을 적용 `GetAllProducts` 합니다.
    * `[HttpGet("{id}")]`작업에 특성을 적용 `GetProduct` 합니다.

마이그레이션된 프로젝트를 실행 하 고로 이동 `/api/products` 합니다. 세 제품의 전체 목록이 표시 됩니다. [https://www.microsoft.com]\(`/api/products/1`) 로 이동합니다. 첫 번째 제품이 표시 됩니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>ASP.NET 4.x 웹 API 프로젝트를 검토 합니다.

이 문서에서는 [ASP.NET Web API 2 시작](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)에서 만든 *ProductsApp* 프로젝트를 사용 합니다. 해당 프로젝트에서 기본 ASP.NET 4.x 웹 API 프로젝트는 다음과 같이 구성 됩니다.

*Global.asax.cs* 에서 다음을 호출 합니다 `WebApiConfig.Register` .

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig`클래스는 *App_Start* 폴더에 있으며 정적 메서드를 포함 합니다 `Register` .

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

이 클래스는 실제로 프로젝트에서 사용 되지 않지만 [특성 라우팅을](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)구성 합니다. 또한 ASP.NET Web API에서 사용 하는 라우팅 테이블을 구성 합니다. 이 경우 ASP.NET 4.x Web API는 Url과 일치 하는 Url을 `/api/{controller}/{id}` 선택 사항으로 예상 `{id}` 합니다.

다음 섹션에서는 ASP.NET Core MVC로 웹 API 프로젝트를 마이그레이션하는 방법을 보여 줍니다.

## <a name="create-the-destination-project"></a>대상 프로젝트 만들기

Visual Studio에서 다음 단계를 완료 합니다.

* **파일**  >  **새로 만들기**  >  **프로젝트**  >  **기타 프로젝트 형식**  >  **Visual Studio 솔루션** 으로 이동 합니다. **비어 있는 솔루션** 을 선택 하 고 솔루션의 이름을 *WebAPIMigration* 로 선택 합니다. **확인** 단추를 클릭합니다.
* 기존 *ProductsApp* 프로젝트를 솔루션에 추가 합니다.
* 새 **ASP.NET Core 웹 응용 프로그램** 프로젝트를 솔루션에 추가 합니다. 드롭다운에서 **.Net Core** 대상 프레임 워크를 선택 하 고 **API** 프로젝트 템플릿을 선택 합니다. 프로젝트의 이름을 *제품 점수* 로 매기고 **확인** 단추를 클릭 합니다.

이제 솔루션에는 두 개의 프로젝트가 포함 되어 있습니다. 다음 섹션에서는 *ProductsApp* 프로젝트의 콘텐츠를 *제품 점수* 프로젝트로 마이그레이션하는 방법에 대해 설명 합니다.

## <a name="migrate-configuration"></a>구성 마이그레이션

사용 하지 않는 ASP.NET Core:

* *App_Start* 폴더 또는 *global.asax* 파일
* *web.config* 파일은 게시할 때 추가 됩니다.

`Startup` 클래스는:

* *Global.asax* 를 바꿉니다.
* 모든 앱 시작 작업을 처리 합니다.

자세한 내용은 <xref:fundamentals/startup>를 참조하세요.

ASP.NET Core MVC에서는가에서 호출 될 때 특성 라우팅이 기본적으로 포함 됩니다 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> `Startup.Configure` . 다음 `UseMvc` 호출은 *ProductsApp* 프로젝트의 *App_Start/Webapicon.cs* 파일을 대체 합니다.

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a>모델 및 컨트롤러 마이그레이션

다음 코드에서는 `ProductsController` ASP.NET Core에 대 한 업데이트를 보여 줍니다. [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]

`ProductsController`ASP.NET Core에 대 한를 업데이트 합니다.

1. 원본 프로젝트의 *Controllers/ProductsController* 를 새 프로젝트에 복사 합니다.
1. 원본 프로젝트의 *모델* 폴더를 새 프로젝트에 복사 합니다.
1. 복사한 파일의 루트 네임 스페이스를로 변경 `ProductsCore` 합니다.
1. `using ProductsApp.Models;`문을로 업데이트 `using ProductsCore.Models;` 합니다.

ASP.NET Core에는 다음 구성 요소가 없습니다.

* `ApiController` 클래스
* `System.Web.Http` 네임스페이스
* `IHttpActionResult` 인터페이스

다음과 같이 변경합니다.

1. `ApiController`을 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>으로 변경합니다. `using Microsoft.AspNetCore.Mvc;`를 추가 하 여 `ControllerBase` 참조를 확인 합니다.
1. `using System.Web.Http;`를 삭제합니다.
1. `GetProduct`작업의 반환 형식을에서로 변경 `IHttpActionResult` 합니다 `ActionResult<Product>` .
1. `GetProduct`작업의 `return` 문을 다음과 같이 단순화 합니다.

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>라우팅 구성

다음과 같이 라우팅을 구성 합니다.

1. 클래스에 `ProductsController` 다음 특성을 표시 합니다.

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    위의 [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 특성은 컨트롤러의 특성 라우팅 패턴을 구성 합니다. [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)특성은이 컨트롤러의 모든 동작에 대 한 요구 사항을 라우팅하는 특성을 만듭니다.

    특성 라우팅은 및와 같은 토큰을 `[controller]` 지원 `[action]` 합니다. 런타임에 각 토큰은 특성이 적용 되는 각각의 컨트롤러나 동작의 이름으로 바뀝니다. 토큰은 프로젝트의 매직 문자열 수를 줄입니다. 또한 토큰은 자동 이름 바꾸기 리팩터링 적용 될 때 해당 컨트롤러 및 작업과 동기화 된 상태로 유지 되도록 합니다.
1. 프로젝트의 호환성 모드를 ASP.NET Core 2.2으로 설정 합니다.

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    이전 변경 내용:

    * 는 컨트롤러 수준에서 특성을 사용 하는 데 필요 `[ApiController]` 합니다.
    * ASP.NET Core 2.2에 도입 된 잠재적으로 Opts 수 있습니다.
1. 작업에 대 한 HTTP Get 요청을 사용 하도록 설정 합니다 `ProductController` .
    * [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)작업에 특성을 적용 `GetAllProducts` 합니다.
    * `[HttpGet("{id}")]`작업에 특성을 적용 `GetProduct` 합니다.

마이그레이션된 프로젝트를 실행 하 고로 이동 `/api/products` 합니다. 세 제품의 전체 목록이 표시 됩니다. [https://www.microsoft.com]\(`/api/products/1`) 로 이동합니다. 첫 번째 제품이 표시 됩니다.

## <a name="compatibility-shim"></a>호환성 shim

[AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) 라이브러리는 ASP.NET 4.X 웹 API 프로젝트를 ASP.NET Core로 이동 하는 호환성 shim을 제공 합니다. 호환성 shim은 ASP.NET 4.x Web API 2의 다양 한 규칙을 지원 하도록 ASP.NET Core를 확장 합니다. 이 문서에서 이전에 이식 한 샘플은 호환성 shim이 필요 하지 않은 경우에만 기본입니다. 대규모 프로젝트의 경우 호환성 shim을 사용 하면 ASP.NET Core와 ASP.NET 4.x Web API 2 간의 API 격차를 일시적으로 브리징 하는 데 유용할 수 있습니다.

Web API 호환성 shim은 ASP.NET Core로의 large ASP.NET 4.x 웹 API 프로젝트 마이그레이션을 지원 하기 위한 임시 조치로 사용 됩니다. 시간이 지남에 따라 프로젝트는 호환성 shim을 사용 하는 대신 ASP.NET Core 패턴을 사용 하도록 업데이트 되어야 합니다.

에 포함 된 호환성 기능은 `Microsoft.AspNetCore.Mvc.WebApiCompatShim` 다음과 같습니다.

* `ApiController`컨트롤러의 기본 형식을 업데이트할 필요가 없도록 형식을 추가 합니다.
* 웹 API 스타일 모델 바인딩을 사용 하도록 설정 합니다. ASP.NET Core MVC 모델 바인딩 함수는 기본적으로 ASP.NET 4.x MVC 5와 유사 합니다. 호환성 shim은 모델 바인딩을 ASP.NET 4.x Web API 2 모델 바인딩 규칙과 더 유사 하 게 변경 합니다. 예를 들어 복합 형식은 요청 본문에서 자동으로 바인딩됩니다.
* 컨트롤러 작업에서 형식의 매개 변수를 사용할 수 있도록 모델 바인딩을 확장 `HttpRequestMessage` 합니다.
* 형식의 결과를 반환 하는 작업을 허용 하는 메시지 포맷터를 추가 `HttpResponseMessage` 합니다.
* 웹 API 2 작업에서 응답을 제공 하는 데 사용할 수 있는 추가 응답 메서드를 추가 합니다.
  * `HttpResponseMessage` 생성기
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * 작업 결과 메서드:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* 앱의 추가 `IContentNegotiator` DI (종속성 주입) 컨테이너에 인스턴스를 추가 하 고 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)의 콘텐츠 협상 관련 형식을 사용 가능 하 게 설정 합니다. 이러한 형식의 예로는 `DefaultContentNegotiator` 및가 `MediaTypeFormatter` 있습니다.

호환성 shim을 사용 하려면 다음을 수행 합니다.

1. [AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 패키지를 설치 합니다.
1. 에서를 호출 하 여 앱의 DI 컨테이너에 호환성 shim의 서비스를 등록 `services.AddMvc().AddWebApiConventions()` `Startup.ConfigureServices` 합니다.
1. 앱 호출의에서를 사용 하 여 웹 API 관련 경로를 정의 `MapWebApiRoute` `IRouteBuilder` `IApplicationBuilder.UseMvc` 합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
