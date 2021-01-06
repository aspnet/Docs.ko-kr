---
title: ASP.NET Core의 세션
author: rick-anderson
description: 요청 간에 세션을 유지하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
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
uid: fundamentals/app-state
ms.openlocfilehash: c11b748f9d79235b14c9541019da6e1fb3428af6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93051410"
---
# <a name="session-and-state-management-in-aspnet-core"></a>ASP.NET Core의 세션 및 상태 관리

::: moniker range=">= aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Diana LaRose](https://github.com/DianaLaRose)

HTTP는 상태 비저장 프로토콜입니다. 기본적으로 HTTP 요청은 사용자 값을 유지하지 않는 독립적인 메시지입니다. 이 문서에서는 요청 간에 사용자 데이터를 유지하는 여러 가지 방법을 설명합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>상태 관리

상태는 여러 방법을 사용하여 저장할 수 있습니다. 각 방법은 이 항목 뒷부분에서 설명합니다.

| 스토리지 접근 방식 | 스토리지 메커니즘 |
| ---------------- | ----------------- |
| [Cookie](#cookies) | HTTP cookie. 서버 쪽 앱 코드를 사용하여 저장된 데이터를 포함할 수 있습니다. |
| [세션 상태](#session-state) | HTTP cookie 및 서버 쪽 앱 코드 |
| [TempData](#tempdata) | HTTP cookie 또는 세션 상태 |
| [쿼리 문자열](#query-strings) | HTTP 쿼리 문자열 |
| [숨겨진 필드](#hidden-fields) | HTTP 양식 필드 |
| [HttpContext.Items](#httpcontextitems) | 서버 쪽 앱 코드 |
| [캐시](#cache) | 서버 쪽 앱 코드 |

## <a name="no-loccookies"></a>Cookies

Cookie는 요청 간에 데이터를 저장합니다. cookie는 모든 요청과 함께 전송되므로 해당 크기는 최소로 유지되어야 합니다. 이상적으로 식별자만 앱에 저장된 데이터와 함께 cookie에 저장되어야 합니다. 대부분의 브라우저는 cookie 크기를 4,096바이트로 제한합니다. 제한된 수의 cookie만 각 도메인에 사용할 수 있습니다.

cookie는 변조될 수 있기 때문에 앱에서 유효성을 검사해야 합니다. Cookie는 사용자가 삭제할 수 있으며 클라이언트에서 만료됩니다. 그러나 cookie는 일반적으로 클라이언트에서 데이터 지속성의 가장 안정적인 형태입니다.

Cookie는 종종 개인 설정에 사용됩니다. 여기서 콘텐츠는 알려진 사용자에 대해 사용자 지정됩니다. 사용자만 식별되고 대부분의 경우 인증되지 않습니다. cookie는 사용자 이름, 계정 이름 또는 고유한 사용자 ID(예: GUID)를 저장할 수 있습니다. cookie를 사용하여 선호하는 웹 사이트 배경색과 같은 사용자의 맞춤형 설정에 액세스할 수 있습니다.

cookie를 발급하고 개인 정보 보호 문제를 처리하는 경우 [유럽 연합 GDPR](https://ec.europa.eu/info/law/law-topic/data-protection)(일반 데이터 보호 규정)을 참조하세요. 자세한 내용은 [ASP.NET Core의 GDPR(일반 데이터 보호 규정) 지원](xref:security/gdpr)을 참조하세요.

## <a name="session-state"></a>세션 상태

세션 상태는 사용자가 웹앱을 탐색하는 동안 사용자 데이터를 스토리지하기 위한 ASP.NET Core 시나리오입니다. 세션 상태는 앱에서 유지 관리하는 저장소를 사용하여 클라이언트의 요청 간에 데이터를 유지합니다. 세션 데이터는 캐시에 백업되며 임시 데이터로 간주됩니다. 세션 데이터 없이도 사이트가 계속 작동해야 합니다. 중요한 애플리케이션 데이터는 사용자 데이터베이스에 저장되고 성능 최적화로 세션에 캐시되어야 합니다.

[SignalR 허브](xref:signalr/hubs)가 HTTP 컨텍스트와 독립적으로 실행될 수 있으므로, 세션은 [SignalR](xref:signalr/index) 앱에서 지원되지 않습니다. 예를 들어, 허브에서 긴 폴링 요청이 HTTP 컨텍스트 수명을 초과하여 계속 열려 있을 경우 이 문제가 발생할 수 있습니다.

ASP.NET Core는 세션 ID를 포함하는 cookie를 클라이언트에 제공하여 세션 상태를 유지 관리합니다. cookie 세션 ID:

* 각 요청과 함께 앱에 전송됩니다.
* 앱에서 세션 데이터를 가져오는 데 사용됩니다.

세션 상태는 다음과 같은 동작을 보여 줍니다.

* 세션 cookie는 브라우저와 관련이 있습니다. 세션은 브라우저 간에 공유되지 않습니다.
* 세션 cookie는 브라우저 세션이 끝나면 삭제됩니다.
* cookie가 만료된 세션에 대해 수신되면 동일한 세션 cookie를 사용하는 새 세션이 생성됩니다.
* 빈 세션은 유지되지 않습니다. 요청 간에 세션을 유지하려면 세션에 하나 이상의 값이 설정되어 있어야 합니다. 세션이 유지되지 않으면 새 요청마다 새 세션 ID가 생성됩니다.
* 앱은 마지막 요청 이후 제한된 시간 동안 세션을 유지합니다. 앱은 세션 시간 제한을 설정하거나 20분의 기본값을 사용합니다. 세션 상태는 다음 조건을 충족하는 사용자 데이터를 저장하는 데 적합합니다.
  * 특정 세션과 관련이 있습니다.
  * 세션 간에 데이터의 영구 스토리지가 필요하지 않습니다.
* 세션 데이터는 <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> 구현이 호출되거나 세션이 만료될 때 삭제됩니다.
* 클라이언트 브라우저가 닫혔거나 세션 cookie가 삭제 또는 클라이언트에서 만료되었을 때 앱 코드에 이를 알려주는 기본 메커니즘은 없습니다.
* 기본적으로 세션 상태 cookie는 필수로 표시되어 있지 않습니다. 사이트 방문자가 추적을 허용하지 않는 한, 세션 상태는 작동하지 않습니다. 자세한 내용은 <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>를 참조하세요.

> [!WARNING]
> 중요한 데이터를 세션 상태에 저장하지 마세요. 사용자는 브라우저를 닫지 않고 세션 cookie를 지울 수 있습니다. 일부 브라우저는 브라우저 창이 닫혀도 유효한 세션 cookie를 유지 관리합니다. 세션을 단일 사용자로 제한하지 못할 수도 있습니다. 다음 사용자가 동일한 세션 cookie로 앱을 계속 검색할 수 있습니다.

메모리 내 캐시 공급자는 앱이 있는 서버의 메모리에 세션 데이터를 저장합니다. 서버 팜 시나리오:

* *고정 세션* 을 사용하여 각 세션을 개별 서버의 특정 앱 인스턴스에 연결합니다. [Azure App Service](https://azure.microsoft.com/services/app-service/)는 [ARR(애플리케이션 요청 라우팅)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)을 사용하여 기본적으로 고정 세션을 적용합니다. 그러나 고정 세션은 확장성에 영향을 주고 웹앱 업데이트를 복잡하게 만들 수 있습니다. 더 나은 방법은 고정 세션이 필요 없는 Redis 또는 SQL Server 분산 캐시를 사용하는 것입니다. 자세한 내용은 <xref:performance/caching/distributed>를 참조하세요.
* 세션 cookie는 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>를 통해 암호화됩니다. 데이터 보호는 각 머신에서 세션 cookie를 읽을 수 있도록 올바르게 구성되어야 합니다. 자세한 내용은 <xref:security/data-protection/introduction> 및 [키 스토리지 공급자](xref:security/data-protection/implementation/key-storage-providers)를 참조하세요.

### <a name="configure-session-state"></a>세션 상태 구성

[Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 패키지와 관련해서 다음 사항을 확인합니다.

* 프레임워크를 통해 암시적으로 포함됩니다.
* 세션 상태를 관리하기 위한 미들웨어를 제공합니다.

세션 미들웨어를 활성화하려면 `Startup`은 다음을 포함해야 합니다.

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리 캐시 중 하나. `IDistributedCache` 구현은 세션에 대한 백업 저장소로 사용됩니다. 자세한 내용은 <xref:performance/caching/distributed>를 참조하세요.
* `ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A>에 대한 호출.
* `Configure`의 <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>에 대한 호출.

다음 코드에서는 `IDistributedCache`의 기본 메모리 내 구현을 사용하여 메모리 내 세션 공급자를 설정하는 방법을 보여 줍니다.

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

위의 코드는 테스트를 간소화하기 위해 짧은 시간 제한을 설정합니다.

미들웨어의 순서가 중요합니다.  `UseRouting` 이후, `UseEndpoints` 이전에 `UseSession`을 호출합니다. [미들웨어 순서](xref:fundamentals/middleware/index#order)를 참조하세요.

[HttpContext.Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session)은 세션 상태가 구성된 후에 사용할 수 있습니다.

`UseSession`이 호출되기 전에 `HttpContext.Session`에 액세스할 수 없습니다.

앱이 응답 스트림에 쓰기 시작한 후에는 새 세션 cookie가 있는 새 세션을 만들 수 없습니다. 예외는 웹 서버 로그에 기록되며 브라우저에는 표시되지 않습니다.

### <a name="load-session-state-asynchronously"></a>세션 상태를 비동기적으로 로드

ASP.NET Core에서 기본 세션 공급자는 <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> 메서드가 <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A>, <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> 또는 <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> 메서드 전에 명시적으로 호출된 경우에만 기본 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 백업 저장소에서 비동기적으로 세션 레코드를 로드합니다. `LoadAsync`가 먼저 호출되지 않은 경우 기본 세션 레코드가 동기적으로 로드되며, 이는 크기에 따라 성능 저하를 초래할 수 있습니다.

이 패턴을 앱에 적용하려면 `LoadAsync` 메서드가 `TryGetValue`, `Set` 또는 `Remove` 이전에 호출되지 않은 경우 예외를 throw하는 버전으로 <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> 및 <xref:Microsoft.AspNetCore.Session.DistributedSession> 구현을 래핑합니다. 서비스 컨테이너에 래핑된 버전을 등록합니다.

### <a name="session-options"></a>세션 옵션

세션 기본값을 재정의하려면 <xref:Microsoft.AspNetCore.Builder.SessionOptions>를 사용합니다.

| 옵션 | 설명 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | cookie를 만드는 데 사용되는 설정을 결정합니다. <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> 기본값은 <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType>(`.AspNetCore.Session`)입니다. <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> 기본값은 <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType>(`/`)입니다. <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite>기본값은 <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType>(`1`)입니다. <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> 기본값은 `true`입니다. <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 기본값은 `false`입니다. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout`은 콘텐츠가 삭제되기 전까지 세션이 유휴 상태일 수 있는 시간을 나타냅니다. 각 세션 액세스는 시간 제한을 다시 설정합니다. 이 설정은 cookie가 아닌 세션의 콘텐츠에만 적용됩니다. 기본값은 20분입니다. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | 저장소에서 세션을 로드하거나 저장소로 다시 커밋할 수 있는 최대 시간입니다. 이 설정은 비동기 작업에만 적용될 수 있습니다. 이 시간 제한은 <xref:System.Threading.Timeout.InfiniteTimeSpan>을 사용하여 사용하지 않도록 설정할 수 있습니다. 기본값은 1분입니다. |

세션은 cookie를 사용하여 단일 브라우저에서 요청을 추적하고 식별합니다. 기본적으로 이 cookie는 `.AspNetCore.Session`이라고 하며 `/`의 경로를 사용합니다. cookie 기본값은 도메인을 지정하지 않으므로 페이지에서 클라이언트 쪽 스크립트에 사용할 수 없습니다(<xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> 기본값이 `true`이므로).

cookie 세션 기본값을 재정의하려면 <xref:Microsoft.AspNetCore.Builder.SessionOptions>를 사용합니다.

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

앱은 <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> 속성을 사용하여 서버 캐시의 콘텐츠가 중단되기 전에 유휴 상태일 수 있는 세션의 기간을 결정합니다. 이 속성은 cookie 만료와 무관합니다. [세션 미들웨어](xref:Microsoft.AspNetCore.Session.SessionMiddleware)를 통해 전달되는 각 요청은 시간 제한을 다시 설정합니다.

세션 상태는 *잠그지 않음* 입니다. 두 요청이 동시에 세션의 콘텐츠를 수정하려고 하는 경우 마지막 요청이 첫 번째 요청을 재정의합니다. `Session`은 *일관된 세션* 으로 구현됩니다. 즉, 모든 콘텐츠는 함께 저장됩니다. 두 요청이 서로 다른 세션 값을 수정하려고 할 때 마지막 요청이 첫 번째 요청에 의해 수행된 세션 변경 내용을 재정의할 수 있습니다.

### <a name="set-and-get-session-values"></a>세션 값 설정 및 가져오기

세션 상태는 <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType>이 포함된 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 클래스 또는 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> 클래스에서 액세스됩니다. 이 속성은 <xref:Microsoft.AspNetCore.Http.ISession> 구현입니다.

`ISession` 구현은 정수 및 문자열 값을 설정 및 검색하는 몇 가지 확장 메서드를 제공합니다. 확장 메서드는 <xref:Microsoft.AspNetCore.Http> 네임스페이스에 있습니다.

`ISession` 확장명 메서드:

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

다음 예제에서는 Razor Pages 페이지에서 `IndexModel.SessionKeyName` 키(샘플 앱의 `_Name`)의 세션 값을 검색합니다.

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

다음 예제에서는 정수와 문자열을 설정하고 가져오는 방법을 보여 줍니다.

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

메모리 내 캐시를 사용하는 경우에도, 분산된 캐시 시나리오를 사용하려면 모든 세션 데이터를 직렬화해야 합니다. 문자열 및 정수 직렬 변환기는 <xref:Microsoft.AspNetCore.Http.ISession>의 확장 메서드가 제공합니다. 복합 형식은 JSON과 같은 다른 메커니즘을 사용하여 사용자가 직렬화해야 합니다.

다음 샘플 코드를 사용하여 개체를 직렬화할 수 있습니다.

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

다음 예제는 `SessionExtensions` 클래스를 사용하여 직렬화 가능 개체를 설정하고 가져오는 방법을 보여 줍니다.

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core는 Razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) 또는 컨트롤러 <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>를 공개합니다. 이 속성은 다른 요청에서 읽혀질 때까지만 데이터를 저장합니다. [Keep](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*)(문자열) 및 [Peek](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*)(문자열) 메서드를 사용하면 요청이 끝날 때 삭제하지 않고 데이터를 검사할 수 있습니다. [Keep](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*)은 사전의 모든 항목을 보존하도록 표시합니다. `TempData`인 경우 다음과 같습니다.

* 데이터가 둘 이상의 요청에 필요한 경우 리디렉션에 유용합니다.
* `TempData` 공급자가 cookie 또는 세션 상태를 사용하여 구현합니다.

## <a name="tempdata-samples"></a>TempData 샘플

고객을 만드는 다음 페이지를 살펴보겠습니다.

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

다음 페이지는 `TempData["Message"]`를 표시합니다.

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

위 태그에서 `Peek`가 사용되기 때문에 요청 끝에서 `TempData["Message"]`가 삭제되지 **않습니다**. 페이지를 새로 고치면 `TempData["Message"]`의 내용이 표시됩니다.

다음 태그는 위 코드와 비슷하지만 `Keep`을 사용하여 요청 끝에서 데이터를 유지합니다.

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

*IndexPeek* 페이지와 *IndexKeep* 페이지 사이를 이동해도 `TempData["Message"]`가 삭제되지 않습니다.

다음 코드는 `TempData["Message"]`를 표시하지만 요청 끝에서 `TempData["Message"]`가 삭제됩니다.

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData 공급자

cookie 기반 TempData 공급자는 TempData를 cookie에 저장하는 데 기본적으로 사용됩니다.

cookie 데이터는 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>를 사용하여 암호화되고, <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder>로 인코딩된 후 청크 분할됩니다. 암호화 및 청크로 인해 최대 cookie 크기는 [4,096바이트](http://www.faqs.org/rfcs/rfc2965.html) 미만입니다. 암호화된 데이터를 압축하는 것은 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격과 같은 보안 문제를 일으킬 수 있으므로 cookie 데이터는 압축되지 않습니다. cookie 기반 TempData 공급자에 대한 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>를 참조하세요.

### <a name="choose-a-tempdata-provider"></a>TempData 공급자 선택

TempData 공급자를 선택하는 데는 다음과 같은 몇 가지 고려 사항이 수반됩니다.

* 앱이 이미 세션 상태를 사용합니까? 그런 경우, 데이터 크기를 제외하고 세션 상태 TempData 공급자 사용과 관련해서 앱에 부과되는 추가 비용은 없습니다.
* 앱이 비교적 적은 양의 데이터(최대 500바이트)에만 TempData를 제한적으로 사용합니까? 그런 경우 cookie TempData 공급자는 TempData를 전달하는 각 요청에 적은 비용을 추가합니다. 그렇지 않은 경우 세션 상태 TempData 공급자는 TempData가 사용될 때까지 각 요청에서 많은 양의 데이터를 왕복 작업하지 않도록 하는 데 도움이 될 수 있습니다.
* 앱이 여러 서버의 서버 팜에서 실행됩니까? 그런 경우 데이터 보호 외부에서 cookie TempData 공급자를 사용하는 데 필요한 추가 구성은 없습니다(<xref:security/data-protection/introduction> 및 [키 스토리지 공급자](xref:security/data-protection/implementation/key-storage-providers) 참조).

대부분의 웹 클라이언트(예: 웹 브라우저)는 각 cookie의 최대 크기와 cookie의 총 수에 제한을 적용합니다. cookie TempData 공급자를 사용하는 경우, 앱이 [해당 제한](http://www.faqs.org/rfcs/rfc2965.html)을 넘지 않도록 확인합니다. 데이터의 총 크기를 고려합니다. 암호화 및 청크 분할로 인한 cookie 크기 증가를 고려합니다.

### <a name="configure-the-tempdata-provider"></a>TempData 공급자 구성

cookie 기반 TempData 공급자는 기본적으로 활성화됩니다.

세션 기반 TempData 공급자를 사용하도록 설정하려면 <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> 확장 메서드를 사용합니다. `AddSessionStateTempDataProvider`를 한 번만 호출하면 됩니다.

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,8,30)]

## <a name="query-strings"></a>쿼리 문자열

제한된 양의 데이터는 새 요청의 쿼리 문자열에 추가하여 한 요청에서 다른 요청으로 전달할 수 있습니다. 이는 이메일 또는 소셜 네트워크를 통해 공유되도록 포함된 상태가 있는 링크를 허용하는 영구적인 방식으로 상태를 캡처하는 데 유용합니다. URL 쿼리 문자열은 공용이므로 중요한 데이터에 쿼리 문자열을 사용하지 마세요.

의도하지 않은 공유 외에도, 쿼리 문자열에 데이터를 포함하면 앱이 [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))(교차 사이트 요청 위조) 공격에 노출될 수 있습니다. 유지된 모든 세션 상태를 CSRF 공격으로부터 보호해야 합니다. 자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.

## <a name="hidden-fields"></a>숨겨진 필드

데이터는 숨겨진 양식 필드에 저장되고 다음 요청에서 다시 게시될 수 있습니다. 이는 다중 페이지 폼에서 일반적입니다. 클라이언트는 잠재적으로 데이터를 변조할 수 있으므로 앱은 항상 숨겨진 필드에 저장된 데이터의 유효성을 다시 검사해야 합니다.

## <a name="httpcontextitems"></a>HttpContext.Items

<xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> 컬렉션은 단일 요청을 처리하는 동안 데이터를 저장하는 데 사용됩니다. 컬렉션의 콘텐츠는 요청이 처리된 후 삭제됩니다. `Items` 컬렉션은 구성 요소 또는 미들웨어가 요청 중에 다른 시점에서 작동하고 매개 변수를 전달할 직접적인 방법이 없는 경우에 통신을 지원하기 위해 자주 사용됩니다.

다음 예제에서 [미들웨어](xref:fundamentals/middleware/index)는 `isVerified`를 `Items` 컬렉션에 추가합니다.

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

단일 앱에서만 사용되는 미들웨어의 경우 고정된 `string` 키가 허용됩니다. 앱 간에 공유되는 미들웨어는 키 충돌을 방지하기 위해 고유한 개체 키를 사용해야 합니다. 다음 예제에서는 미들웨어 클래스에 정의된 고유한 개체 키를 사용하는 방법을 보여 줍니다.

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

다른 코드는 미들웨어 클래스에 의해 노출된 키를 사용하여 `HttpContext.Items`에 저장된 값에 액세스할 수 있습니다.

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

이 방법은 또한 코드에서 키 문자열을 사용하지 않아도 된다는 이점이 있습니다.

## <a name="cache"></a>캐시

캐싱은 데이터 저장 및 검색하는 효율적인 방법입니다. 앱은 캐시된 항목의 수명을 제어할 수 있습니다. 자세한 내용은 <xref:performance/caching/response>를 참조하세요.

캐시된 데이터는 특정 요청, 사용자 또는 세션과 연관되지 않습니다. **다른 사용자 요청을 통해 검색될 수 있는 사용자별 데이터를 캐시하면 안 됩니다.**

애플리케이션 전체 데이터를 캐시하려면 <xref:performance/caching/memory>를 참조하세요.

## <a name="common-errors"></a>일반적인 오류

* "'Microsoft.AspNetCore.Session.DistributedSessionStore'를 활성화하려고 시도하는 동안 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' 형식에 대한 서비스를 확인할 수 없습니다."

  이 오류는 일반적으로 하나 이상의 `IDistributedCache` 구현을 구성하지 못한 경우에 발생합니다. 자세한 내용은 <xref:performance/caching/distributed> 및 <xref:performance/caching/memory>를 참조하세요.

세션 미들웨어가 세션을 유지하지 못한 경우

* 미들웨어가 예외를 기록하고 요청은 정상적으로 계속 진행됩니다.
* 이로 인해 예기치 않은 동작이 발생합니다.

백업 저장소를 사용할 수 없는 경우 세션 미들웨어가 세션을 유지하지 못할 수 있습니다. 예를 들어 사용자는 세션에 쇼핑 카트를 저장합니다. 사용자가 카트에 항목을 추가하지만 커밋이 실패합니다. 앱은 실패에 대해 알지 못하므로 항목이 카트에 추가되었다고 보고하지만, 이는 사실이 아닙니다.

오류를 확인하는 권장 방법은 앱이 세션에 기록을 마쳤을 때 `await feature.Session.CommitAsync`를 호출하는 것입니다. 백업 저장소를 사용할 수 없는 경우 <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*>에서 예외를 throw합니다. `CommitAsync`가 실패하면 앱에서 예외를 처리할 수 있습니다. 데이터 저장소를 사용할 수 없는 경우에는 동일한 조건에서 <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*>가 throw합니다.
  
## <a name="no-locsignalr-and-session-state"></a>SignalR과 세션 상태

SignalR 앱은 세션 상태를 사용하여 정보를 저장해서는 안 됩니다. SignalR 앱은 허브에서 `Context.Items`의 연결 상태별로 저장할 수 있습니다. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>추가 자료

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose) 및 [Luke Latham](https://github.com/guardrex)

HTTP는 상태 비저장 프로토콜입니다. HTTP 요청은 추가 단계를 수행하지 않고 사용자 값 또는 앱 상태를 유지하지 않는 독립적인 메시지입니다. 이 문서에서는 요청 간 사용자 데이터와 앱 상태를 유지하는 몇 가지 방법에 대해 설명합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>상태 관리

상태는 여러 방법을 사용하여 저장할 수 있습니다. 각 방법은 이 항목 뒷부분에서 설명합니다.

| 스토리지 접근 방식 | 스토리지 메커니즘 |
| ---------------- | ----------------- |
| [Cookie](#cookies) | HTTP cookie(서버 쪽 앱 코드를 사용하여 저장된 데이터를 포함할 수 있음) |
| [세션 상태](#session-state) | HTTP cookie 및 서버 쪽 앱 코드 |
| [TempData](#tempdata) | HTTP cookie 또는 세션 상태 |
| [쿼리 문자열](#query-strings) | HTTP 쿼리 문자열 |
| [숨겨진 필드](#hidden-fields) | HTTP 양식 필드 |
| [HttpContext.Items](#httpcontextitems) | 서버 쪽 앱 코드 |
| [캐시](#cache) | 서버 쪽 앱 코드 |
| [종속성 주입](#dependency-injection) | 서버 쪽 앱 코드 |

## <a name="no-loccookies"></a>Cookies

Cookie는 요청 간에 데이터를 저장합니다. cookie는 모든 요청과 함께 전송되므로 해당 크기는 최소로 유지되어야 합니다. 이상적으로 식별자만 앱에 저장된 데이터와 함께 cookie에 저장되어야 합니다. 대부분의 브라우저는 cookie 크기를 4,096바이트로 제한합니다. 제한된 수의 cookie만 각 도메인에 사용할 수 있습니다.

cookie는 변조될 수 있기 때문에 앱에서 유효성을 검사해야 합니다. Cookie는 사용자가 삭제할 수 있으며 클라이언트에서 만료됩니다. 그러나 cookie는 일반적으로 클라이언트에서 데이터 지속성의 가장 안정적인 형태입니다.

Cookie는 종종 개인 설정에 사용됩니다. 여기서 콘텐츠는 알려진 사용자에 대해 사용자 지정됩니다. 사용자만 식별되고 대부분의 경우 인증되지 않습니다. cookie는 사용자 이름, 계정 이름 또는 고유한 사용자 ID(예: GUID)를 저장할 수 있습니다. cookie를 사용하여 선호하는 웹 사이트 배경색과 같은 사용자의 맞춤형 설정에 액세스할 수 있습니다.

cookie를 발급하고 개인 정보 보호 문제를 다룰 때 [유럽 연합 GDPR(일반 데이터 보호 규정)](https://ec.europa.eu/info/law/law-topic/data-protection)에 유의합니다. 자세한 내용은 [ASP.NET Core의 GDPR(일반 데이터 보호 규정) 지원](xref:security/gdpr)을 참조하세요.

## <a name="session-state"></a>세션 상태

세션 상태는 사용자가 웹앱을 탐색하는 동안 사용자 데이터를 스토리지하기 위한 ASP.NET Core 시나리오입니다. 세션 상태는 앱에서 유지 관리하는 저장소를 사용하여 클라이언트의 요청 간에 데이터를 유지합니다. 세션 데이터는 캐시에 의해 백업되고 임시 데이터로 간주되므로 사이트는 세션 데이터 없이 계속 작동합니다. 중요한 애플리케이션 데이터는 사용자 데이터베이스에 저장되고 성능 최적화로 세션에 캐시되어야 합니다.

> [!NOTE]
> [SignalR 허브](xref:signalr/hubs)가 HTTP 컨텍스트와 독립적으로 실행될 수 있으므로, 세션은 [SignalR](xref:signalr/index) 앱에서 지원되지 않습니다. 예를 들어, 허브에서 긴 폴링 요청이 HTTP 컨텍스트 수명을 초과하여 계속 열려 있을 경우 이 문제가 발생할 수 있습니다.

ASP.NET Core는 각 요청과 함께 앱으로 전송되는 세션 ID를 포함하는 cookie를 클라이언트에 제공하여 세션 상태를 유지합니다. 앱은 세션 ID를 사용하여 세션 데이터를 가져옵니다.

세션 상태는 다음과 같은 동작을 보여 줍니다.

* 세션 cookie는 브라우저에 특정되기 때문에 브라우저 간에 세션이 공유되지 않습니다.
* 세션 cookie는 브라우저 세션이 끝나면 삭제됩니다.
* cookie가 만료된 세션에 대해 수신되면 동일한 세션 cookie를 사용하는 새 세션이 생성됩니다.
* 빈 세션은 유지되지 않습니다. 요청 간 세션을 유지하려면 세션에 하나 이상의 값이 설정되어 있어야 합니다. 세션이 유지되지 않으면 새 요청마다 새 세션 ID가 생성됩니다.
* 앱은 마지막 요청 이후 제한된 시간 동안 세션을 유지합니다. 앱은 세션 시간 제한을 설정하거나 20분의 기본값을 사용합니다. 세션 상태는 특정 세션과 관련된 사용자 데이터를 저장하되, 데이터가 세션 간에 영구 스토리지를 요구하지 않는 경우에 적합합니다.
* 세션 데이터는 <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> 구현이 호출되거나 세션이 만료될 때 삭제됩니다.
* 클라이언트 브라우저가 닫혔거나 세션 cookie가 삭제 또는 클라이언트에서 만료되었을 때 앱 코드에 이를 알려주는 기본 메커니즘은 없습니다.
* ASP.NET Core MVC 및 Razor Pages 템플릿에는 GDPR(일반 데이터 보호 규정) 지원이 포함되어 있습니다. 세션 상태 cookie는 기본적으로 필수로 표시되지 않으므로 사이트 방문자가 추적을 허용하지 않는 한 세션 상태가 작동하지 않습니다. 자세한 내용은 <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>를 참조하세요.

> [!WARNING]
> 중요한 데이터를 세션 상태에 저장하지 마세요. 사용자는 브라우저를 닫지 않고 세션 cookie를 지울 수 있습니다. 일부 브라우저는 브라우저 창이 닫혀도 유효한 세션 cookie를 유지 관리합니다. 세션은 단일 사용자로 제한될 수 없으므로 다음 사용자는 동일한 세션 cookie로 앱을 계속 검색할 수 있습니다.

메모리 내 캐시 공급자는 앱이 있는 서버의 메모리에 세션 데이터를 저장합니다. 서버 팜 시나리오:

* *고정 세션* 을 사용하여 각 세션을 개별 서버의 특정 앱 인스턴스에 연결합니다. [Azure App Service](https://azure.microsoft.com/services/app-service/)는 [ARR(애플리케이션 요청 라우팅)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)을 사용하여 기본적으로 고정 세션을 적용합니다. 그러나 고정 세션은 확장성에 영향을 주고 웹앱 업데이트를 복잡하게 만들 수 있습니다. 더 나은 방법은 고정 세션이 필요 없는 Redis 또는 SQL Server 분산 캐시를 사용하는 것입니다. 자세한 내용은 <xref:performance/caching/distributed>를 참조하세요.
* 세션 cookie는 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>를 통해 암호화됩니다. 데이터 보호는 각 머신에서 세션 cookie를 읽을 수 있도록 올바르게 구성되어야 합니다. 자세한 내용은 <xref:security/data-protection/introduction> 및 [키 스토리지 공급자](xref:security/data-protection/implementation/key-storage-providers)를 참조하세요.

### <a name="configure-session-state"></a>세션 상태 구성

[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함된 [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 패키지는 세션 상태 관리용 미들웨어를 제공합니다. 세션 미들웨어를 활성화하려면 `Startup`은 다음을 포함해야 합니다.

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리 캐시 중 하나. `IDistributedCache` 구현은 세션에 대한 백업 저장소로 사용됩니다. 자세한 내용은 <xref:performance/caching/distributed>를 참조하세요.
* `ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A>에 대한 호출.
* `Configure`의 <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>에 대한 호출.

다음 코드에서는 `IDistributedCache`의 기본 메모리 내 구현을 사용하여 메모리 내 세션 공급자를 설정하는 방법을 보여 줍니다.

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

미들웨어의 순서가 중요합니다. 앞의 예에서 `UseMvc` 이후에 `UseSession`이 호출되면 `InvalidOperationException` 예외가 발생합니다. 자세한 내용은 [미들웨어 순서 지정](xref:fundamentals/middleware/index#order)을 참조하세요.

<xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType>은 세션 상태가 구성된 후에 사용할 수 있습니다.

`UseSession`이 호출되기 전에 `HttpContext.Session`에 액세스할 수 없습니다.

앱이 응답 스트림에 쓰기 시작한 후에는 새 세션 cookie가 있는 새 세션을 만들 수 없습니다. 예외는 웹 서버 로그에 기록되며 브라우저에는 표시되지 않습니다.

### <a name="load-session-state-asynchronously"></a>세션 상태를 비동기적으로 로드

ASP.NET Core에서 기본 세션 공급자는 <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> 메서드가 <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A>, <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> 또는 <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> 메서드 전에 명시적으로 호출된 경우에만 기본 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 백업 저장소에서 비동기적으로 세션 레코드를 로드합니다. `LoadAsync`가 먼저 호출되지 않은 경우 기본 세션 레코드가 동기적으로 로드되며, 이는 크기에 따라 성능 저하를 초래할 수 있습니다.

이 패턴을 앱에 적용하려면 `LoadAsync` 메서드가 `TryGetValue`, `Set` 또는 `Remove` 이전에 호출되지 않은 경우 예외를 throw하는 버전으로 <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> 및 <xref:Microsoft.AspNetCore.Session.DistributedSession> 구현을 래핑합니다. 서비스 컨테이너에 래핑된 버전을 등록합니다.

### <a name="session-options"></a>세션 옵션

세션 기본값을 재정의하려면 <xref:Microsoft.AspNetCore.Builder.SessionOptions>를 사용합니다.

| 옵션 | 설명 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | cookie를 만드는 데 사용되는 설정을 결정합니다. <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> 기본값은 <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType>(`.AspNetCore.Session`)입니다. <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> 기본값은 <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType>(`/`)입니다. <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite>기본값은 <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType>(`1`)입니다. <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> 기본값은 `true`입니다. <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 기본값은 `false`입니다. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout`은 콘텐츠가 삭제되기 전까지 세션이 유휴 상태일 수 있는 시간을 나타냅니다. 각 세션 액세스는 시간 제한을 다시 설정합니다. 이 설정은 cookie가 아닌 세션의 콘텐츠에만 적용됩니다. 기본값은 20분입니다. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | 저장소에서 세션을 로드하거나 저장소로 다시 커밋할 수 있는 최대 시간입니다. 이 설정은 비동기 작업에만 적용될 수 있습니다. 이 시간 제한은 <xref:System.Threading.Timeout.InfiniteTimeSpan>을 사용하여 사용하지 않도록 설정할 수 있습니다. 기본값은 1분입니다. |

세션은 cookie를 사용하여 단일 브라우저에서 요청을 추적하고 식별합니다. 기본적으로 이 cookie는 `.AspNetCore.Session`이라고 하며 `/`의 경로를 사용합니다. cookie 기본값은 도메인을 지정하지 않으므로 페이지에서 클라이언트 쪽 스크립트에 사용할 수 없습니다(<xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> 기본값이 `true`이므로).

cookie 세션 기본값을 재정의하려면 `SessionOptions`를 사용합니다.

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

앱은 <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> 속성을 사용하여 서버 캐시의 콘텐츠가 중단되기 전에 유휴 상태일 수 있는 세션의 기간을 결정합니다. 이 속성은 cookie 만료와 무관합니다. [세션 미들웨어](xref:Microsoft.AspNetCore.Session.SessionMiddleware)를 통해 전달되는 각 요청은 시간 제한을 다시 설정합니다.

세션 상태는 *잠그지 않음* 입니다. 두 요청이 동시에 세션의 콘텐츠를 수정하려고 하는 경우 마지막 요청이 첫 번째 요청을 재정의합니다. `Session`은 *일관된 세션* 으로 구현됩니다. 즉, 모든 콘텐츠는 함께 저장됩니다. 두 요청이 서로 다른 세션 값을 수정하려고 할 때 마지막 요청이 첫 번째 요청에 의해 수행된 세션 변경 내용을 재정의할 수 있습니다.

### <a name="set-and-get-session-values"></a>세션 값 설정 및 가져오기

세션 상태는 <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType>이 포함된 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 클래스 또는 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> 클래스에서 액세스됩니다. 이 속성은 <xref:Microsoft.AspNetCore.Http.ISession> 구현입니다.

`ISession` 구현은 정수 및 문자열 값을 설정 및 검색하는 몇 가지 확장 메서드를 제공합니다. 확장 메서드는 [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) 패키지가 프로젝트에서 참조될 때, <xref:Microsoft.AspNetCore.Http> 네임스페이스에 있습니다(확장 메서드에 액세스하려면 `using Microsoft.AspNetCore.Http;` 문 추가). 두 패키지 모두 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.

`ISession` 확장명 메서드:

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

다음 예제에서는 Razor Pages 페이지에서 `IndexModel.SessionKeyName` 키(샘플 앱의 `_Name`)의 세션 값을 검색합니다.

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

다음 예제에서는 정수와 문자열을 설정하고 가져오는 방법을 보여 줍니다.

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

메모리 내 캐시를 사용하는 경우에도, 분산된 캐시 시나리오를 사용하려면 모든 세션 데이터를 직렬화해야 합니다. 문자열 및 정수 직렬 변환기는 <xref:Microsoft.AspNetCore.Http.ISession>의 확장 메서드가 제공합니다. 복합 형식은 JSON과 같은 다른 메커니즘을 사용하여 사용자가 직렬화해야 합니다.

다음 확장 메서드를 추가하여 직렬화 가능 개체를 설정하고 가져옵니다.

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

다음 예제에서는 확장 메서드를 사용하여 직렬화 가능 개체를 설정하고 가져오는 방법을 보여 줍니다.

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core는 Razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) 또는 컨트롤러 <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>를 공개합니다. 이 속성은 다른 요청에서 읽혀질 때까지만 데이터를 저장합니다. [Keep(문자열)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) 및 [Peek(문자열)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) 메서드를 사용하면 요청 끝에서 삭제하지 않고 데이터를 검사할 수 있습니다. [Keep()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*)은 사전의 모든 항목을 보존하도록 표시합니다. `TempData`는 두 개 이상의 요청에 데이터가 필요한 리디렉션에 특히 유용합니다. `TempData`는 `TempData` 공급자가 cookie 또는 세션 상태를 사용하여 구현합니다.

## <a name="tempdata-samples"></a>TempData 샘플

고객을 만드는 다음 페이지를 살펴보겠습니다.

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

다음 페이지는 `TempData["Message"]`를 표시합니다.

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

위 태그에서 `Peek`가 사용되기 때문에 요청 끝에서 `TempData["Message"]`가 삭제되지 **않습니다**. 페이지를 새로 고치면 `TempData["Message"]`가 표시됩니다.

다음 태그는 위 코드와 비슷하지만 `Keep`을 사용하여 요청 끝에서 데이터를 유지합니다.

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

*IndexPeek* 페이지와 *IndexKeep* 페이지 사이를 이동해도 `TempData["Message"]`가 삭제되지 않습니다.

다음 코드는 `TempData["Message"]`를 표시하지만 요청 끝에서 `TempData["Message"]`가 삭제됩니다.

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData 공급자

cookie 기반 TempData 공급자는 TempData를 cookie에 저장하는 데 기본적으로 사용됩니다.

cookie 데이터는 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>를 사용하여 암호화되고, <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder>로 인코딩된 후 청크 분할됩니다. cookie가 청크 분할되므로 ASP.NET Core 1.x에서 확인한 단일 cookie 크기 제한은 적용되지 않습니다. 암호화된 데이터를 압축하는 것은 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격과 같은 보안 문제를 일으킬 수 있으므로 cookie 데이터는 압축되지 않습니다. cookie 기반 TempData 공급자에 대한 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>를 참조하세요.

### <a name="choose-a-tempdata-provider"></a>TempData 공급자 선택

TempData 공급자를 선택하는 데는 다음과 같은 몇 가지 고려 사항이 수반됩니다.

1. 앱이 이미 세션 상태를 사용합니까? 그런 경우 세션 상태 TempData 공급자 사용에는 앱에 대한 추가 비용이 없습니다(데이터 크기 제외).
2. 앱은 상대적으로 적은 양의 데이터에 TempData만 제한적으로 사용합니까(최대 500바이트)? 그런 경우 cookie TempData 공급자는 TempData를 전달하는 각 요청에 적은 비용을 추가합니다. 그렇지 않은 경우 세션 상태 TempData 공급자는 TempData가 사용될 때까지 각 요청에서 많은 양의 데이터를 왕복 작업하지 않도록 하는 데 도움이 될 수 있습니다.
3. 앱이 여러 서버의 서버 팜에서 실행됩니까? 그런 경우 데이터 보호 외부에서 cookie TempData 공급자를 사용하는 데 필요한 추가 구성은 없습니다(<xref:security/data-protection/introduction> 및 [키 스토리지 공급자](xref:security/data-protection/implementation/key-storage-providers) 참조).

> [!NOTE]
> 대부분의 웹 클라이언트(예: 웹 브라우저)는 각 cookie의 최대 크기, cookie의 총 수 또는 둘 다에 제한을 적용합니다. cookie TempData 공급자를 사용하는 경우, 앱이 해당 제한을 넘지 않도록 확인합니다. 데이터의 총 크기를 고려합니다. 암호화 및 청크 분할로 인한 cookie 크기 증가를 고려합니다.

### <a name="configure-the-tempdata-provider"></a>TempData 공급자 구성

cookie 기반 TempData 공급자는 기본적으로 활성화됩니다.

세션 기반 TempData 공급자를 사용하도록 설정하려면 <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> 확장 메서드를 사용합니다.

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

미들웨어의 순서가 중요합니다. 앞의 예에서 `UseMvc` 이후에 `UseSession`이 호출되면 `InvalidOperationException` 예외가 발생합니다. 자세한 내용은 [미들웨어 순서 지정](xref:fundamentals/middleware/index#order)을 참조하세요.

> [!IMPORTANT]
> .NET Framework를 대상으로 하고 세션 기반 TempData 공급자를 사용하는 경우 [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 패키지를 프로젝트에 추가합니다.

## <a name="query-strings"></a>쿼리 문자열

제한된 양의 데이터는 새 요청의 쿼리 문자열에 추가하여 한 요청에서 다른 요청으로 전달할 수 있습니다. 이는 이메일 또는 소셜 네트워크를 통해 공유되도록 포함된 상태가 있는 링크를 허용하는 영구적인 방식으로 상태를 캡처하는 데 유용합니다. URL 쿼리 문자열은 공용이므로 중요한 데이터에 쿼리 문자열을 사용하지 마세요.

의도하지 않은 공유 외에도 쿼리 문자열에 데이터를 포함하면 사용자가 인증되는 동안 악성 사이트를 방문하도록 유도할 수 있는 [CSRF(교차 사이트 요청 위조)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 공격에 대한 기회를 만들 수 있습니다. 공격자는 앱에서 사용자 데이터를 도용하거나 사용자를 대신하여 악의적인 작업을 수행할 수 있습니다. 유지된 모든 앱 또는 세션 상태를 CSRF 공격으로부터 보호해야 합니다. 자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.

## <a name="hidden-fields"></a>숨겨진 필드

데이터는 숨겨진 양식 필드에 저장되고 다음 요청에서 다시 게시될 수 있습니다. 이는 다중 페이지 폼에서 일반적입니다. 클라이언트는 잠재적으로 데이터를 변조할 수 있으므로 앱은 항상 숨겨진 필드에 저장된 데이터의 유효성을 다시 검사해야 합니다.

## <a name="httpcontextitems"></a>HttpContext.Items

<xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> 컬렉션은 단일 요청을 처리하는 동안 데이터를 저장하는 데 사용됩니다. 컬렉션의 콘텐츠는 요청이 처리된 후 삭제됩니다. `Items` 컬렉션은 구성 요소 또는 미들웨어가 요청 중에 다른 시점에서 작동하고 매개 변수를 전달할 직접적인 방법이 없는 경우에 통신을 지원하기 위해 자주 사용됩니다.

다음 예제에서 [미들웨어](xref:fundamentals/middleware/index)는 `Items` 컬렉션에 `isVerified`를 추가합니다.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

파이프라인의 뒷부분에서 다른 미들웨어는 `isVerified` 값에 액세스할 수 있습니다.

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

단일 앱에서만 사용되는 미들웨어의 경우 `string` 키가 허용됩니다. 앱 인스턴스 간에 공유되는 미들웨어는 키 충돌을 방지하기 위해 고유한 개체 키를 사용해야 합니다. 다음 예제에서는 미들웨어 클래스에 정의된 고유한 개체 키를 사용하는 방법을 보여 줍니다.

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

다른 코드는 미들웨어 클래스에 의해 노출된 키를 사용하여 `HttpContext.Items`에 저장된 값에 액세스할 수 있습니다.

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

이 방법은 또한 코드에서 키 문자열을 사용하지 않아도 된다는 이점이 있습니다.

## <a name="cache"></a>캐시

캐싱은 데이터 저장 및 검색하는 효율적인 방법입니다. 앱은 캐시된 항목의 수명을 제어할 수 있습니다.

캐시된 데이터는 특정 요청, 사용자 또는 세션과 연관되지 않습니다. **다른 사용자의 요청으로 검색될 수 있는 사용자별 데이터를 캐시하지 않도록 주의합니다.**

자세한 내용은 <xref:performance/caching/response>를 참조하세요.

## <a name="dependency-injection"></a>종속성 주입

[종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 모든 사용자에게 데이터를 사용할 수 있도록 합니다.

1. 데이터를 포함하는 서비스를 정의합니다. 예를 들어 `MyAppData`라는 클래스가 정의됩니다.

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. `Startup.ConfigureServices`에 서비스 클래스를 추가합니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. 데이터 서비스 클래스를 사용합니다.

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>일반적인 오류

* "'Microsoft.AspNetCore.Session.DistributedSessionStore'를 활성화하려고 시도하는 동안 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' 형식에 대한 서비스를 확인할 수 없습니다."

  이는 일반적으로는 하나 이상의 `IDistributedCache` 구현을 구성하는 데 실패하여 발생됩니다. 자세한 내용은 <xref:performance/caching/distributed> 및 <xref:performance/caching/memory>를 참조하세요.

* 세션 미들웨어가 세션 유지에 실패할 경우(예: 백업 저장소를 사용할 수 없는 경우) 미들웨어는 예외를 기록하고 요청은 정상적으로 계속됩니다. 이로 인해 예기치 않은 동작이 발생합니다.

  예를 들어 사용자는 세션에 쇼핑 카트를 저장합니다. 사용자가 카트에 항목을 추가하지만 커밋이 실패합니다. 앱은 실패에 대해 알지 못하므로 항목이 카트에 추가되었다고 보고하지만, 이는 사실이 아닙니다.

  권장되는 오류 확인 방법은 앱이 세션에 작성을 완료하면 앱 코드에서 `await feature.Session.CommitAsync();`를 호출하는 것입니다. 백업 저장소를 사용할 수 없는 경우 `CommitAsync`에서 예외를 throw합니다. `CommitAsync`가 실패하면 앱에서 예외를 처리할 수 있습니다. `LoadAsync`는 같은 조건에서 데이터 저장소를 사용할 수 없는 경우 throw됩니다.
  
## <a name="no-locsignalr-and-session-state"></a>SignalR과 세션 상태

SignalR 앱은 세션 상태를 사용하여 정보를 저장해서는 안 됩니다. SignalR 앱은 허브에서 `Context.Items`의 연결 상태별로 저장할 수 있습니다. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>추가 자료

<xref:host-and-deploy/web-farm>
::: moniker-end
