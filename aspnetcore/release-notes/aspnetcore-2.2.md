---
title: ASP.NET Core 2.2의 새로운 기능
author: rick-anderson
description: ASP.NET Core 2.2의 새로운 기능에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.2
ms.openlocfilehash: 67581904c686b0f8b6736b751c8e3f1545f6a286
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059704"
---
# <a name="whats-new-in-aspnet-core-22"></a>ASP.NET Core 2.2의 새로운 기능

이 문서에서는 ASP.NET Core 2.2의 가장 큰 변경 내용을 중점적으로 설명하고 관련 문서의 링크를 제공합니다.

## <a name="openapi-analyzers--conventions"></a>OpenAPI 분석기 및 규칙

OpenAPI(이전에 Swagger라고도 함)는 REST API를 설명하는 언어 중립적 사양입니다. OpenAPI 에코시스템에는 사양을 사용하여 클라이언트 코드를 검색, 테스트 및 생성할 수 있는 도구가 있습니다. ASP.NET Core MVC에서 OpenAPI 문서 생성 및 시각화 지원은 [NSwag](https://github.com/RicoSuter/NSwag) 및 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)와 같은 커뮤니티 기반 프로젝트를 통해 제공됩니다. ASP.NET Core 2.2는 OpenAPI 문서를 만들기 위해 향상된 도구 및 런타임 환경을 제공합니다.

자세한 내용은 다음 자료를 참조하세요.

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [ASP.NET Core 2.2.0 미리 보기1: OpenAPI 분석기 및 규칙](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a>문제 세부 정보 지원

ASP.NET Core 2.1은 HTTP 응답과 관련된 오류 세부 정보를 포함하는 [RFC 7807](https://tools.ietf.org/html/rfc7807) 사양에 따라 `ProblemDetails`를 도입했습니다. 2\.2에서 `ProblemDetails`는 `ApiControllerAttribute`로 인한 컨트롤러의 클라이언트 오류 코드의 표준 응답입니다. 클라이언트 오류 상태 코드(4xx)를 반환하는 `IActionResult`는 이제 `ProblemDetails` 본문을 반환합니다. 결과에는 요청 로그를 사용하여 오류를 상관시키는 데 사용할 수 있는 상관 관계 ID도 포함됩니다. 클라이언트 오류의 경우 `ProducesResponseType`은 응답 유형으로 `ProblemDetails` 사용을 기본으로 합니다. 이는 NSwag 또는 Swashbuckle.AspNetCore를 사용하여 생성된 OpenAPI/Swagger 출력에 설명되어 있습니다.

## <a name="endpoint-routing"></a>엔드포인트 라우팅

ASP.NET Core 2.2는 요청 디스패치를 향상시키기 위해 새로운 *엔드포인트 라우팅* 시스템을 사용합니다. 변경 사항에는 새로운 링크 생성 API 멤버 및 경로 매개 변수 변환기가 포함됩니다.

자세한 내용은 다음 자료를 참조하세요.

* [2.2의 엔드포인트 라우팅](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/)
* [경로 매개 변수 변환기](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx)( **라우팅** 섹션 참조)
* [IRouter 기반 라우팅과 엔드포인트 기반 라우팅의 차이점](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a>상태 확인

새로운 상태 검사 서비스를 사용하면 Kubernetes와 같은 상태 검사가 필요한 환경에서 ASP.NET Core를 보다 쉽게 사용할 수 있습니다. 상태 검사에는 미들웨어와 `IHealthCheck` 추상화 및 서비스를 정의하는 라이브러리 세트가 포함됩니다.

상태 검사는 시스템이 요청에 정상적으로 응답하는지 여부를 빠르게 결정하기 위해 컨테이너 오케스트레이터 또는 부하 분산 장치에 의해 사용됩니다. 컨테이너 오케스트레이터는 롤링 배포를 중지하거나 컨테이너를 다시 시작하여 실패한 상태 검사에 응답할 수 있습니다. 부하 분산 장치는 장애가 발생한 서비스 인스턴스로부터 트래픽을 다른 곳으로 라우팅하여 상태 검사에 응답할 수 있습니다.

상태 검사는 애플리케이션에 의해 모니터링 시스템에서 사용되는 HTTP 엔트포인트로서 노출됩니다. 상태 검사는 다양한 실시간 모니터링 시나리오 및 모니터링 시스템에 맞게 구성할 수 있습니다. 상태 검사 서비스는 [BeatPulse 프로젝트](https://github.com/Xabaril/BeatPulse)와 통합됩니다. 수십 개의 인기 있는 시스템 및 종속성에 대한 검사를 쉽게 추가할 수 있습니다.

자세한 내용은 [ASP.NET Core의 상태 검사](xref:host-and-deploy/health-checks)을 참조하세요.

## <a name="http2-in-kestrel"></a>Kestrel의 HTTP/2

ASP.NET Core 2.2는 HTTP/2 지원이 추가되었습니다.

HTTP/2는 HTTP 프로토콜의 주요 수정 버전입니다. HTTP/2의 중요한 기능은 다음과 같습니다.

* 헤더 압축을 지원합니다.
* 단일 연결을 통해 스트림을 완전히 멀티플렉싱합니다.

HTTP/2는 HTTP의 의미 체계(예: HTTP 헤더 및 메서드)를 유지하지만 이 변경으로 클라이언트와 서버 간에 데이터가 프레이밍되고 전송되는 방법에서는 HTTP/1.x와의 호환성이 손상됩니다.

이러한 프레이밍 변경으로 인해 서버와 클라이언트는 사용된 프로토콜 버전을 협상해야 합니다. ALPN(Application-Layer Protocol Negotiation)은 서버와 클라이언트가 TLS 핸드셰이크의 일부로 사용되는 프로토콜 버전을 협상할 수 있게 해주는 TLS 확장입니다. 프로토콜에서 서버와 클라이언트 사이에 사전 지식을 보유할 수는 있지만 모든 주요 브라우저는 HTTP/2 연결을 설정하는 유일한 방법으로 ALPN을 지원합니다

자세한 내용은 [HTTP/2 지원](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support)을 참조하세요.

## <a name="kestrel-configuration"></a>Kestrel 구성

이전 버전의 ASP.NET Core에서는 Kestrel 옵션이 `UseKestrel`을 호출하여 구성되었습니다. 2\.2에서 Kestrel 옵션은 호스트 빌더에서 `ConfigureKestrel`을 호출하여 구성됩니다. 이 변경으로 인해 In-process 호스팅의 `IServer` 등록 순서 문제가 해결됩니다. 자세한 내용은 다음 자료를 참조하세요.

* [UseIIS 충돌 완료](https://github.com/aspnet/KestrelHttpServer/issues/2760)
* [ConfigureKestrel을 사용하여 Kestrel 서버 옵션 구성](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a>IIS In-process 호스팅

이전 버전의 ASP.NET Core에서 IIS는 역방향 프록시 역할을 합니다. 2\.2에서 ASP.NET Core 모듈은 CoreCLR을 부팅하고 IIS 작업자 프로세스( *w3wp.exe* ) 내부에서 앱을 호스팅할 수 있습니다. In-process 호스팅은 IIS로 실행할 때 성능 및 진단 이득을 제공합니다.

자세한 내용은 [IIS에 대한 In-process 호스팅](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model)을 참조하세요.

## <a name="no-locsignalr-java-client"></a>SignalR Java 클라이언트

ASP.NET Core 2.2는 SignalR용 Java 클라이언트를 도입합니다. 이 클라이언트는 Android 앱을 포함하여 Java 코드에서 ASP.NET Core SignalR 서버에 연결할 수 있도록 지원합니다.

자세한 내용은 [ASP.NET Core SignalR Java 클라이언트](../signalr/java-client.md?view=aspnetcore-2.2)를 참조하세요.

## <a name="cors-improvements"></a>CORS 기능 향상

이전 버전의 ASP.NET Core에서 CORS 미들웨어는 `CorsPolicy.Headers`에 구성된 값과 관계없이 `Accept`, `Accept-Language`, `Content-Language` 및 `Origin` 헤더를 보낼 수 있습니다. 2\.2에서 CORS 미들웨어 정책 일치는 `Access-Control-Request-Headers`에서 보낸 헤더가 `WithHeaders`에 명시된 헤더와 정확히 일치할 때만 가능합니다.

자세한 내용은 [CORS 미들웨어](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers)를 참조하세요.

## <a name="response-compression"></a>응답 압축

ASP.NET Core 2.2는 [Brotli 압축 형식](https://tools.ietf.org/html/rfc7932)을 사용하여 응답을 압축할 수 있습니다.

자세한 내용은 [응답 압축 미들웨어가 Brotli 압축 지원](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider)을 참조하세요.

## <a name="project-templates"></a>프로젝트 템플릿

ASP.NET Core 웹 프로젝트 템플릿은 [부트스트랩 4](https://getbootstrap.com/docs/4.1/migration/) 및 [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4)으로 업데이트되었습니다. 새로운 모양은 시각적으로 더 단순하며 앱의 중요한 구조를 더 쉽게 볼 수 있습니다.

![홈 또는 인덱스 페이지](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a>유효성 검사 성능

MVC의 유효성 검사 시스템은 확장 가능하고 유연하게 설계되어 있어 지정된 모델에 적용되는 유효성 검사기를 요청에 따라 결정할 수 있습니다. 이는 복잡한 유효성 검사 공급자를 작성하는 데 적합합니다. 그러나 가장 일반적인 경우 애플리케이션은 기본 제공된 유효성 검사기만 사용하므로 이러한 유연성이 추가로 필요하지 않습니다. 기본 제공된 유효성 검사기에는 [Required], [StringLength] 및 `IValidatableObject` 등과 같은 DataAnnotations가 있습니다.

ASP.NET Core 2.2에서 MVC는 지정된 모델 그래프가 유효성 검사를 필요로 하지 않다고 판단하면 유효성 검사를 단락합니다. 유효성 검사를 건너뛰는 것은 유효성 검사기를 가질 수 없거나 없는 모델의 유효성을 검사할 때 상당한 개선을 가져옵니다. 여기에는 기본 형식(`byte[]`, `string[]`, `Dictionary<string, string>` 등)의 컬렉션과 같은 개체 또는 많은 유효성 검사기가 없는 복합 개체 그래프가 있습니다.

## <a name="http-client-performance"></a>HTTP 클라이언트 성능

ASP.NET Core 2.2에서는 연결 풀 잠금 경합을 줄임으로써 `SocketsHttpHandler`의 성능이 향상되었습니다. 일부 마이크로 서비스 아키텍처와 같이 많은 HTTP 요청을 보내는 앱의 경우 처리량이 향상됩니다. 부하가 있는 상태에서 `HttpClient` 처리량은 Linux의 경우 최대 60%까지, Windows의 경우 최대 20%까지 향상시킬 수 있습니다.

자세한 내용은 [이를 개선하는 끌어오기 요청](https://github.com/dotnet/corefx/pull/32568)을 참조하세요.

## <a name="additional-information"></a>추가 정보

전체 변경 내용 목록을 보려면 [ASP.NET Core 2.2 릴리스 정보](https://github.com/dotnet/aspnetcore/releases/tag/2.2.0)를 참조하세요.