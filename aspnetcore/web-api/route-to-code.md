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
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a>ASP.NET Core의 기본 JSON Api Route-to-code

작성자: [James Newton-King](https://github.com/jamesnk)

ASP.NET Core은 JSON 웹 Api를 만드는 다양 한 방법을 지원 합니다.

* [ASP.NET Core WEB api](xref:web-api/index) 는 api를 만들기 위한 전체 프레임 워크를 제공 합니다. 에서 상속 하 여 서비스를 만듭니다 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . 프레임 워크에서 제공 하는 일부 기능에는 모델 바인딩, 유효성 검사, 콘텐츠 협상, 입력 및 출력 형식 및 OpenAPI가 포함 됩니다.
* Route-to-code 는 web API를 ASP.NET Core 하는 프레임 워크가 아닌 대안입니다. Route-to-code[ASP.NET Core 라우팅을](xref:fundamentals/routing) 코드에 직접 연결 합니다. 코드에서 요청을 읽고 응답을 씁니다. Route-to-code 에는 web API의 고급 기능이 없지만이 기능을 사용 하는 데 필요한 구성도 없습니다.

Route-to-code 는 작은 및 기본 JSON 웹 Api를 빌드할 때 좋은 방법입니다.

## <a name="create-json-web-apis"></a>JSON 웹 Api 만들기

ASP.NET Core은 JSON 웹 Api를 쉽게 만들 도우미 메서드를 제공 합니다.

* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A>`Content-Type`JSON 콘텐츠 형식의 헤더를 확인 합니다.
* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> 요청에서 JSON을 읽고 지정 된 형식으로 deserialize 합니다.
* <xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> 지정 된 값을 JSON으로 응답 본문에 쓰고 응답 콘텐츠 형식을로 설정 합니다 `application/json` .

*Startup.cs* 에는 간단한 경로 기반 JSON api가 지정 되어 있습니다. 경로 및 API 논리는 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 앱의 요청 파이프라인의 일부로에서 구성 됩니다.

### <a name="write-json-response"></a>JSON 응답 쓰기

앱에 대 한 JSON API를 구성 하는 다음 코드를 고려 합니다.

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

앞의 코드가 하는 역할은 다음과 같습니다.

* 을 경로 템플릿으로 사용 하 여 HTTP GET API 끝점을 추가 `/hello/{name:alpha}` 합니다.
* 경로가 일치 하는 경우 API는 `name` 요청에서 경로 값을 읽습니다.
* 를 사용 하 여 익명 형식을 JSON 응답으로 씁니다 `WriteAsJsonAsync` .

### <a name="read-json-request"></a>JSON 요청 읽기

`HasJsonContentType` 및 `ReadFromJsonAsync` 는 경로 기반 JSON API에서 json 응답을 deserialize 하는 데 사용할 수 있습니다.

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

앞의 코드가 하는 역할은 다음과 같습니다.

* 을 경로 템플릿으로 사용 하 여 HTTP POST API 끝점을 추가 `/weather` 합니다.
* 경로가 일치 하면는 `HasJsonContentType` 요청 콘텐츠 형식의 유효성을 검사 합니다. 비 JSON 콘텐츠 형식이 415 상태 코드를 반환 합니다.
* 콘텐츠 형식이 JSON 인 경우 요청 콘텐츠는에서 deserialize 됩니다 `ReadFromJsonAsync` .

### <a name="configure-json-serialization"></a>JSON serialization 구성

JSON serialization을 사용자 지정 하는 방법에는 다음 두 가지가 있습니다.

* 메서드에서 기본 직렬화 옵션을 구성할 수 있습니다 <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> `Startup.ConfigureServices` .
* `WriteAsJsonAsync` 및에는 `ReadFromJsonAsync` 개체를 허용 하는 오버 로드가 있습니다 <xref:System.Text.Json.JsonSerializerOptions> . 이 옵션 개체는 기본 옵션을 재정의 합니다.

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

Route-to-code 인증 및 권한 부여를 지원 합니다. 및와 같은 특성 `[Authorize]` `[AllowAnonymous]` 은 요청 대리자에 매핑되는 끝점에 배치할 수 없습니다. 대신 및 확장 메서드를 사용 하 여 권한 부여 메타 데이터를 추가 <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> 합니다.

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a>종속성 주입

생성자를 사용 하는 [DI (종속성 주입)](xref:fundamentals/dependency-injection) 는와 함께 사용할 수 없습니다 Route-to-code . Web API는 생성자에 삽입 된 서비스를 사용 하 여 컨트롤러를 만듭니다. 끝점을 실행할 때는 형식이 생성 되지 않으므로 서비스를 수동으로 확인 해야 합니다.

경로 기반 Api는를 사용 <xref:System.IServiceProvider> 하 여 서비스를 확인할 수 있습니다.

* 와 같은 임시 및 범위가 지정 된 수명 서비스 `DbContext` 는 끝점의 요청 대리자 내에서 [HttpContext 서비스](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 에서 확인 되어야 합니다.
* `ILogger` [IEndpointRouteBuilder. ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)에서와 같은 Singleton 수명 서비스를 확인할 수 있습니다. 서비스는 요청 위임 외부에서 확인 되 고 끝점 간에 공유 될 수 있습니다.

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

DI를 광범위 하 게 사용 하는 Api는 DI를 지 원하는 ASP.NET Core 앱 유형을 사용 하는 것이 좋습니다. 예를 들어 웹 API를 ASP.NET Core 합니다. 컨트롤러의 생성자를 사용 하는 서비스 주입은 수동으로 서비스를 해결 하는 것 보다 더 쉽습니다.

## <a name="api-project-structure"></a>API 프로젝트 구조

경로 기반 Api는 *Startup.cs* 에서 찾을 필요가 없습니다. Api는 다른 파일에 배치 하 고 시작 시에 매핑할 수 있습니다 `UseEndpoints` . 이 방법은 시작 구성 파일 크기를 줄입니다.

`UserApi`메서드를 정의 하는 다음 정적 클래스를 살펴보겠습니다 `Map` . 메서드는 경로 기반 Api를 매핑합니다.

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

`Startup.Configure`메서드에서 `Map` 메서드 및 다른 클래스의 정적 메서드는에서 호출 됩니다 `UseEndpoints` .

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a>Web API와 비교 하 여 주목할 만한 누락 기능

Route-to-code 는 기본 JSON Api 용으로 설계 되었습니다. ASP.NET Core Web API에서 제공 하는 다양 한 고급 기능을 지원 하지 않습니다.

에서 제공 하지 않는 기능에 Route-to-code 는 다음이 포함 됩니다.

* 모델 바인딩
* 모델 유효성 검사
* OpenAPI/Swagger
* 콘텐츠 협상
* 생성자 종속성 주입
* `ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))

[ASP.NET Core WEB api](xref:web-api/index) 를 사용 하 여 위의 목록에 있는 일부 기능이 필요한 경우 api를 만드는 것이 좋습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
