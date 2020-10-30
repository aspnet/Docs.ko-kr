---
title: cookieASP.NET Core에서 SameSite s 작업
author: rick-anderson
description: 을 사용 하 여 ASP.NET Core를 SameSite 하는 방법을 알아봅니다. cookie
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
- Electron
uid: security/samesite
ms.openlocfilehash: 6f826416e3045df32abf41e94e667120e71ae717
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051618"
---
# <a name="work-with-samesite-no-loccookies-in-aspnet-core"></a>cookieASP.NET Core에서 SameSite s 작업

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

SameSite은 CSRF (교차 사이트 요청 위조) 공격에 대 한 보호를 제공 하도록 설계 된 [IETF](https://ietf.org/about/) 초안 표준입니다. 원래 [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)에서는 초안 표준이 [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)에서 업데이트 되었습니다. 업데이트 된 표준은 이전 표준과 호환 되지 않으며 다음과 같은 가장 눈에 띄는 차이점이 있습니다.

* CookieSameSite 헤더가 없는 s는 기본적으로로 처리 됩니다 `SameSite=Lax` .
* `SameSite=None` 는 사이트 간 사용을 허용 하는 데 사용 해야 합니다 cookie .
* Cookieassert `SameSite=None` 도로 표시 해야 합니다 `Secure` .
* 를 사용 하는 응용 프로그램 [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) `sameSite=Lax` `sameSite=Strict` cookie `<iframe>` 은 사이트 간 시나리오로 처리 되기 때문에 또는와 관련 된 문제가 발생할 수 있습니다.
* 값은 `SameSite=None` [2016 표준](https://tools.ietf.org/html/draft-west-first-party-cookies-07) 에서 허용 되지 않으며 일부 구현에서 이러한를 cookie 로 처리 `SameSite=Strict` 합니다. 이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.

이 `SameSite=Lax` 설정은 대부분의 응용 프로그램에서 작동 cookie 합니다. Oidc ( [Openid connect Connect](https://openid.net/connect/) )와 같은 일부 형태의 인증 및 [ws-federation](https://auth0.com/docs/protocols/ws-fed) 은 게시 기반 리디렉션에 대해 기본적으로 사용 됩니다. 사후 기반 리디렉션은 SameSite 브라우저 보호를 트리거하고 이러한 구성 요소에 대해 SameSite을 사용할 수 없습니다. 대부분의 [OAuth](https://oauth.net/) 로그인은 요청 흐름의 차이로 인해 영향을 받지 않습니다.

를 내보내는 각 ASP.NET Core 구성 요소는 cookie SameSite가 적절 한지 결정 해야 합니다.

## <a name="samesite-and-no-locidentity"></a>SameSite 및 Identity

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a>SameSite 테스트 샘플 코드

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

다음 샘플을 다운로드 하 고 테스트할 수 있습니다.

| 예제               | 문서 |
| ----------------- | ------------ |
| [.NET Core MVC](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [.NET Core Razor 페이지](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

다음 샘플을 다운로드 하 고 테스트할 수 있습니다.


| 예제               | 문서 |
| ----------------- | ------------ |
| [.NET Core Razor 페이지](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a>SameSite 특성에 대 한 .NET Core 지원

.NET Core 2.2 이상에서는 12 월 2019의 업데이트 출시 이후 SameSite의 2019 초안 표준을 지원 합니다. 개발자는 속성을 사용 하 여 sameSite 특성의 값을 프로그래밍 방식으로 제어할 수 있습니다 `HttpCookie.SameSite` . 속성을 `SameSite` Strict, Strict 또는 None으로 설정 하면 해당 값이로 네트워크에 기록 됩니다 cookie . 이 값을로 설정 `(SameSiteMode)(-1)` 하면 네트워크에 sameSite 특성이 포함 되지 않음을 나타냅니다. cookie

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

.NET Core 3.0 이상에서는 업데이트 된 SameSite 값을 지원 하 고 열거형에 추가 열거형 값을 추가 합니다 `SameSiteMode.Unspecified` `SameSiteMode` .
이 새 값은를 사용 하 여 sameSite를 전송 하지 않음을 나타냅니다 cookie .

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a>12 월 패치 동작 변경 내용

.NET Framework 및 .NET Core 2.1의 특정 동작 변경 내용은 `SameSite` 속성이 값을 해석 하는 방법입니다 `None` . 패치 후의 값이 " `None` 특성을 전혀 내보내지 않습니다." 라는 값을 패치 한 후에는 "값을 사용 하 여 특성을 내보냅니다." 라는 의미입니다 `None` . 패치를 값으로 `SameSite` 설정 하면 `(SameSiteMode)(-1)` 특성을 내보내지 않습니다.

폼 인증 및 세션 상태의 기본 SameSite 값 cookie 이에서로 변경 되었습니다 `None` `Lax` .

::: moniker-end

## <a name="api-usage-with-samesite"></a>SameSite를 사용 하는 API 사용

[HttpContext 응답입니다. Cookie s.](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) 의 기본값은에 `Unspecified` 추가 됩니다. 즉,에 SameSite 특성이 추가 되지 않고 cookie 클라이언트는 기본 동작을 사용 합니다 (새 브라우저의 경우는 안 되며 이전 버전의 경우에는 없음). 다음 코드에서는 cookie SameSite 값을로 변경 하는 방법을 보여 줍니다 `SameSiteMode.Lax` .

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

S를 내보내는 모든 ASP.NET Core 구성 요소는 cookie 해당 시나리오에 적합 한 설정을 사용 하 여 이전 기본값을 재정의 합니다. 재정의 된 이전 기본값은 변경 되지 않았습니다.

| 구성 요소 | cookie | 기본값 |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [Cookie 인증은](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [TwitterOptions. 상태 Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. 상관 관계Cookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None` |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [OpenIdConnectOptions. NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [HttpContext 응답입니다. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

ASP.NET Core 3.1 이상에서는 다음 SameSite 지원 합니다.

* 내보내기에 대 한 동작을 다시 정의 합니다. `SameSiteMode.None``SameSite=None`
* SameSite 특성을 생략 하는 새 값을 추가 `SameSiteMode.Unspecified` 합니다.
* 모든 cookie s api의 기본값은 `Unspecified` 입니다. S를 사용 하는 일부 구성 요소는 cookie 해당 시나리오에 보다 구체적인 값을 설정 합니다. 예는 위의 표를 참조 하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 3.0 이상에서 SameSite 기본값은 일관 되지 않은 클라이언트 기본값과 충돌 하지 않도록 변경 되었습니다. 다음 Api는의 기본값을로 변경 `SameSiteMode.Lax ` `-1` 하 여 이러한에 대 한 SameSite 특성을 내보내지 않도록 합니다 cookie .

* <xref:Microsoft.AspNetCore.Http.CookieOptions>[HttpContext 응답과 함께 사용 됩니다. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)
* <xref:Microsoft.AspNetCore.Http.CookieBuilder>  의 팩터리로 사용 됩니다. `CookieOptions`
* [CookiePolicyOptions. MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a>기록 및 변경 내용

SameSite 지원은 [2016 초안 표준을](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)사용 하 여 2.0의 ASP.NET Core에서 처음 구현 되었습니다. 2016 표준은 옵트인 (opt in) 되었습니다. 기본적으로 여러을로 설정 하 여 옵트인 (opt in cookie )을 ASP.NET Core `Lax` 합니다. 인증에 몇 가지 [문제가 발생](https://github.com/aspnet/Announcements/issues/318) 한 후에는 대부분의 SameSite 사용이 [사용 되지 않도록 설정](https://github.com/aspnet/Announcements/issues/348)되었습니다.

11 월 2019에 [패치](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) 를 실행 하 여 2016 표준에서 2019 standard로 업데이트 했습니다. [SameSite 사양의 2019 초안](https://github.com/aspnet/Announcements/issues/390):

* 는 이전 버전과 호환 **되지 않습니다** . 2016 초안. 자세한 내용은이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.
* cookie는 기본적으로로 처리 되는를 지정 합니다 `SameSite=Lax` .
* cookie `SameSite=None` 사이트 간 배달을 가능 하 게 하기 위해 명시적으로 어설션하는를로 표시 합니다 `Secure` . `None` 옵트아웃 (opt out) 할 새 항목입니다.
* 는 ASP.NET Core 2.1, 2.2 및 3.0에 대해 발급 된 패치에 의해 지원 됩니다. ASP.NET Core 3.1에는 추가 SameSite 지원 됩니다.
* 는 기본적으로 [2 월 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)에 [Chrome](https://chromestatus.com/feature/5088147346030592) 에서 사용 하도록 예약 됩니다. 브라우저에서 2019의이 표준으로 이동 하기 시작 했습니다.

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a>2016 SameSite draft standard에서 2019 초안 표준으로 변경 되어 영향을 받는 Api

* [SameSiteMode](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [CookieSameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [CookieSameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [CookiePolicyOptions. MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a>이전 브라우저 지원

2016 SameSite 표준에서는 알 수 없는 값을 값으로 처리 해야 합니다 `SameSite=Strict` . 2016 SameSite 표준을 지 원하는 이전 브라우저에서 액세스 된 앱은 값이 인 SameSite 속성을 가져올 때 손상 될 수 있습니다 `None` . 웹 앱은 이전 브라우저를 지원 하려는 경우 브라우저 검색을 구현 해야 합니다. ASP.NET Core은 User-Agents 값이 매우 휘발성 이며 자주 변경 되기 때문에 브라우저 검색을 구현 하지 않습니다. 의 확장 지점은 User-Agent 특정 논리를 연결 하는 것을 <xref:Microsoft.AspNetCore.CookiePolicy> 허용 합니다.

에서 `Startup.Configure` 를 <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> 호출 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 하거나을 (를) 쓰는 *any* 메서드를 호출 하는 코드를 추가 합니다 cookie .

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

에서 `Startup.ConfigureServices` 다음과 유사한 코드를 추가 합니다.

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

위의 샘플에서는 사용자 `MyUserAgentDetectionLib.DisallowsSameSiteNone` 에이전트가 SameSite을 지원 하지 않는지 검색 하는 사용자 제공 라이브러리입니다 `None` .

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

다음 코드는 샘플 메서드를 보여 줍니다 `DisallowsSameSiteNone` .

> [!WARNING]
> 다음 코드는 데모용 으로만 사용할 수 있습니다.
> * 완료 되지 않은 것으로 간주 됩니다.
> * 유지 관리 되거나 지원 되지 않습니다.

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a>SameSite 문제에 대 한 앱 테스트

타사 로그인을 통해와 같은 원격 사이트와 상호 작용 하는 앱은 다음 작업을 수행 해야 합니다.

* 여러 브라우저에서 상호 작용을 테스트 합니다.
* 이 문서에서 설명 [ Cookie 하는 정책 브라우저 검색 및 완화](#sob) 를 적용 합니다.

새 SameSite 동작을 옵트인 (opt in) 할 수 있는 클라이언트 버전을 사용 하 여 웹 앱을 테스트 합니다. Chrome, Firefox 및 Chromium Edge 모두에는 테스트에 사용할 수 있는 새로운 옵트인 기능 플래그가 있습니다. 앱이 SameSite 패치를 적용 한 후 이전 클라이언트 버전, 특히 Safari를 사용 하 여 테스트 합니다. 자세한 내용은이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.

### <a name="test-with-chrome"></a>Chrome으로 테스트

Chrome 78 +는 일시적인 완화를 제공 하기 때문에 잘못 된 결과를 제공 합니다. Chrome 78 + 임시 완화를 사용 하면 2 분 이내에 s를 사용할 수 있습니다 cookie . 적절 한 테스트 플래그가 설정 된 Chrome 76 또는 77은 보다 정확한 결과를 제공 합니다. 새 SameSite 동작을 테스트 하려면 `chrome://flags/#same-site-by-default-cookies` **사용** 으로 전환 합니다. 이전 버전의 Chrome (75 및 아래)은 새 설정으로 인해 실패 하는 것으로 보고 됩니다 `None` . 이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.

Google은 이전 chrome 버전을 사용할 수 없도록 설정 하지 않습니다. [Chromium 다운로드](https://www.chromium.org/getting-involved/download-chromium) 의 지침에 따라 이전 버전의 Chrome을 테스트 합니다. 이전 버전의 chrome을 검색 하 여 제공 된 링크에서 Chrome을 다운로드 **하지** 마세요.

* [Chromium 76 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [Chromium 74 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

카나리아 버전부터 `80.0.3975.0` 완화 된 + 사후 임시 완화는 새 플래그를 사용 하 여 테스트 목적으로 사용 하지 않도록 설정 하 여 `--enable-features=SameSiteDefaultChecksMethodRigorously` 완화가 제거 된 기능의 최종 종료 상태에서 사이트 및 서비스를 테스트할 수 있습니다. 자세한 내용은 Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site) 를 참조 하세요.

### <a name="test-with-safari"></a>Safari를 사용 하 여 테스트

Safari 12는 이전 초안을 엄격 하 게 구현 했으며 새 `None` 값이에 있는 경우 실패 합니다 cookie . `None` 이 문서에서 [이전 브라우저를 지 원하는](#sob) 브라우저 검색 코드를 통해가 방지 됩니다. MSAL, ADAL 또는 사용 중인 라이브러리를 사용 하 여 Safari 12, Safari 13 및 WebKit 기반 OS 스타일 로그인을 테스트 합니다. 문제는 기본 OS 버전에 따라 달라집니다. OSX Mojave (10.14) 및 iOS 12는 새로운 SameSite 동작의 호환성 문제를 해결 하는 것으로 알려져 있습니다. OS를 OSX Catalina.properties (10.15) 또는 iOS 13로 업그레이드 하면 문제가 해결 됩니다. Safari에는 현재 새 사양 동작 테스트를 위한 옵트인 플래그가 없습니다.

### <a name="test-with-firefox"></a>Firefox로 테스트

새 표준에 대 한 Firefox 지원은 기능 플래그를 사용 하 여 페이지에서 옵트인 하 여 버전 68 이상에서 테스트할 수 있습니다 `about:config` `network.cookie.sameSite.laxByDefault` . 이전 버전의 Firefox와의 호환성 문제에 대 한 보고서가 없습니다.

### <a name="test-with-edge-browser"></a>Edge 브라우저를 사용 하 여 테스트

Edge는 이전 SameSite 표준을 지원 합니다. Edge 버전 44에는 새로운 표준과의 알려진 호환성 문제가 없습니다.

### <a name="test-with-edge-chromium"></a>Edge를 사용 하 여 테스트 (Chromium)

SameSite 플래그는 페이지에 설정 되어 `edge://flags/#same-site-by-default-cookies` 있습니다. Edge Chromium에서 호환성 문제가 검색 되지 않았습니다.

### <a name="test-with-no-locelectron"></a>테스트 Electron

버전에는 Electron 이전 버전의 Chromium가 포함 되어 있습니다. 예를 들어 팀에서 사용 하는의 버전은 Electron Chromium 66 이며,이는 이전 동작을 보여 주는 것입니다. 제품 버전을 사용 하 여 사용자 고유의 호환성 테스트를 수행 해야 합니다 Electron . 다음 섹션에서 [이전 브라우저 지원](#sob) 을 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* [Chromium 블로그: 개발자: 새 SameSite를 사용할 준비가 되었습니다. 보안 Cookie 설정](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [SameSite cookie s 설명](https://web.dev/samesite-cookies-explained/)
* [11 월 2019 패치](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| 예제               | 문서 |
| ----------------- | ------------ |
| [.NET Core MVC](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [.NET Core Razor 페이지](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| 예제               | 문서 |
| ----------------- | ------------ |
| [.NET Core Razor 페이지](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end
