---
title: ASP.NET Core에서 CORS (원본 간 요청) 사용
author: rick-anderson
description: ASP.NET Core 앱에서 원본 간 요청을 허용 하거나 거부 하기 위한 표준으로 CORS를 학습 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: 7afa8105e0ab007153d5c3e8238765d4e9f22641
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586802"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>ASP.NET Core에서 CORS (원본 간 요청) 사용

::: moniker range=">= aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://twitter.com/serpent5)

이 문서에서는 ASP.NET Core 앱에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.

브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다. 이러한 제한 사항을 *동일 원본 정책* 이라고 합니다. 동일 원본 정책은 악성 사이트에서 다른 사이트의 중요한 데이터를 읽지 못하도록 차단합니다. 경우에 따라 다른 사이트에서 앱에 대 한 원본 간 요청을 수행할 수 있도록 허용 하는 것이 좋습니다. 자세한 내용은 [MOZILLA CORS 문서](https://developer.mozilla.org/docs/Web/HTTP/CORS)를 참조 하세요.

CORS ( [원본 간 리소스 공유](https://www.w3.org/TR/cors/) ):

* 는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.
* 는 CORS 완화 security의 보안 기능이 **아닙니다** . API는 CORS를 허용 하 여 안전 하지 않습니다. 자세한 내용은 [CORS 작동 방식](#how-cors)을 참조 하세요.
* 다른 서버를 거부 하는 동안 서버에서 일부 교차 원본 요청을 명시적으로 허용할 수 있습니다.
* [JSONP](/dotnet/framework/wcf/samples/jsonp)와 같은 이전 기술 보다 안전 하 고 유연 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>동일한 원점

동일한 스키마, 호스트 및 포트 ([RFC 6454](https://tools.ietf.org/html/rfc6454))가 있는 경우 두 url의 원본이 동일 합니다.

이러한 두 Url의 출처는 동일 합니다.

* `https://example.com/foo.html`
* `https://example.com/bar.html`

이러한 Url의 원본은 이전 두 Url과 다릅니다.

* `https://example.net`: 다른 도메인
* `https://www.example.com/foo.html`: 다른 하위 도메인
* `http://example.com/foo.html`: 다른 구성표
* `https://example.com:9000/foo.html`: 다른 포트

## <a name="enable-cors"></a>CORS를 사용하도록 설정

CORS를 사용 하도록 설정 하는 방법에는 세 가지가 있습니다.

* 미들웨어에서 [명명 된 정책](#np) 또는 [기본 정책을](#dp)사용 합니다.
* [끝점 라우팅을](#ecors)사용 합니다.
* [[EnableCors]](#attr) 특성을 사용 합니다.

명명 된 정책에서 [[EnableCors]](#attr) 특성을 사용 하면 CORS를 지 원하는 끝점을 제한 하는 가장 세밀 한 제어를 제공 합니다.

> [!WARNING]
> <xref:Owin.CorsExtensions.UseCors%2A><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>을 사용 하는 경우 먼저 `UseResponseCaching` 를 호출 해야 합니다.

각 접근 방식은 다음 섹션에 자세히 설명 되어 있습니다.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>명명 된 정책 및 미들웨어를 사용 하는 CORS

CORS 미들웨어는 크로스-원본 요청을 처리 합니다. 다음 코드는 지정 된 원본을 사용 하 여 모든 앱의 끝점에 CORS 정책을 적용 합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

위의 코드는

* 정책 이름을로 설정 `_myAllowSpecificOrigins` 합니다. 정책 이름은 임의로입니다.
* <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>확장 메서드를 호출 하 고 `_myAllowSpecificOrigins` CORS 정책을 지정 합니다. `UseCors` CORS 미들웨어를 추가 합니다. 에 대 `UseCors` 한 호출은 앞에 배치 해야 `UseRouting` `UseAuthorization` 합니다. 자세한 내용은 [미들웨어 순서](xref:fundamentals/middleware/index#middleware-order)를 참조 하세요.
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [람다 식을](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)사용 하 여를 호출 합니다. 람다는 개체를 사용 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 합니다. 등의 [구성 옵션](#cors-policy-options)에 `WithOrigins` 대해서는이 문서의 뒷부분에서 설명 합니다.
* `_myAllowSpecificOrigins`모든 컨트롤러 끝점에 대해 CORS 정책을 사용 하도록 설정 합니다. 특정 끝점에 CORS 정책을 적용 하려면 [끝점 라우팅](#ecors) 을 참조 하세요.
* [응답 캐싱 미들웨어](xref:performance/caching/middleware)를 사용 하는 경우 앞에서를 호출 <xref:Owin.CorsExtensions.UseCors%2A> <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> 합니다.

끝점 라우팅을 사용 하 여 및에 대 한 호출 사이에 CORS 미들웨어를 실행 하도록 구성 **해야 합니다** `UseRouting` `UseEndpoints` .

이전 코드와 유사한 코드 테스트에 대 한 지침은 [CORS 테스트](#testc) 를 참조 하세요.

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>메서드 호출은 CORS 서비스를 앱의 서비스 컨테이너에 추가 합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

자세한 내용은이 문서의 [CORS 정책 옵션](#cpo) (영문)을 참조 하세요.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>다음 코드와 같이 메서드를 연결할 수 있습니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

참고: 지정 된 URL은 후행 슬래시 ()를 포함 **하지** 않아야 합니다 `/` . URL이로 종료 되 면 `/` 비교가 반환 `false` 되 고 헤더는 반환 되지 않습니다.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>기본 정책 및 미들웨어를 사용 하는 CORS

다음 강조 표시 된 코드는 기본 CORS 정책을 사용 하도록 설정 합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

위의 코드는 모든 컨트롤러 끝점에 기본 CORS 정책을 적용 합니다.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>엔드포인트 라우팅을 사용하여 Cors 사용

현재를 사용 하 여 끝점 별로 CORS를 사용 하도록 설정 하면 `RequireCors` [자동 실행 전 요청](#apf)을 지원 **하지** 않습니다. 자세한 내용은 [이 GitHub 문제](https://github.com/dotnet/aspnetcore/issues/20709) 및 [끝점 라우팅을 사용 하 여 CORS 테스트 및 [httpoptions]](#tcer)를 참조 하세요.

끝점 라우팅을 사용 하면 확장 메서드 집합을 사용 하 여 끝점 별로 CORS를 사용 하도록 설정할 수 있습니다 <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> .

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

위의 코드에서

* `app.UseCors` CORS 미들웨어를 사용 하도록 설정 합니다. 기본 정책은 구성 되지 않았기 때문에 `app.UseCors()` CORS를 사용 하도록 설정 하지 않습니다.
* `/echo`및 컨트롤러 끝점은 지정 된 정책을 사용 하 여 원본 간 요청을 허용 합니다.
* `/echo2` Razor 기본 정책을 지정 하지 않았으므로 및 페이지 끝점에서 원본 간 요청을 허용 **하지 않습니다** .

[[DisableCors]](#dc) 특성은를 **사용 하** 여 끝점 라우팅을 통해 설정 된 CORS를 사용 하지 않도록 설정 `RequireCors` 합니다.

위와 비슷한 코드 테스트에 대 한 지침은 [끝점 라우팅을 사용 하 여 CORS 테스트 및 [HttpOptions]](#tcer) 를 참조 하세요.

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>특성과 함께 CORS 사용

[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성으로 cors를 사용 하도록 설정 하 고 cors가 필요한 끝점에만 명명 된 정책을 적용 하면 가장 많이 사용 되는 컨트롤이 제공 됩니다.

[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용 하는 대안을 제공 합니다. `[EnableCors]`특성은 모든 끝점이 아니라 선택한 끝점에 대해 CORS를 사용 하도록 설정 합니다.

* `[EnableCors]` 기본 정책을 지정 합니다.
* `[EnableCors("{Policy String}")]` 명명 된 정책을 지정 합니다.

`[EnableCors]`특성은 다음에 적용 될 수 있습니다.

* Razor 페이지 `PageModel`
* 컨트롤러
* 컨트롤러 동작 방법

특성을 사용 하는 컨트롤러, 페이지 모델 또는 작업 메서드에 다른 정책을 적용할 수 있습니다 `[EnableCors]` . `[EnableCors]`특성이 컨트롤러, 페이지 모델 또는 동작 메서드에 적용 되 고 CORS가 미들웨어에서 사용 하도록 설정 된 경우 **두 정책이 모두** 적용 됩니다. **정책을 결합 하지 않는 것이 좋습니다.** `[EnableCors]` **동일한 앱에서 특성 또는 미들웨어를 사용 합니다.**

다음 코드는 각 메서드에 다른 정책을 적용 합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

다음 코드는 두 개의 CORS 정책을 만듭니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

CORS 요청 제한의 가장 적합 한 제어:

* 명명 된 정책에를 사용 `[EnableCors("MyPolicy")]` 합니다.
* 기본 정책을 정의 하지 않습니다.
* [끝점 라우팅을](#ecors)사용 하지 않습니다.

다음 섹션의 코드는 위의 목록을 충족 합니다.

이전 코드와 유사한 코드 테스트에 대 한 지침은 [CORS 테스트](#testc) 를 참조 하세요.

<a name="dc"></a>

### <a name="disable-cors"></a>CORS 사용 안 함

[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 [끝점 라우팅으로](#ecors)설정 된 CORS를 사용 하지 않도록 설정 **하지** 않습니다.

다음 코드에서는 CORS 정책을 정의 합니다 `"MyPolicy"` .

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

다음 코드는 작업에 대해 CORS를 사용 하지 않도록 설정 합니다 `GetValues2` .

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

위의 코드는

* [끝점 라우팅을](#ecors)사용 하 여 CORS를 사용 하도록 설정 하지 않습니다.
* [기본 CORS 정책을](#dp)정의 하지 않습니다.
* [[EnableCors ("MyPolicy")]](#attr) 를 사용 하 여 `"MyPolicy"` 컨트롤러에 대 한 CORS 정책을 사용 하도록 설정 합니다.
* 메서드에 대해 CORS를 사용 하지 않도록 설정 `GetValues2` 합니다.

위의 코드를 테스트 하는 방법에 대 한 지침은 [CORS 테스트](#testc) 를 참조 하세요.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS 정책 옵션

이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션에 대해 설명 합니다.

* [허용 되는 원본 설정](#set-the-allowed-origins)
* [허용 되는 HTTP 메서드 설정](#set-the-allowed-http-methods)
* [허용 되는 요청 헤더 설정](#set-the-allowed-request-headers)
* [노출 된 응답 헤더 설정](#set-the-exposed-response-headers)
* [원본 간 요청에 대 한 자격 증명](#credentials-in-cross-origin-requests)
* [실행 전 만료 시간 설정](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 는에서 호출 됩니다 `Startup.ConfigureServices` . 일부 옵션의 경우 [CORS 작동 방법](#how-cors) 섹션을 먼저 읽는 것이 도움이 될 수 있습니다.

## <a name="set-the-allowed-origins"></a>허용 되는 원본 설정

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: 모든 원본에서 임의의 스키마 (또는)로 CORS 요청을 허용 `http` `https` 합니다. `AllowAnyOrigin`*모든 웹 사이트* 에서 앱에 대 한 원본 간 요청을 수행할 수 있기 때문에은 안전 하지 않습니다.

> [!NOTE]
> `AllowAnyOrigin`및 지정 `AllowCredentials` 은 안전 하지 않은 구성으로, 사이트 간 요청 위조를 발생 시킬 수 있습니다. 앱이 두 가지 방법으로 구성 된 경우 CORS 서비스는 잘못 된 CORS 응답을 반환 합니다.

`AllowAnyOrigin` 실행 전 요청 및 헤더에 영향을 줍니다 `Access-Control-Allow-Origin` . 자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 원본이 허용 되는지 평가할 때 구성 된 와일드 카드 도메인에 대 한 원본을 허용 하는 함수로 정책의 속성을 설정 합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>허용 되는 HTTP 메서드 설정

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* 모든 HTTP 메서드를 허용 합니다.
* 실행 전 요청 및 헤더에 영향을 줍니다 `Access-Control-Allow-Methods` . 자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.

### <a name="set-the-allowed-request-headers"></a>허용 되는 요청 헤더 설정

[작성자 요청 헤더](https://xhr.spec.whatwg.org/#request)라는 CORS 요청에서 특정 헤더를 보낼 수 있게 하려면를 호출 하 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 고 허용 되는 헤더를 지정 합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

모든 [작성자 요청 헤더](https://www.w3.org/TR/cors/#author-request-headers)를 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> .

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader` 실행 전 요청 및 [액세스 제어 요청 헤더](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) 헤더에 영향을 줍니다. 자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.

로 지정 된 특정 헤더와 일치 하는 CORS 미들웨어 정책은에 지정 된 헤더와 정확 하 게 일치 하는 `WithHeaders` 경우에만 가능 합니다 `Access-Control-Request-Headers` `WithHeaders` .

예를 들어 다음과 같이 구성 된 앱을 살펴보겠습니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

CORS 미들웨어는 다음 요청 헤더를 사용 하 여 실행 전 요청을 거부 `Content-Language` [합니다 (Headernames. contentlanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) `WithHeaders` .

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다. 따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.

### <a name="set-the-exposed-response-headers"></a>노출 된 응답 헤더 설정

기본적으로 브라우저는 모든 응답 헤더를 앱에 노출 하지 않습니다. 자세한 내용은 [W3C 원본 간 리소스 공유 (용어): 단순 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조 하세요.

기본적으로 사용할 수 있는 응답 헤더는 다음과 같습니다.

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 사양은 이러한 헤더 *단순 응답 헤더* 를 호출 합니다. 앱에서 다른 헤더를 사용할 수 있도록 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>원본 간 요청에 대 한 자격 증명

자격 증명을 CORS 요청에서 특수 하 게 처리 해야 합니다. 기본적으로 브라우저는 원본 간 요청과 함께 자격 증명을 보내지 않습니다. 자격 증명 cookie 에는 및 HTTP 인증 스키마가 포함 됩니다. 원본 간 요청을 사용 하 여 자격 증명을 보내려면 클라이언트는를로 설정 해야 합니다 `XMLHttpRequest.withCredentials` `true` .

`XMLHttpRequest`직접 사용:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

JQuery 사용:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)사용:

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

서버에서 자격 증명을 허용 해야 합니다. 원본 간 자격 증명을 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> .

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

HTTP 응답에는 `Access-Control-Allow-Credentials` 서버에서 원본 간 요청에 대 한 자격 증명을 허용 하도록 브라우저에 지시 하는 헤더가 포함 되어 있습니다.

브라우저에서 자격 증명을 전송 하지만 응답에 유효한 헤더가 포함 되지 않은 경우 `Access-Control-Allow-Credentials` 브라우저에서 앱에 응답을 노출 하지 않으며 크로스-원본 요청이 실패 합니다.

크로스-원본 자격 증명을 허용 하는 것은 보안상 위험 합니다. 다른 도메인의 웹 사이트는 사용자의 지식 없이 사용자를 대신 하 여 사용자의 앱에 로그인 한 사용자의 자격 증명을 보낼 수 있습니다. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS 사양은 `"*"` 헤더가 있는 경우 원본으로 설정 (모든 원본)을 사용할 수 없다는 것을 `Access-Control-Allow-Credentials` 나타냅니다.

<a name="pref"></a>

## <a name="preflight-requests"></a>실행 전 요청

일부 CORS 요청의 경우 브라우저는 실제 요청을 만들기 전에 추가 [옵션](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 요청을 보냅니다. 이 요청을 실행 [전 요청](https://developer.mozilla.org/docs/Glossary/Preflight_request)이라고 합니다. 다음 조건이 **모두** 충족 되는 경우 브라우저에서 실행 전 요청을 건너뛸 수 있습니다.

* 요청 메서드는 GET, HEAD 또는 POST입니다.
* 앱은 `Accept` ,, `Accept-Language` `Content-Language` , `Content-Type` 또는 이외의 요청 헤더를 설정 하지 않습니다 `Last-Event-ID` .
* `Content-Type`헤더 (설정 된 경우)에는 다음 값 중 하나가 있습니다.
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

클라이언트 요청에 대해 설정 된 요청 헤더에 대 한 규칙은 응용 프로그램에서 개체에 대해를 호출 하 여 설정 하는 헤더에 적용 됩니다 `setRequestHeader` `XMLHttpRequest` . CORS 사양은 이러한 헤더 [작성자 요청 헤더](https://www.w3.org/TR/cors/#author-request-headers)를 호출 합니다. 이 규칙은 브라우저에서 설정할 수 있는 헤더 (예:, 또는)에는 적용 되지 않습니다 `User-Agent` `Host` `Content-Length` .

다음은이 문서의 [CORS 테스트](#testc) 섹션에서 **[Put test]** 단추를 통해 수행 된 실행 전 요청과 유사한 응답의 예입니다.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

실행 전 요청은 [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 메서드를 사용 합니다. 여기에는 다음 헤더가 포함 될 수 있습니다.

* [액세스 제어-요청 메서드](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): 실제 요청에 사용 되는 HTTP 메서드입니다.
* [액세스 제어-요청 헤더](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): 앱이 실제 요청에 대해 설정 하는 요청 헤더 목록입니다. 앞에서 설명한 것 처럼 브라우저에서 설정 하는 헤더 (예:)는 여기에 포함 되지 않습니다 `User-Agent` .
* [액세스 제어-허용 방법](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

실행 전 요청이 거부 되 면 앱은 응답을 반환 `200 OK` 하지만 CORS 헤더를 설정 하지 않습니다. 따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다. 거부 된 실행 전 요청에 대 한 예는이 문서의 [CORS 테스트](#testc) 섹션을 참조 하세요.

콘솔 앱은 F12 도구를 사용 하 여 브라우저에 따라 다음과 같은 오류를 표시 합니다.

* Firefox: 크로스-원본 요청 차단 됨: 동일한 원본 정책이에서 원격 리소스를 읽을 것을 허용 하지 `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` 않습니다. (원인: CORS 요청이 실패 했습니다.) [자세한 내용](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Chromium based: ' ' 원본에서 ' '의 fetch에 대 한 액세스가 https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net CORS 정책에 의해 차단 되었습니다. 실행 전 요청에 대 한 응답은 액세스 제어 확인을 통과 하지 않습니다. 요청한 리소스에 ' 액세스 제어-허용-원본 ' 헤더가 없습니다. 사용자 요구에 불명확한 응답이 제공되면 요청 모드를 'no-cors'로 설정하여 CORS가 사용되지 않도록 설정된 리소스를 가져옵니다.

특정 헤더를 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

모든 [작성자 요청 헤더](https://www.w3.org/TR/cors/#author-request-headers)를 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> .

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

브라우저의 설정 방법은 일관 되지 않습니다 `Access-Control-Request-Headers` . 다음 중 하나입니다.

* 헤더는 다음 이외의 값으로 설정 됩니다. `"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> 가 호출 됩니다 .에는 `Accept` , `Content-Type` 및를 포함 하 고 `Origin` 지원 하려는 사용자 지정 헤더를 포함 합니다.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>자동 실행 전 요청 코드

CORS 정책이 적용 되는 경우 다음 중 하나를 수행 합니다.

* 에서를 호출 하 여 전역적으로 `app.UseCors` `Startup.Configure`
* 특성을 사용 `[EnableCors]` 합니다.

ASP.NET Core는 실행 전 옵션 요청에 응답 합니다.

현재를 사용 하 여 끝점 별로 CORS를 사용 하도록 설정 하면 `RequireCors` 자동 실행 전 요청을 지원 **하지** 않습니다.

이 문서의 [CORS 테스트](#testc) 섹션에서는이 동작을 보여 줍니다.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[HttpOptions] 실행 전 요청에 대 한 특성

적절 한 정책으로 CORS를 사용 하도록 설정 하면 ASP.NET Core 일반적으로 CORS 실행 전 요청에 자동으로 응답 합니다. 일부 시나리오에서는 그렇지 않을 수 있습니다. 예를 들어 [끝점 라우팅과 함께 CORS](#ecors)를 사용 합니다.

다음 코드는 [[httpoptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) 특성을 사용 하 여 OPTIONS 요청에 대 한 끝점을 만듭니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

위의 코드를 테스트 하는 방법에 대 한 지침은 [끝점 라우팅을 사용 하 여 CORS 테스트 및 [HttpOptions]](#tcer) 를 참조 하세요.

### <a name="set-the-preflight-expiration-time"></a>실행 전 만료 시간 설정

헤더는 실행 `Access-Control-Max-Age` 전 요청에 대 한 응답을 캐시할 수 있는 기간을 지정 합니다. 이 헤더를 설정 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> .

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS 작동 방법

이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 요청에 발생 하는 작업에 대해 설명 합니다.

* CORS는 보안 기능이 **아닙니다** . CORS는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.
  * 예를 들어 악의적인 행위자는 사이트에 대해 [XSS (교차 사이트 스크립팅)](xref:security/cross-site-scripting) 를 사용 하 고 CORS 지원 사이트에 대 한 교차 사이트 요청을 실행 하 여 정보를 도용할 수 있습니다.
* API는 CORS를 허용 하 여 안전 하지 않습니다.
  * CORS를 적용 하는 것은 클라이언트 (브라우저)에 있습니다. 서버는 요청을 실행 하 고 응답을 반환 합니다. 클라이언트는 오류를 반환 하 고 응답을 차단 합니다. 예를 들어 다음 도구 중 하나는 서버 응답을 표시 합니다.
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * 주소 표시줄에 URL을 입력 하는 웹 브라우저입니다.
* 서버에서 브라우저가 원본 간 [Xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 요청을 실행 하도록 허용 하는 방법입니다. 그렇지 않으면이를 사용할 수 없습니다.
  * CORS가 없는 브라우저는 크로스-원본 요청을 수행할 수 없습니다. CORS 이전에는 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 을 사용 하 여이 제한을 회피 했습니다. JSONP는 XHR을 사용 하지 않으며 태그를 사용 하 여 `<script>` 응답을 수신 합니다. 스크립트를 크로스-원본으로 로드할 수 있습니다.

[CORS 사양](https://www.w3.org/TR/cors/) 에는 크로스-원본 요청을 가능 하 게 하는 몇 가지 새로운 HTTP 헤더가 도입 되었습니다. 브라우저에서 CORS를 지 원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정 합니다. CORS를 사용 하도록 설정 하는 데 사용자 지정 JavaScript 코드가 필요 하지 않습니다.

배포 된 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) 에 대 한 [테스트 배치 단추](https://cors3.azurewebsites.net/test)

다음은 [값](https://cors3.azurewebsites.net/) 테스트 단추에서로의 원본 간 요청 예제입니다 `https://cors1.azurewebsites.net/api/values` . `Origin`헤더:

* 요청을 수행 하는 사이트의 도메인을 제공 합니다.
* 는 필수 이며 호스트와 달라 야 합니다.

**일반 헤더**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**응답 헤더**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**요청 헤더**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

`OPTIONS`요청에서 서버는 응답에 **응답 헤더** 헤더를 설정 합니다 `Access-Control-Allow-Origin: {allowed origin}` . 예를 들어, 배포 된 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)인 [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) 단추 `OPTIONS` 요청은 다음 헤더를 포함 합니다.

**일반 헤더**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**응답 헤더**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**요청 헤더**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

위의 **응답 헤더** 에서 서버는 응답에 [액세스 제어 허용 원본](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 헤더를 설정 합니다. `https://cors1.azurewebsites.net`이 헤더의 값은 요청의 헤더와 일치 합니다 `Origin` .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>가 호출 되 면 `Access-Control-Allow-Origin: *` 와일드 카드 값이 반환 됩니다. `AllowAnyOrigin` 모든 원본을 허용 합니다.

응답에 헤더가 포함 되지 않은 경우 `Access-Control-Allow-Origin` 원본 간 요청이 실패 합니다. 특히 브라우저에서 요청을 허용 하지 않습니다. 서버에서 성공적인 응답을 반환 하는 경우에도 브라우저에서 응답을 클라이언트 앱에 사용할 수 있도록 설정 하지 않습니다.

<a name="options"></a>

### <a name="display-options-requests"></a>표시 옵션 요청

기본적으로 Chrome 및 Edge 브라우저는 F12 도구의 네트워크 탭에서 옵션 요청을 표시 하지 않습니다. 이러한 브라우저에 옵션 요청을 표시 하려면 다음을 수행 합니다.

* `chrome://flags/#out-of-blink-cors` 또는 `edge://flags/#out-of-blink-cors`
* 플래그를 사용 하지 않습니다.
* 시작할지.

Firefox에는 기본적으로 옵션 요청이 표시 됩니다.

## <a name="cors-in-iis"></a>IIS의 CORS

IIS에 배포할 때 서버에서 익명 액세스를 허용 하도록 구성 되지 않은 경우 CORS를 Windows 인증 전에 실행 해야 합니다. 이 시나리오를 지원 하려면 앱에 대해 [IIS CORS 모듈](https://www.iis.net/downloads/microsoft/iis-cors-module) 을 설치 하 고 구성 해야 합니다.

<a name="testc"></a>

## <a name="test-cors"></a>CORS 테스트

[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) 에는 CORS를 테스트 하는 코드가 있습니다. [다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요. 이 샘플은 페이지가 추가 된 API 프로젝트입니다 Razor .

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`[다운로드 샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)와 유사한 샘플 앱을 테스트 하는 데만 사용 해야 합니다.

다음은 `ValuesController` 테스트에 대 한 끝점을 제공 합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) 는 RouteInfo NuGet 패키지 [Rick.Doc](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) 에서 제공 하며 경로 정보를 표시 합니다.

다음 방법 중 하나를 사용 하 여 앞의 샘플 코드를 테스트 합니다.

* 에서 배포 된 샘플 앱을 사용 [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) 합니다. 샘플을 다운로드할 필요가 없습니다.
* `dotnet run`의 기본 URL을 사용 하 여 샘플을 실행 합니다 `https://localhost:5001` .
* URL에 대해 44398로 설정 된 포트를 사용 하 여 Visual Studio에서 샘플을 실행 합니다 `https://localhost:44398` .

F12 도구를 사용 하 여 브라우저 사용:

* **값** 단추를 선택 하 고 **네트워크** 탭에서 헤더를 검토 합니다.
* **테스트 배치** 단추를 선택 합니다. 옵션 요청 표시에 대 한 지침은 [옵션 요청 표시](#options) 를 참조 하세요. **Put 테스트** 는 옵션 실행 전 요청 및 PUT 요청 이라는 두 개의 요청을 만듭니다.
* **`GetValues2 [DisableCors]`** 실패 한 CORS 요청을 트리거하는 단추를 선택 합니다. 문서에 설명 된 것 처럼 응답은 200 성공을 반환 하지만 CORS 요청은 수행 되지 않습니다. CORS 오류를 보려면 **콘솔** 탭을 선택 합니다. 브라우저에 따라 다음과 같은 오류가 표시 됩니다.

     원본에서 가져오기에 대 한 액세스가 `'https://cors1.azurewebsites.net/api/values/GetValues2'` `'https://cors3.azurewebsites.net'` CORS 정책에 의해 차단 되었습니다. 요청 된 리소스에 ' 액세스 제어-원본 ' 헤더가 없습니다. 사용자 요구에 불명확한 응답이 제공되면 요청 모드를 'no-cors'로 설정하여 CORS가 사용되지 않도록 설정된 리소스를 가져옵니다.
     
CORS 사용 끝점은 [말아](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler)또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 하 여 테스트할 수 있습니다. 도구를 사용 하는 경우 헤더에 의해 지정 된 요청의 원점은 `Origin` 요청을 받는 호스트와 달라 야 합니다. 요청이 헤더의 값을 기반으로 하는 *교차 원본이* 아닌 경우 `Origin` :

* CORS 미들웨어가 요청을 처리할 필요가 없습니다.
* CORS 헤더는 응답에서 반환 되지 않습니다.

다음 명령은를 사용 `curl` 하 여 정보를 포함 하는 옵션 요청을 실행 합니다.

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>끝점 라우팅을 사용 하 여 CORS 테스트 및 [HttpOptions]

현재를 사용 하 여 끝점 별로 CORS를 사용 하도록 설정 하면 `RequireCors` [자동 실행 전 요청](#apf)을 지원 **하지** 않습니다. 끝점 라우팅을 사용 하 여 [CORS를 사용 하도록 설정 하](#ecors)는 다음 코드를 고려 합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

`TodoItems1Controller`테스트를 위한 끝점은 다음과 같습니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

배포 된 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)의 [테스트 페이지](https://cors1.azurewebsites.net/test?number=1) 에서 이전 코드를 테스트 합니다.

끝점이 실행 전 요청에 응답 하 고이 요청에 응답 하기 때문에 **Delete [EnableCors]** 및 **GET [EnableCors]** 단추가 성공 합니다 `[EnableCors]` . 다른 끝점은 실패 합니다. [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) 가 다음을 보내기 때문에 **GET** 단추가 실패 합니다.

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

다음은 `TodoItems2Controller` 비슷한 끝점을 제공 하지만 OPTIONS 요청에 응답 하는 명시적인 코드를 포함 합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

배포 된 샘플의 [테스트 페이지](https://cors1.azurewebsites.net/test?number=2) 에서 이전 코드를 테스트 합니다. **컨트롤러** 드롭다운 목록에서 실행 **전** 을 선택 하 고 **컨트롤러를 설정** 합니다. 끝점에 대 한 모든 CORS 호출이 `TodoItems2Controller` 성공 했습니다.

## <a name="additional-resources"></a>추가 리소스

* [CORS (원본 간 리소스 공유)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [IIS CORS 모듈 시작](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 문서에서는 ASP.NET Core 앱에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.

브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다. 이러한 제한 사항을 *동일 원본 정책* 이라고 합니다. 동일 원본 정책은 악성 사이트에서 다른 사이트의 중요한 데이터를 읽지 못하도록 차단합니다. 경우에 따라 다른 사이트에서 앱에 대 한 원본 간 요청을 허용 하는 것이 좋습니다. 자세한 내용은 [MOZILLA CORS 문서](https://developer.mozilla.org/docs/Web/HTTP/CORS)를 참조 하세요.

CORS ( [원본 간 리소스 공유](https://www.w3.org/TR/cors/) ):

* 는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.
* 는 CORS 완화 security의 보안 기능이 **아닙니다** . API는 CORS를 허용 하 여 안전 하지 않습니다. 자세한 내용은 [CORS 작동 방식](#how-cors)을 참조 하세요.
* 다른 서버를 거부 하는 동안 서버에서 일부 교차 원본 요청을 명시적으로 허용할 수 있습니다.
* [JSONP](/dotnet/framework/wcf/samples/jsonp)와 같은 이전 기술 보다 안전 하 고 유연 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>동일한 원점

동일한 스키마, 호스트 및 포트 ([RFC 6454](https://tools.ietf.org/html/rfc6454))가 있는 경우 두 url의 원본이 동일 합니다.

이러한 두 Url의 출처는 동일 합니다.

* `https://example.com/foo.html`
* `https://example.com/bar.html`

이러한 Url의 원본은 이전 두 Url과 다릅니다.

* `https://example.net`: 다른 도메인
* `https://www.example.com/foo.html`: 다른 하위 도메인
* `http://example.com/foo.html`: 다른 구성표
* `https://example.com:9000/foo.html`: 다른 포트

Internet Explorer는 원본을 비교할 때 포트를 고려 하지 않습니다.

## <a name="cors-with-named-policy-and-middleware"></a>명명 된 정책 및 미들웨어를 사용 하는 CORS

CORS 미들웨어는 크로스-원본 요청을 처리 합니다. 다음 코드는 지정 된 원본으로 전체 앱에 대해 CORS를 사용 하도록 설정 합니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

위의 코드는

* 정책 이름을 " \_ myAllowSpecificOrigins"로 설정 합니다. 정책 이름은 임의로입니다.
* <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>CORS를 사용 하도록 설정 하는 확장 메서드를 호출 합니다.
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [람다 식을](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)사용 하 여를 호출 합니다. 람다는 개체를 사용 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 합니다. 등의 [구성 옵션](#cors-policy-options)에 `WithOrigins` 대해서는이 문서의 뒷부분에서 설명 합니다.

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>메서드 호출은 CORS 서비스를 앱의 서비스 컨테이너에 추가 합니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

자세한 내용은이 문서의 [CORS 정책 옵션](#cpo) (영문)을 참조 하세요.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>메서드는 다음 코드와 같이 메서드를 연결할 수 있습니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

참고: URL은 후행 슬래시 ()를 포함 **하지** 않아야 합니다 `/` . URL이로 종료 되 면 `/` 비교가 반환 `false` 되 고 헤더는 반환 되지 않습니다.

다음 코드에서는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책을 적용 합니다.
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
참고: `UseCors` 이전에를 호출 해야 합니다 `UseMvc` .

페이지 [ Razor , 컨트롤러 및 작업 메서드에서 Cors를 사용](#ecors) 하 여 페이지/컨트롤러/동작 수준에서 cors 정책 적용을 참조 하세요.

이전 코드와 유사한 코드 테스트에 대 한 지침은 [CORS 테스트](#test) 를 참조 하세요.

## <a name="enable-cors-with-attributes"></a>특성과 함께 CORS 사용

[ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용 하는 대안을 제공 합니다. `[EnableCors]`특성은 모든 끝점이 아니라 선택한 끝점에 대해 CORS를 사용 하도록 설정 합니다.

`[EnableCors]`기본 정책을 지정 하 고 정책을 지정 하려면를 사용 `[EnableCors("{Policy String}")]` 합니다.

`[EnableCors]`특성은 다음에 적용 될 수 있습니다.

* Razor 페이지 `PageModel`
* 컨트롤러
* 컨트롤러 동작 방법

특성을 사용 하 여 컨트롤러/페이지-모델/작업에 다른 정책을 적용할 수 있습니다  `[EnableCors]` . `[EnableCors]`특성이 컨트롤러/페이지 모델/동작 메서드에 적용 되 고 CORS가 미들웨어에서 사용 하도록 설정 된 경우 두 정책이 **모두** 적용 됩니다. 정책을 결합 **하지 않는** 것이 좋습니다. `[EnableCors]`특성 또는 미들웨어를 사용 합니다 . 를 사용 하 `[EnableCors]` 는 경우 기본 정책을 정의 **하지** 마십시오.

다음 코드는 각 메서드에 다른 정책을 적용 합니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

다음 코드에서는 CORS 기본 정책과 라는 정책을 만듭니다 `"AnotherPolicy"` .

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>CORS 사용 안 함

[ &lbrack; DisableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 컨트롤러/페이지-모델/작업에 대해 CORS를 사용 하지 않도록 설정 합니다.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS 정책 옵션

이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션에 대해 설명 합니다.

* [허용 되는 원본 설정](#set-the-allowed-origins)
* [허용 되는 HTTP 메서드 설정](#set-the-allowed-http-methods)
* [허용 되는 요청 헤더 설정](#set-the-allowed-request-headers)
* [노출 된 응답 헤더 설정](#set-the-exposed-response-headers)
* [원본 간 요청에 대 한 자격 증명](#credentials-in-cross-origin-requests)
* [실행 전 만료 시간 설정](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 는에서 호출 됩니다 `Startup.ConfigureServices` . 일부 옵션의 경우 [CORS 작동 방법](#how-cors) 섹션을 먼저 읽는 것이 도움이 될 수 있습니다.

## <a name="set-the-allowed-origins"></a>허용 되는 원본 설정

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: 모든 원본에서 임의의 스키마 (또는)로 CORS 요청을 허용 `http` `https` 합니다. `AllowAnyOrigin`*모든 웹 사이트* 에서 앱에 대 한 원본 간 요청을 수행할 수 있기 때문에은 안전 하지 않습니다.

> [!NOTE]
> `AllowAnyOrigin`및 지정 `AllowCredentials` 은 안전 하지 않은 구성으로, 사이트 간 요청 위조를 발생 시킬 수 있습니다. 보안 응용 프로그램의 경우 클라이언트에서 서버 리소스에 액세스할 수 있는 권한을 부여 해야 하는 경우 정확한 원본 목록을 지정 합니다.

`AllowAnyOrigin` 실행 전 요청 및 헤더에 영향을 줍니다 `Access-Control-Allow-Origin` . 자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 원본이 허용 되는지 평가할 때 구성 된 와일드 카드 도메인에 대 한 원본을 허용 하는 함수로 정책의 속성을 설정 합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>허용 되는 HTTP 메서드 설정

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* 모든 HTTP 메서드를 허용 합니다.
* 실행 전 요청 및 헤더에 영향을 줍니다 `Access-Control-Allow-Methods` . 자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.

### <a name="set-the-allowed-request-headers"></a>허용 되는 요청 헤더 설정

*작성자 요청 헤더* 라는 CORS 요청에서 특정 헤더를 보낼 수 있게 하려면를 호출 하 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 고 허용 되는 헤더를 지정 합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

모든 작성자 요청 헤더를 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> .

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

이 설정은 실행 전 요청 및 헤더에 영향을 줍니다 `Access-Control-Request-Headers` . 자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.

CORS 미들웨어는 항상에 있는 4 개의 헤더 `Access-Control-Request-Headers` 를 CorsPolicy 헤더에 구성 된 값에 관계 없이 보낼 수 있습니다. 이 헤더 목록에는 다음이 포함 됩니다.

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

예를 들어 다음과 같이 구성 된 앱을 살펴보겠습니다.

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

가 항상 허용 되기 때문에 CORS 미들웨어는 다음 요청 헤더를 사용 하 여 실행 전 요청에 성공적으로 응답 합니다 `Content-Language` .

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>노출 된 응답 헤더 설정

기본적으로 브라우저는 모든 응답 헤더를 앱에 노출 하지 않습니다. 자세한 내용은 [W3C 원본 간 리소스 공유 (용어): 단순 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조 하세요.

기본적으로 사용할 수 있는 응답 헤더는 다음과 같습니다.

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 사양은 이러한 헤더 *단순 응답 헤더* 를 호출 합니다. 앱에서 다른 헤더를 사용할 수 있도록 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>원본 간 요청에 대 한 자격 증명

자격 증명을 CORS 요청에서 특수 하 게 처리 해야 합니다. 기본적으로 브라우저는 원본 간 요청과 함께 자격 증명을 보내지 않습니다. 자격 증명 cookie 에는 및 HTTP 인증 스키마가 포함 됩니다. 원본 간 요청을 사용 하 여 자격 증명을 보내려면 클라이언트는를로 설정 해야 합니다 `XMLHttpRequest.withCredentials` `true` .

`XMLHttpRequest`직접 사용:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

JQuery 사용:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)사용:

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

서버에서 자격 증명을 허용 해야 합니다. 원본 간 자격 증명을 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> .

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP 응답에는 `Access-Control-Allow-Credentials` 서버에서 원본 간 요청에 대 한 자격 증명을 허용 하도록 브라우저에 지시 하는 헤더가 포함 되어 있습니다.

브라우저에서 자격 증명을 전송 하지만 응답에 유효한 헤더가 포함 되지 않은 경우 `Access-Control-Allow-Credentials` 브라우저에서 앱에 응답을 노출 하지 않으며 크로스-원본 요청이 실패 합니다.

크로스-원본 자격 증명을 허용 하는 것은 보안상 위험 합니다. 다른 도메인의 웹 사이트는 사용자의 지식 없이 사용자를 대신 하 여 사용자의 앱에 로그인 한 사용자의 자격 증명을 보낼 수 있습니다. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS 사양은 `"*"` 헤더가 있는 경우 원본으로 설정 (모든 원본)을 사용할 수 없다는 것을 `Access-Control-Allow-Credentials` 나타냅니다.

### <a name="preflight-requests"></a>실행 전 요청

일부 CORS 요청의 경우 브라우저는 실제 요청을 만들기 전에 추가 요청을 보냅니다. 이 요청을 실행 *전 요청* 이라고 합니다. 다음 조건에 해당 하는 경우 브라우저에서 실행 전 요청을 건너뛸 수 있습니다.

* 요청 메서드는 GET, HEAD 또는 POST입니다.
* 앱은 `Accept` ,, `Accept-Language` `Content-Language` , `Content-Type` 또는 이외의 요청 헤더를 설정 하지 않습니다 `Last-Event-ID` .
* `Content-Type`헤더 (설정 된 경우)에는 다음 값 중 하나가 있습니다.
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

클라이언트 요청에 대해 설정 된 요청 헤더에 대 한 규칙은 응용 프로그램에서 개체에 대해를 호출 하 여 설정 하는 헤더에 적용 됩니다 `setRequestHeader` `XMLHttpRequest` . CORS 사양은 이러한 헤더 *작성자 요청 헤더* 를 호출 합니다. 이 규칙은 브라우저에서 설정할 수 있는 헤더 (예:, 또는)에는 적용 되지 않습니다 `User-Agent` `Host` `Content-Length` .

다음은 실행 전 요청의 예입니다.

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

사전 비행 요청은 HTTP OPTIONS 메서드를 사용 합니다. 여기에는 두 가지 특수 헤더가 포함 됩니다.

* `Access-Control-Request-Method`: 실제 요청에 사용 되는 HTTP 메서드입니다.
* `Access-Control-Request-Headers`: 앱이 실제 요청에 대해 설정 하는 요청 헤더 목록입니다. 앞에서 설명한 것 처럼 브라우저에서 설정 하는 헤더 (예:)는 여기에 포함 되지 않습니다 `User-Agent` .

<!-- I think this needs to be removed, was put here accidently -->

적절 한 정책으로 CORS를 사용 하도록 설정 하면 ASP.NET Core는 일반적으로 CORS 실행 전 요청에 자동으로 응답 합니다. 실행 [전 요청에 대 한 [Httpoptions] 특성을](#pro)참조 하세요.

CORS 실행 전 요청에는 헤더가 포함 될 수 `Access-Control-Request-Headers` 있으며,이는 실제 요청과 함께 전송 되는 헤더를 서버에 나타냅니다.

특정 헤더를 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

모든 작성자 요청 헤더를 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> .

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

브라우저의 설정 방법은 완전히 일치 하지 않습니다 `Access-Control-Request-Headers` . 헤더를 (또는을 사용 하는 경우) 이외의 값으로 설정 하는 경우, `"*"` <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> `Accept` 및를 포함 하 `Content-Type` 고 지원 하려는 사용자 지정 헤더를 포함 해야 합니다 `Origin` .

다음은 실행 전 요청에 대 한 응답 예제입니다 (서버에서 요청을 허용 한다고 가정).

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

응답에는 허용 된 `Access-Control-Allow-Methods` 메서드를 나열 하는 헤더와 허용 되는 헤더를 나열 하는 헤더 (선택 사항)가 포함 되어 있습니다 `Access-Control-Allow-Headers` . 실행 전 요청이 성공 하면 브라우저에서 실제 요청을 보냅니다.

실행 전 요청이 거부 되 면 앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다. 따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.

### <a name="set-the-preflight-expiration-time"></a>실행 전 만료 시간 설정

헤더는 실행 `Access-Control-Max-Age` 전 요청에 대 한 응답을 캐시할 수 있는 기간을 지정 합니다. 이 헤더를 설정 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> .

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS 작동 방법

이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 요청에 발생 하는 작업에 대해 설명 합니다.

* CORS는 보안 기능이 **아닙니다** . CORS는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.
  * 예를 들어 악의적인 행위자는 사이트에 대 한 [XSS (교차 사이트 스크립팅) 방지](xref:security/cross-site-scripting) 를 사용 하 여 정보를 도용 하기 위해 CORS 지원 사이트에 대 한 교차 사이트 요청을 실행할 수 있습니다.
* API는 CORS를 허용 하 여 안전 하지 않습니다.
  * CORS를 적용 하는 것은 클라이언트 (브라우저)에 있습니다. 서버는 요청을 실행 하 고 응답을 반환 합니다. 클라이언트는 오류를 반환 하 고 응답을 차단 합니다. 예를 들어 다음 도구 중 하나는 서버 응답을 표시 합니다.
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * 주소 표시줄에 URL을 입력 하는 웹 브라우저입니다.
* 서버에서 브라우저가 원본 간 [Xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 요청을 실행 하도록 허용 하는 방법입니다. 그렇지 않으면이를 사용할 수 없습니다.
  * 브라우저 (CORS 불포함)는 크로스-원본 요청을 수행할 수 없습니다. CORS 이전에는 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 을 사용 하 여이 제한을 회피 했습니다. JSONP는 XHR을 사용 하지 않으며 태그를 사용 하 여 `<script>` 응답을 수신 합니다. 스크립트를 크로스-원본으로 로드할 수 있습니다.

[CORS 사양](https://www.w3.org/TR/cors/) 에는 크로스-원본 요청을 가능 하 게 하는 몇 가지 새로운 HTTP 헤더가 도입 되었습니다. 브라우저에서 CORS를 지 원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정 합니다. CORS를 사용 하도록 설정 하는 데 사용자 지정 JavaScript 코드가 필요 하지 않습니다.

다음은 원본 간 요청의 예입니다. `Origin`헤더는 요청을 만드는 사이트의 도메인을 제공 합니다. `Origin`헤더는 필수 이며 호스트와 달라 야 합니다.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

서버에서 요청을 허용 하는 경우 `Access-Control-Allow-Origin` 응답에 헤더를 설정 합니다. 이 헤더의 값은 `Origin` 요청의 헤더와 일치 하거나 와일드 카드 값입니다 `"*"` . 즉, 모든 원본이 허용 됩니다.

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

응답에 헤더가 포함 되지 않은 경우 `Access-Control-Allow-Origin` 원본 간 요청이 실패 합니다. 특히 브라우저에서 요청을 허용 하지 않습니다. 서버에서 성공적인 응답을 반환 하는 경우에도 브라우저에서 응답을 클라이언트 앱에 사용할 수 있도록 설정 하지 않습니다.

<a name="test"></a>

## <a name="test-cors"></a>CORS 테스트

CORS를 테스트 하려면:

1. [API 프로젝트를 만듭니다](xref:tutorials/first-web-api). 또는 [샘플을 다운로드할](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample/Cors)수 있습니다.
1. 이 문서의 방법 중 하나를 사용 하 여 CORS를 사용 하도록 설정 합니다. 예를 들면 다음과 같습니다.

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`[다운로드 샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)와 유사한 샘플 앱을 테스트 하는 데만 사용 해야 합니다.

1. 웹 앱 프로젝트 ( Razor 페이지 또는 MVC)를 만듭니다. 이 샘플에서는 Razor 페이지를 사용 합니다. API 프로젝트와 동일한 솔루션에서 웹 앱을 만들 수 있습니다.
1. 다음 강조 표시 된 코드를 *Index. cshtml* 파일에 추가 합니다.

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. 위의 코드에서를 배포 된 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 앱의 URL로 바꿉니다.
1. API 프로젝트를 배포 합니다. 예를 들어 [Azure에 배포할 수](xref:host-and-deploy/azure-apps/index)있습니다.
1. Razor바탕 화면에서 페이지 또는 MVC 앱을 실행 하 고 **테스트** 단추를 클릭 합니다. F12 도구를 사용 하 여 오류 메시지를 검토 합니다.
1. 에서 localhost 원본을 제거 `WithOrigins` 하 고 앱을 배포 합니다. 또는 다른 포트를 사용 하 여 클라이언트 앱을 실행 합니다. 예를 들어 Visual Studio에서를 실행 합니다.
1. 클라이언트 앱을 사용 하 여 테스트 합니다. CORS 오류는 오류를 반환 하지만 JavaScript에는 오류 메시지를 사용할 수 없습니다. F12 도구에서 콘솔 탭을 사용 하 여 오류를 확인 합니다. 브라우저에 따라 다음과 비슷한 오류가 표시 됩니다 (F12 도구 콘솔).

   * Microsoft Edge 사용:

     **SEC7120: [CORS] 원본에서 원본 `https://localhost:44375` `https://localhost:44375` 간 리소스에 대 한 액세스 제어-원본 응답 헤더를 찾을 수 없습니다. `https://webapi.azurewebsites.net/api/values/1`**

   * Chrome 사용:

     **원본에서의 XMLHttpRequest에 대 한 액세스가 `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` CORS 정책에 의해 차단 되었습니다. 요청 된 리소스에 ' 액세스 제어--원본 ' 헤더가 없습니다.**
     
CORS 사용 끝점은 [Fiddler](https://www.telerik.com/fiddler) 또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 하 여 테스트할 수 있습니다. 도구를 사용 하는 경우 헤더에 의해 지정 된 요청의 원점은 `Origin` 요청을 받는 호스트와 달라 야 합니다. 요청이 헤더의 값을 기반으로 하는 *교차 원본이* 아닌 경우 `Origin` :

* CORS 미들웨어가 요청을 처리할 필요가 없습니다.
* CORS 헤더는 응답에서 반환 되지 않습니다.

## <a name="cors-in-iis"></a>IIS의 CORS

IIS에 배포할 때 서버에서 익명 액세스를 허용 하도록 구성 되지 않은 경우 CORS를 Windows 인증 전에 실행 해야 합니다. 이 시나리오를 지원 하려면 앱에 대해 [IIS CORS 모듈](https://www.iis.net/downloads/microsoft/iis-cors-module) 을 설치 하 고 구성 해야 합니다.

## <a name="additional-resources"></a>추가 리소스

* [CORS (원본 간 리소스 공유)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [IIS CORS 모듈 시작](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
