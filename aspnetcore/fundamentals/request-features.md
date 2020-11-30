---
title: ASP.NET Core의 요청 기능
author: ardalis
description: ASP.NET Core용 인터페이스에 정의된 HTTP 요청 및 응답과 관련된 웹 서버 구현 세부 사항에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: 88e97d88341789a76a79da8d92098c2e00396fe7
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870427"
---
# <a name="request-features-in-aspnet-core"></a>ASP.NET Core의 요청 기능

작성자: [Steve Smith](https://ardalis.com/)

애플리케이션과 미들웨어에서 요청을 처리하는 데 사용하는 `HttpContext` API에는 아래에 ‘기능 인터페이스’라는 추상화 계층이 있습니다. 각 기능 인터페이스는 `HttpContext`에 의해 노출된 기능의 세분화된 하위 집합을 제공합니다. 이러한 인터페이스는 전체 `HttpContext`를 구현하지 않고도 요청이 처리될 때 서버 또는 미들웨어에 의해 추가, 수정, 래핑, 대체 또는 제거될 수 있습니다. 테스트 시 모의 기능을 만드는 데도 사용할 수 있습니다.

## <a name="feature-collections"></a>기능 컬렉션

`HttpContext`의 <xref:Microsoft.AspNetCore.Http.HttpContext.Features> 속성은 현재 요청의 기능 인터페이스 컬렉션에 대한 액세스를 제공합니다. 기능 컬렉션은 요청 컨텍스트 내에서도 변경할 수 있기 때문에, 미들웨어를 사용하여 콜렉션을 수정하고 추가 기능에 대한 지원을 추가할 수 있습니다. 일부 고급 기능은 기능 컬렉션을 통해 연결된 인터페이스에 액세스해야 사용할 수 있습니다.

## <a name="feature-interfaces"></a>기능 인터페이스

ASP.NET Core는 <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>에서 여러 서버와 미들웨어가 지원하는 기능을 식별하기 위해 공유하는 다수의 공통 HTTP 기능 인터페이스를 정의합니다. 서버 및 미들웨어는 추가 기능과 함께 자체 인터페이스를 제공할 수도 있습니다.

대부분의 기능 인터페이스는 선택적인 추가 기능을 제공하고 관련 `HttpContext` API는 기능이 없는 경우 기본값을 제공합니다. 일부 인터페이스는 다음 콘텐츠에 필수로 표시되어 있으며, 이는 이런 인터페이스가 핵심 요청 및 응답 기능을 제공하므로 요청 처리를 위해 구현되어야 하기 때문입니다.

다음 기능 인터페이스는 <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>에서 가져온 것입니다.

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: 프로토콜, 경로, 쿼리 문자열, 헤더 및 본문을 포함하여 HTTP 요청의 구조를 정의합니다. 요청을 처리하려면 이 기능이 필요합니다.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: 상태 코드, 헤더, 응답 본문 등 HTTP 응답의 구조를 정의합니다. 요청을 처리하려면 이 기능이 필요합니다.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: `Stream`, `PipeWriter` 또는 파일을 사용하여 응답 본문을 작성하는 다양한 방법을 정의합니다. 요청을 처리하려면 이 기능이 필요합니다. 이는 `IHttpResponseFeature.Body` 및 `IHttpSendFileFeature`를 대체합니다.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: 현재 요청에 연결되어 있는 <xref:System.Security.Claims.ClaimsPrincipal>을 보류합니다.

<xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: 수신 HTTP 및 다중 파트 양식 제출을 구문 분석하고 캐시하는 데 사용됩니다.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: 요청 또는 응답 본문에 동기 IO 작업이 허용되는지 여부를 제어하는 데 사용됩니다.

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: 요청 및/또는 응답의 버퍼링을 사용하지 않도록 메서드를 정의합니다.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: 연결 ID, 로컬 및 원격 주소와 포트에 대한 속성을 정의합니다.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: 현재 요청에 허용되는 최대 요청 본문 크기를 제어합니다.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

`IHttpRequestBodyDetectionFeature`: 요청에 본문이 포함될 수 있는지 여부를 나타냅니다.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: 요청을 고유하게 식별하기 위해 구현할 수 있는 속성을 추가합니다.

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: 연결을 중단하는 기능 또는 클라이언트 연결 끊김과 같이 요청이 너무 일찍 종료되었는지를 감지하는 기능에 대한 지원을 정의합니다.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: 요청 트레일러 헤더(있는 경우)에 대한 액세스를 제공합니다.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: HTTP/2 또는 HTTP/3과 같이 지원하는 프로토콜에 대해 초기화 메시지를 전송하는 데 사용됩니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: 애플리케이션에서 응답 트레일러 헤더가 지원되는 경우 제공하도록 설정합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: 파일을 비동기적으로 보내는 메서드를 정의합니다.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: 서버가 프로토콜을 전환하려는 경우 사용할 추가 프로토콜을 지정하는 클라이언트를 허용하는 [HTTP 업그레이드](https://tools.ietf.org/html/rfc2616.html#section-14.42)에 대한 지원을 정의합니다.

<xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: 웹 소켓을 지원하기 위한 API를 정의합니다.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: HTTPS 연결을 통해 응답 압축을 사용해야 하는지 여부를 제어합니다.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: 요청별 애플리케이션 상태에 따라 <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> 컬렉션을 저장합니다.

<xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: 쿼리 문자열을 구문 분석하고 캐시합니다.
   
::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: 요청 본문을 <xref:System.IO.Pipelines.PipeReader>로 구문 분석합니다.
 
::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: 요청 `Cookie` 헤더 값을 구문 분석하고 캐시합니다.

<xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: 응답 cookie가 `Set-Cookie` 헤더에 적용되는 방식을 제어합니다.

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: 이 기능은 IIS에서 제공하는 것과 같이 요청 서버 변수에 대한 액세스를 제공합니다.

::: moniker-end
   
<xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: 범위가 지정된 요청 서비스를 통해 <xref:System.IServiceProvider>에 대한 액세스를 제공합니다.

<xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: 사용자 세션을 지원하기 위한 `ISessionFactory` 및 <xref:Microsoft.AspNetCore.Http.ISession> 추상화를 정의합니다. `ISessionFeature`는 <xref:Microsoft.AspNetCore.Session.SessionMiddleware>로 구현됩니다(<xref:fundamentals/app-state> 참조).

<xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: 클라이언트 인증서를 검색하기 위한 API를 정의합니다.

<xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: TLS 토큰 바인딩 매개 변수 작업을 위한 메서드를 정의합니다.
   
::: moniker range=">= aspnetcore-2.2"
   
<xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: 사이트 활동 및 기능과 관련된 사용자 정보의 저장에 대한 사용자 동의를 쿼리, 부여, 철회하는 데 사용됩니다.
   
::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
