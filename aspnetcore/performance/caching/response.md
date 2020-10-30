---
title: ASP.NET Core의 응답 캐싱
author: rick-anderson
description: 응답 캐싱을 사용하여 ASP.NET Core 앱의 성능을 향상하고 대역폭 요구 사항을 낮추는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
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
uid: performance/caching/response
ms.openlocfilehash: 2864de5b9931ed255569cb087c67c71004c4df92
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059015"
---
# <a name="response-caching-in-aspnet-core"></a>ASP.NET Core의 응답 캐싱

작성자, [John 루 오 어](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)및 [Steve Smith](https://ardalis.com/)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

응답 캐싱은 클라이언트 또는 프록시가 웹 서버에 대해 수행 하는 요청 수를 줄입니다. 응답 캐싱은 웹 서버에서 응답을 생성 하기 위해 수행 하는 작업의 양을 줄입니다. 응답 캐싱은 클라이언트, 프록시 및 미들웨어에서 응답을 캐시 하는 방법을 지정 하는 헤더에 의해 제어 됩니다.

[ResponseCache 특성](#responsecache-attribute) 은 응답 캐싱 헤더 설정에 참여 합니다. 클라이언트 및 중간 프록시는 [HTTP 1.1 캐싱 사양](https://tools.ietf.org/html/rfc7234)에 따라 응답을 캐시 하는 데 헤더를 적용 해야 합니다.

HTTP 1.1 캐싱 사양을 따르는 서버 쪽 캐싱의 경우 [응답 캐싱 미들웨어](xref:performance/caching/middleware)를 사용 합니다. 미들웨어는 속성을 사용 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 하 여 서버 쪽 캐싱 동작에 영향을 줄 수 있습니다.

## <a name="http-based-response-caching"></a>HTTP 기반 응답 캐싱

[HTTP 1.1 캐싱 사양은](https://tools.ietf.org/html/rfc7234) 인터넷 캐시가 동작 하는 방식을 설명 합니다. 캐싱에 사용 되는 기본 HTTP 헤더는 캐시 *지시문* 을 지정 하는 데 사용 되는 [cache-control](https://tools.ietf.org/html/rfc7234#section-5.2)입니다. 지시문은 요청이 클라이언트에서 서버로 이동 하 고 응답을 통해 서버에서 클라이언트로 다시 이동 하는 방식으로 캐싱 동작을 제어 합니다. 요청 및 응답은 프록시 서버를 통해 이동 하 고, 프록시 서버는 HTTP 1.1 캐싱 사양을 준수 해야 합니다.

`Cache-Control`다음 표에서는 일반적인 지시문을 보여 줍니다.

| 지시문                                                       | 작업 |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | 캐시에서 응답을 저장할 수 있습니다. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | 응답은 공유 캐시에 저장 되지 않아야 합니다. 개인 캐시는 응답을 저장 하 고 다시 사용할 수 있습니다. |
| [최대 사용 기간](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | 클라이언트는 나이가 지정 된 시간 (초) 보다 큰 응답을 수락 하지 않습니다. 예: `max-age=60` (60 초), `max-age=2592000` (1 개월) |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **요청 시** : 캐시가 저장 된 응답을 사용 하 여 요청을 충족 해서는 안 됩니다. 원본 서버는 클라이언트에 대 한 응답을 다시 생성 하 고 미들웨어는 캐시에 저장 된 응답을 업데이트 합니다.<br><br>**응답 시** : 원본 서버에서 유효성 검사 없이 후속 요청에 대 한 응답을 사용 하지 않아야 합니다. |
| [저장소 없음](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **요청 시** : 캐시에서 요청을 저장 하지 않아야 합니다.<br><br>**응답 시** : 캐시가 응답의 일부를 저장 하지 않아야 합니다. |

캐시에서 역할을 수행 하는 다른 캐시 헤더는 다음 표에 나와 있습니다.

| 헤더                                                     | 함수 |
| ---------------------------------------------------------- | -------- |
| [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | 응답을 생성 하거나 원본 서버에서 유효성을 검사 한 이후의 시간 (초)입니다. |
| [기간이](https://tools.ietf.org/html/rfc7234#section-5.3) | 응답이 오래 된 것으로 간주 되는 시간입니다. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | 동작 설정에 대 한 HTTP/1.0 캐시와의 이전 버전과의 호환성을 위해 존재 `no-cache` 합니다. 헤더가 있으면 `Cache-Control` `Pragma` 헤더가 무시 됩니다. |
| [날](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | 캐시 된 응답의 `Vary` 원래 요청과 새 요청 모두에서 헤더 필드가 모두 일치 하지 않는 경우 캐시 된 응답을 보내지 않도록 지정 합니다. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>HTTP 기반 캐싱은 요청 Cache-Control 지시문을 사용 합니다.

[Cache-Control 헤더에 대 한 HTTP 1.1 캐싱 지정](https://tools.ietf.org/html/rfc7234#section-5.2) 에는 클라이언트가 보낸 유효한 헤더를 인식 하기 위해 캐시가 필요 합니다 `Cache-Control` . 클라이언트는 헤더 값을 사용 하 여 요청을 수행 하 `no-cache` 고 서버에서 모든 요청에 대 한 새 응답을 생성 하도록 강제할 수 있습니다.

`Cache-Control`HTTP 캐싱의 목표를 고려 하는 경우 항상 클라이언트 요청 헤더를 파악 하는 것이 좋습니다. 공식 사양에서 캐싱은 클라이언트, 프록시 및 서버 네트워크에서 요청을 충족 하는 대기 시간 및 네트워크 오버 헤드를 줄이기 위한 것입니다. 원본 서버에서 로드를 제어 하는 방법은 아닙니다.

미들웨어가 공식 캐싱 사양을 준수 하기 때문에 [응답 캐싱 미들웨어](xref:performance/caching/middleware) 를 사용 하는 경우이 캐싱 동작에 대 한 개발자 제어는 없습니다. [미들웨어에 대해 계획 된 향상 된 기능은](https://github.com/dotnet/AspNetCore/issues/2612) 캐시 된 응답을 제공 하도록 결정할 때 요청의 헤더를 무시 하도록 미들웨어를 구성할 수 있는 기회 `Cache-Control` 입니다. 계획 된 향상 된 기능을 통해 서버 부하를 보다 효과적으로 제어할 수 있습니다.

## <a name="other-caching-technology-in-aspnet-core"></a>ASP.NET Core의 기타 캐싱 기술

### <a name="in-memory-caching"></a>메모리 내 캐싱

메모리 내 캐싱은 서버 메모리를 사용 하 여 캐시 된 데이터를 저장 합니다. 이 유형의 캐싱은 단일 서버 또는 *고정 세션* 을 사용 하는 여러 서버에 적합 합니다. 고정 세션은 클라이언트의 요청이 처리를 위해 항상 동일한 서버로 라우팅되는 것을 의미 합니다.

자세한 내용은 <xref:performance/caching/memory>를 참조하세요.

### <a name="distributed-cache"></a>분산 캐시

앱이 클라우드 또는 서버 팜에서 호스팅될 때 분산 캐시를 사용 하 여 메모리에 데이터를 저장 합니다. 캐시는 요청을 처리 하는 서버에서 공유 됩니다. 클라이언트에 대해 캐시 된 데이터를 사용할 수 있는 경우 클라이언트는 그룹의 모든 서버에서 처리 한 요청을 제출할 수 있습니다. ASP.NET Core는 SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)및 [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) 분산 캐시와 함께 작동 합니다.

자세한 내용은 <xref:performance/caching/distributed>를 참조하세요.

### <a name="cache-tag-helper"></a>캐시 태그 도우미

캐시 태그 도우미를 사용 하 여 MVC 뷰 또는 페이지에서 콘텐츠를 캐시 합니다 Razor . 캐시 태그 도우미는 메모리 내 캐싱을 사용 하 여 데이터를 저장 합니다.

자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>를 참조하세요.

### <a name="distributed-cache-tag-helper"></a>분산 캐시 태그 도우미

분산 된 Razor 캐시 태그 도우미를 사용 하 여 분산 클라우드 또는 웹 팜 시나리오의 MVC 뷰나 페이지에서 콘텐츠를 캐시 합니다. 분산 캐시 태그 도우미는 SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)또는 [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) 를 사용 하 여 데이터를 저장 합니다.

자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>를 참조하세요.

## <a name="responsecache-attribute"></a>ResponseCache 특성

는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 응답 캐싱에 적절 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.

> [!WARNING]
> 인증 된 클라이언트에 대 한 정보가 포함 된 콘텐츠에 대해 캐싱을 사용 하지 않도록 설정 합니다. 사용자의 id에 따라 변경 되지 않는 콘텐츠나 사용자의 로그인 여부에 따라 캐싱을 사용 하도록 설정 해야 합니다.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 지정 된 쿼리 키 목록 값에 따라 저장 된 응답을 변경 합니다. 의 단일 값 `*` 이 제공 되 면 미들웨어는 모든 요청 쿼리 문자열 매개 변수의 응답을 변경 합니다.

속성을 설정 하려면 [응답 캐싱 미들웨어](xref:performance/caching/middleware) 를 사용 하도록 설정 해야 합니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> . 그렇지 않으면 런타임 예외가 throw 됩니다. 속성에 해당 하는 HTTP 헤더가 없습니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> . 속성은 응답 캐싱 미들웨어에 의해 처리 되는 HTTP 기능입니다. 미들웨어가 캐시 된 응답을 제공 하려면 쿼리 문자열 및 쿼리 문자열 값이 이전 요청과 일치 해야 합니다. 예를 들어 다음 표에 표시 된 요청 및 결과의 순서를 고려 합니다.

| 요청                          | 결과                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | 서버에서 반환 됩니다. |
| `http://example.com?key1=value1` | 미들웨어에서 반환 됩니다. |
| `http://example.com?key1=value2` | 서버에서 반환 됩니다. |

첫 번째 요청은 서버에서 반환 되 고 미들웨어에 캐시 됩니다. 쿼리 문자열이 이전 요청과 일치 하기 때문에 두 번째 요청은 미들웨어에서 반환 됩니다. 쿼리 문자열 값이 이전 요청과 일치 하지 않으므로 세 번째 요청이 미들웨어 캐시에 없습니다.

는를 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 통해를 구성 하 고 만드는 데 사용 됩니다 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` . 는 `ResponseCacheFilter` 응답의 적절 한 HTTP 헤더 및 기능을 업데이트 하는 작업을 수행 합니다. 필터:

* , 및에 대 한 기존 헤더 `Vary` 를 제거 `Cache-Control` `Pragma` 합니다.
* 에 설정 된 속성을 기반으로 적절 한 헤더를 작성 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 합니다.
* 가 설정 된 경우 응답 캐싱 HTTP 기능을 업데이트 합니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> .

### <a name="vary"></a>날

이 헤더는 속성이 설정 된 경우에만 기록 됩니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> . 속성 값으로 설정 된 속성 `Vary` 입니다. 다음 샘플에서는 속성을 사용 합니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> .

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

샘플 앱을 사용 하 여 브라우저의 네트워크 도구를 사용 하 여 응답 헤더를 확인 합니다. 다음 응답 헤더는 Cache1 페이지 응답과 함께 전송 됩니다.

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>NoStore 및 위치입니다. 없음

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 는 대부분의 다른 속성을 재정의 합니다. 이 속성이로 설정 되 면 `true` `Cache-Control` 헤더가로 설정 됩니다 `no-store` . <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>가로 설정 된 `None` 경우

* `Cache-Control`이 `no-store,no-cache`로 설정됩니다.
* `Pragma`이 `no-cache`로 설정됩니다.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>가이 `false` 고 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 가 `None` , `Cache-Control` 및 이면가 `Pragma` 로 설정 됩니다 `no-cache` .

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 는 일반적으로 `true` 오류 페이지에 대해로 설정 됩니다. 샘플 앱의 Cache2 페이지는 클라이언트에 응답을 저장 하지 않도록 지시 하는 응답 헤더를 생성 합니다.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

샘플 앱은 다음 헤더를 포함 하는 Cache2 페이지를 반환 합니다.

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>위치 및 기간

캐싱을 사용 하도록 설정 하려면를 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 양수 값으로 설정 하 고 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> `Any` (기본값) 또는 중 하나 여야 합니다 `Client` . 프레임 워크는 `Cache-Control` 헤더를 위치 값으로 설정 하 고 그 뒤에 응답의을 설정 합니다 `max-age` .

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>및의 옵션 `Any` 은 `Client` `Cache-Control` 각각 및의 헤더 값으로 변환 `public` `private` 됩니다. [Nostore 및 Location. None](#nostore-and-locationnone) 섹션에서 설명한 대로 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 을로 설정 하면 `None` `Cache-Control` 및 헤더가 모두 `Pragma` 로 설정 `no-cache` 됩니다.

`Location.Any` ( `Cache-Control` 로 설정 `public` )은 *클라이언트 또는 중간 프록시가* [응답 캐싱 미들웨어](xref:performance/caching/middleware)를 포함 하 여 값을 캐시할 수 있음을 나타냅니다.

`Location.Client` ( `Cache-Control` 로 설정 `private` )는 *클라이언트만* 값을 캐시할 수 있음을 나타냅니다. [응답 캐싱 미들웨어](xref:performance/caching/middleware)를 포함 하 여 값을 캐시 해야 하는 중간 캐시는 없습니다.

캐시 컨트롤 헤더는 응답을 캐시 하는 방법 및 방법에 대 한 지침을 클라이언트 및 중간 프록시에 제공 합니다. 클라이언트 및 프록시가 [HTTP 1.1 캐싱 사양을](https://tools.ietf.org/html/rfc7234)인식 한다는 보장이 없습니다. [응답 캐싱 미들웨어](xref:performance/caching/middleware) 는 항상 사양에 의해 배치 된 캐싱 규칙을 따릅니다.

다음 예제에서는 샘플 앱의 Cache3 페이지 모델 및 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 기본값을 설정 하 고 종료 하 여 생성 된 헤더를 보여 줍니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> .

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

샘플 앱은 다음 헤더를 포함 하는 Cache3 페이지를 반환 합니다.

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>캐시 프로필

여러 컨트롤러 작업 특성에 대 한 응답 캐시 설정을 복제 하는 대신에서 MVC/Pages를 설정할 때 캐시 프로필을 옵션으로 구성할 수 있습니다 Razor `Startup.ConfigureServices` . 참조 된 캐시 프로필에 있는 값은에서 기본값으로 사용 되며 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 특성에 지정 된 모든 속성에 의해 재정의 됩니다.

캐시 프로필을 설정 합니다. 다음 예제에서는 샘플 앱의 30 초 캐시 프로필을 보여 줍니다 `Startup.ConfigureServices` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response/samples/3.x/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

샘플 앱의 Cache4 페이지 모델은 캐시 프로필을 참조 합니다 `Default30` .

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 다음에 적용할 수 있습니다.

* Razor Pages: 특성은 처리기 메서드에 적용할 수 없습니다.
* MVC 컨트롤러.
* MVC 작업 메서드: 메서드 수준 특성은 클래스 수준 특성에 지정 된 설정을 재정의 합니다.

캐시 프로필에의 한 Cache4 페이지 응답에 적용 되는 결과 헤더는 `Default30` 다음과 같습니다.

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>추가 리소스

* [캐시에 응답 저장](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
