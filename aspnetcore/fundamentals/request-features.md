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
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="b61fb-103">ASP.NET Core의 요청 기능</span><span class="sxs-lookup"><span data-stu-id="b61fb-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="b61fb-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b61fb-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b61fb-105">애플리케이션과 미들웨어에서 요청을 처리하는 데 사용하는 `HttpContext` API에는 아래에 ‘기능 인터페이스’라는 추상화 계층이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-105">The `HttpContext` API that applications and middleware use to process requests has an abstraction layer underneath it called *feature interfaces*.</span></span> <span data-ttu-id="b61fb-106">각 기능 인터페이스는 `HttpContext`에 의해 노출된 기능의 세분화된 하위 집합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-106">Each feature interface provides a granular subset of the functionality exposed by `HttpContext`.</span></span> <span data-ttu-id="b61fb-107">이러한 인터페이스는 전체 `HttpContext`를 구현하지 않고도 요청이 처리될 때 서버 또는 미들웨어에 의해 추가, 수정, 래핑, 대체 또는 제거될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-107">These interfaces can be added, modified, wrapped, replaced, or even removed by the server or middleware as the request is processed without having to re-implement the entire `HttpContext`.</span></span> <span data-ttu-id="b61fb-108">테스트 시 모의 기능을 만드는 데도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-108">They can also be used to mock functionality when testing.</span></span>

## <a name="feature-collections"></a><span data-ttu-id="b61fb-109">기능 컬렉션</span><span class="sxs-lookup"><span data-stu-id="b61fb-109">Feature collections</span></span>

<span data-ttu-id="b61fb-110">`HttpContext`의 <xref:Microsoft.AspNetCore.Http.HttpContext.Features> 속성은 현재 요청의 기능 인터페이스 컬렉션에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-110">The <xref:Microsoft.AspNetCore.Http.HttpContext.Features> property of `HttpContext` provides access to the collection of feature interfaces for the current request.</span></span> <span data-ttu-id="b61fb-111">기능 컬렉션은 요청 컨텍스트 내에서도 변경할 수 있기 때문에, 미들웨어를 사용하여 콜렉션을 수정하고 추가 기능에 대한 지원을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-111">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span> <span data-ttu-id="b61fb-112">일부 고급 기능은 기능 컬렉션을 통해 연결된 인터페이스에 액세스해야 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-112">Some advanced features are only available by accessing the associated interface through the feature collection.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="b61fb-113">기능 인터페이스</span><span class="sxs-lookup"><span data-stu-id="b61fb-113">Feature interfaces</span></span>

<span data-ttu-id="b61fb-114">ASP.NET Core는 <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>에서 여러 서버와 미들웨어가 지원하는 기능을 식별하기 위해 공유하는 다수의 공통 HTTP 기능 인터페이스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-114">ASP.NET Core defines a number of common HTTP feature interfaces in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, which are shared by various servers and middleware to identify the features that they support.</span></span> <span data-ttu-id="b61fb-115">서버 및 미들웨어는 추가 기능과 함께 자체 인터페이스를 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-115">Servers and middleware may also provide their own interfaces with additional functionality.</span></span>

<span data-ttu-id="b61fb-116">대부분의 기능 인터페이스는 선택적인 추가 기능을 제공하고 관련 `HttpContext` API는 기능이 없는 경우 기본값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-116">Most feature interfaces provide optional, light-up functionality, and their associated `HttpContext` APIs provide defaults if the feature isn't present.</span></span> <span data-ttu-id="b61fb-117">일부 인터페이스는 다음 콘텐츠에 필수로 표시되어 있으며, 이는 이런 인터페이스가 핵심 요청 및 응답 기능을 제공하므로 요청 처리를 위해 구현되어야 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-117">A few interfaces are indicated in the following content as required because they provide core request and response functionality and must be implemented in order to process the request.</span></span>

<span data-ttu-id="b61fb-118">다음 기능 인터페이스는 <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>에서 가져온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-118">The following feature interfaces are from <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span></span>

<span data-ttu-id="b61fb-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: 프로토콜, 경로, 쿼리 문자열, 헤더 및 본문을 포함하여 HTTP 요청의 구조를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span> <span data-ttu-id="b61fb-120">요청을 처리하려면 이 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-120">This feature is required in order to process requests.</span></span>

<span data-ttu-id="b61fb-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: 상태 코드, 헤더, 응답 본문 등 HTTP 응답의 구조를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span> <span data-ttu-id="b61fb-122">요청을 처리하려면 이 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-122">This feature is required in order to process requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b61fb-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: `Stream`, `PipeWriter` 또는 파일을 사용하여 응답 본문을 작성하는 다양한 방법을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Defines different ways of writing out the response body, using either a `Stream`, a `PipeWriter`, or a file.</span></span> <span data-ttu-id="b61fb-124">요청을 처리하려면 이 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-124">This feature is required in order to process requests.</span></span> <span data-ttu-id="b61fb-125">이는 `IHttpResponseFeature.Body` 및 `IHttpSendFileFeature`를 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-125">This replaces `IHttpResponseFeature.Body` and `IHttpSendFileFeature`.</span></span>

::: moniker-end

<span data-ttu-id="b61fb-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: 현재 요청에 연결되어 있는 <xref:System.Security.Claims.ClaimsPrincipal>을 보류합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Holds the <xref:System.Security.Claims.ClaimsPrincipal> currently associated with the request.</span></span>

<span data-ttu-id="b61fb-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: 수신 HTTP 및 다중 파트 양식 제출을 구문 분석하고 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Used to parse and cache incoming HTTP and multipart form submissions.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b61fb-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: 요청 또는 응답 본문에 동기 IO 작업이 허용되는지 여부를 제어하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Used to control if synchronous IO operations are allowed for the request or response bodies.</span></span>

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b61fb-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: 요청 및/또는 응답의 버퍼링을 사용하지 않도록 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Defines methods for disabling buffering of requests and/or responses.</span></span>

::: moniker-end

<span data-ttu-id="b61fb-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: 연결 ID, 로컬 및 원격 주소와 포트에 대한 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Defines properties for the connection id and local and remote addresses and ports.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b61fb-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: 현재 요청에 허용되는 최대 요청 본문 크기를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controls the maximum allowed request body size for the current request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b61fb-132">`IHttpRequestBodyDetectionFeature`: 요청에 본문이 포함될 수 있는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-132">`IHttpRequestBodyDetectionFeature`: Indicates if the request can have a body.</span></span>

::: moniker-end

<span data-ttu-id="b61fb-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: 요청을 고유하게 식별하기 위해 구현할 수 있는 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="b61fb-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: 연결을 중단하는 기능 또는 클라이언트 연결 끊김과 같이 요청이 너무 일찍 종료되었는지를 감지하는 기능에 대한 지원을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Defines support for aborting connections or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b61fb-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: 요청 트레일러 헤더(있는 경우)에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Provides access to the request trailer headers, if any.</span></span>

<span data-ttu-id="b61fb-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: HTTP/2 또는 HTTP/3과 같이 지원하는 프로토콜에 대해 초기화 메시지를 전송하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Used to send reset messages for protocols that support them such as HTTP/2 or HTTP/3.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b61fb-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: 애플리케이션에서 응답 트레일러 헤더가 지원되는 경우 제공하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Enables the application to provide response trailer headers if supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b61fb-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: 파일을 비동기적으로 보내는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Defines a method for sending files asynchronously.</span></span>

::: moniker-end

<span data-ttu-id="b61fb-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: 서버가 프로토콜을 전환하려는 경우 사용할 추가 프로토콜을 지정하는 클라이언트를 허용하는 [HTTP 업그레이드](https://tools.ietf.org/html/rfc2616.html#section-14.42)에 대한 지원을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="b61fb-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: 웹 소켓을 지원하기 위한 API를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Defines an API for supporting web sockets.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b61fb-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: HTTPS 연결을 통해 응답 압축을 사용해야 하는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controls if response compression should be used over HTTPS connections.</span></span>

::: moniker-end

<span data-ttu-id="b61fb-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: 요청별 애플리케이션 상태에 따라 <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> 컬렉션을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stores the <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection for per request application state.</span></span>

<span data-ttu-id="b61fb-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: 쿼리 문자열을 구문 분석하고 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Parses and caches the query string.</span></span>
   
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b61fb-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: 요청 본문을 <xref:System.IO.Pipelines.PipeReader>로 구문 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Represents the request body as a <xref:System.IO.Pipelines.PipeReader>.</span></span>
 
::: moniker-end

<span data-ttu-id="b61fb-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: 요청 `Cookie` 헤더 값을 구문 분석하고 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Parses and caches the request `Cookie` header values.</span></span>

<span data-ttu-id="b61fb-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: 응답 cookie가 `Set-Cookie` 헤더에 적용되는 방식을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controls how response cookies are applied to the `Set-Cookie` header.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b61fb-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: 이 기능은 IIS에서 제공하는 것과 같이 요청 서버 변수에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: This feature provides access to request server variables such as those provided by IIS.</span></span>

::: moniker-end
   
<span data-ttu-id="b61fb-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: 범위가 지정된 요청 서비스를 통해 <xref:System.IServiceProvider>에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Provides access to an <xref:System.IServiceProvider> with scoped request services.</span></span>

<span data-ttu-id="b61fb-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: 사용자 세션을 지원하기 위한 `ISessionFactory` 및 <xref:Microsoft.AspNetCore.Http.ISession> 추상화를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Defines `ISessionFactory` and <xref:Microsoft.AspNetCore.Http.ISession> abstractions for supporting user sessions.</span></span> <span data-ttu-id="b61fb-150">`ISessionFeature`는 <xref:Microsoft.AspNetCore.Session.SessionMiddleware>로 구현됩니다(<xref:fundamentals/app-state> 참조).</span><span class="sxs-lookup"><span data-stu-id="b61fb-150">`ISessionFeature` is implemented by the <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (see <xref:fundamentals/app-state>).</span></span>

<span data-ttu-id="b61fb-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: 클라이언트 인증서를 검색하기 위한 API를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="b61fb-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: TLS 토큰 바인딩 매개 변수 작업을 위한 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Defines methods for working with TLS token binding parameters.</span></span>
   
::: moniker range=">= aspnetcore-2.2"
   
<span data-ttu-id="b61fb-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: 사이트 활동 및 기능과 관련된 사용자 정보의 저장에 대한 사용자 동의를 쿼리, 부여, 철회하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b61fb-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Used to query, grant, and withdraw user consent regarding the storage of user information related to site activity and functionality.</span></span>
   
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b61fb-154">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="b61fb-154">Additional resources</span></span>

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
