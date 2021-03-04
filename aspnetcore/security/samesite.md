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
ms.openlocfilehash: e06c73edfc999053e0aa37f05d984a2b428f69a9
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109938"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="94967-103">cookieASP.NET Core에서 SameSite s 작업</span><span class="sxs-lookup"><span data-stu-id="94967-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="94967-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="94967-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="94967-105">SameSite은 CSRF (교차 사이트 요청 위조) 공격에 대 한 보호를 제공 하도록 설계 된 [IETF](https://ietf.org/about/) 초안 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="94967-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="94967-106">원래 [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)에서는 초안 표준이 [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)에서 업데이트 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="94967-107">업데이트 된 표준은 이전 표준과 호환 되지 않으며 다음과 같은 가장 눈에 띄는 차이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="94967-108">CookieSameSite 헤더가 없는 s는 기본적으로로 처리 됩니다 `SameSite=Lax` .</span><span class="sxs-lookup"><span data-stu-id="94967-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="94967-109">`SameSite=None` 는 사이트 간 사용을 허용 하는 데 사용 해야 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="94967-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="94967-110">Cookieassert `SameSite=None` 도로 표시 해야 합니다 `Secure` .</span><span class="sxs-lookup"><span data-stu-id="94967-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="94967-111">를 사용 하는 응용 프로그램 [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) `sameSite=Lax` `sameSite=Strict` cookie `<iframe>` 은 사이트 간 시나리오로 처리 되기 때문에 또는와 관련 된 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="94967-112">값은 `SameSite=None` [2016 표준](https://tools.ietf.org/html/draft-west-first-party-cookies-07) 에서 허용 되지 않으며 일부 구현에서 이러한를 cookie 로 처리 `SameSite=Strict` 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="94967-113">이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="94967-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="94967-114">이 `SameSite=Lax` 설정은 대부분의 응용 프로그램에서 작동 cookie 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="94967-115">Oidc ( [Openid connect Connect](https://openid.net/connect/) )와 같은 일부 형태의 인증 및 [ws-federation](https://auth0.com/docs/protocols/ws-fed) 은 게시 기반 리디렉션에 대해 기본적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="94967-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="94967-116">사후 기반 리디렉션은 SameSite 브라우저 보호를 트리거하고 이러한 구성 요소에 대해 SameSite을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="94967-117">대부분의 [OAuth](https://oauth.net/) 로그인은 요청 흐름의 차이로 인해 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="94967-118">를 내보내는 각 ASP.NET Core 구성 요소는 cookie SameSite가 적절 한지 결정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-and-identity"></a><span data-ttu-id="94967-119">SameSite 및 Identity</span><span class="sxs-lookup"><span data-stu-id="94967-119">SameSite and Identity</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a><span data-ttu-id="94967-120">SameSite 테스트 샘플 코드</span><span class="sxs-lookup"><span data-stu-id="94967-120">SameSite test sample code</span></span>

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="94967-121">다음 샘플을 다운로드 하 고 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-121">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="94967-122">샘플</span><span class="sxs-lookup"><span data-stu-id="94967-122">Sample</span></span>               | <span data-ttu-id="94967-123">문서</span><span class="sxs-lookup"><span data-stu-id="94967-123">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="94967-124">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="94967-124">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="94967-125">[.NET Core Razor 페이지](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="94967-125">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="94967-126">다음 샘플을 다운로드 하 고 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-126">The following sample can be downloaded and tested:</span></span>

| <span data-ttu-id="94967-127">샘플</span><span class="sxs-lookup"><span data-stu-id="94967-127">Sample</span></span>               | <span data-ttu-id="94967-128">문서</span><span class="sxs-lookup"><span data-stu-id="94967-128">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="94967-129">[.NET Core Razor 페이지](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="94967-129">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="94967-130">SameSite 특성에 대 한 .NET Core 지원</span><span class="sxs-lookup"><span data-stu-id="94967-130">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="94967-131">.NET Core 2.2 이상에서는 12 월 2019의 업데이트 출시 이후 SameSite의 2019 초안 표준을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-131">.NET Core 2.2 and later support the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="94967-132">개발자는 속성을 사용 하 여 sameSite 특성의 값을 프로그래밍 방식으로 제어할 수 있습니다 `HttpCookie.SameSite` .</span><span class="sxs-lookup"><span data-stu-id="94967-132">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="94967-133">속성을 `SameSite` Strict, Strict 또는 None으로 설정 하면 해당 값이로 네트워크에 기록 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="94967-133">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="94967-134">이 값을로 설정 `(SameSiteMode)(-1)` 하면 네트워크에 sameSite 특성이 포함 되지 않음을 나타냅니다. cookie</span><span class="sxs-lookup"><span data-stu-id="94967-134">Setting it equal to `(SameSiteMode)(-1)` indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="94967-135">.NET Core 3.0 이상에서는 업데이트 된 SameSite 값을 지원 하 고 열거형에 추가 열거형 값을 추가 합니다 `SameSiteMode.Unspecified` `SameSiteMode` .</span><span class="sxs-lookup"><span data-stu-id="94967-135">.NET Core 3.0 and later support the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="94967-136">이 새 값은를 사용 하 여 sameSite를 전송 하지 않음을 나타냅니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="94967-136">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="94967-137">12 월 패치 동작 변경 내용</span><span class="sxs-lookup"><span data-stu-id="94967-137">December patch behavior changes</span></span>

<span data-ttu-id="94967-138">.NET Framework 및 .NET Core 2.1의 특정 동작 변경 내용은 `SameSite` 속성이 값을 해석 하는 방법입니다 `None` .</span><span class="sxs-lookup"><span data-stu-id="94967-138">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="94967-139">패치 후의 값이 " `None` 특성을 전혀 내보내지 않습니다." 라는 값을 패치 한 후에는 "값을 사용 하 여 특성을 내보냅니다." 라는 의미입니다 `None` .</span><span class="sxs-lookup"><span data-stu-id="94967-139">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="94967-140">패치를 값으로 `SameSite` 설정 하면 `(SameSiteMode)(-1)` 특성을 내보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-140">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="94967-141">폼 인증 및 세션 상태의 기본 SameSite 값 cookie 이에서로 변경 되었습니다 `None` `Lax` .</span><span class="sxs-lookup"><span data-stu-id="94967-141">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="94967-142">SameSite를 사용 하는 API 사용</span><span class="sxs-lookup"><span data-stu-id="94967-142">API usage with SameSite</span></span>

<span data-ttu-id="94967-143">[HttpContext 응답입니다. Cookie s.](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) 의 기본값은에 `Unspecified` 추가 됩니다. 즉,에 SameSite 특성이 추가 되지 않고 cookie 클라이언트는 기본 동작을 사용 합니다 (새 브라우저의 경우는 안 되며 이전 버전의 경우에는 없음).</span><span class="sxs-lookup"><span data-stu-id="94967-143">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="94967-144">다음 코드에서는 cookie SameSite 값을로 변경 하는 방법을 보여 줍니다 `SameSiteMode.Lax` .</span><span class="sxs-lookup"><span data-stu-id="94967-144">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="94967-145">S를 내보내는 모든 ASP.NET Core 구성 요소는 cookie 해당 시나리오에 적합 한 설정을 사용 하 여 이전 기본값을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-145">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="94967-146">재정의 된 이전 기본값은 변경 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-146">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="94967-147">구성 요소</span><span class="sxs-lookup"><span data-stu-id="94967-147">Component</span></span> | cookie | <span data-ttu-id="94967-148">기본값</span><span class="sxs-lookup"><span data-stu-id="94967-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | <span data-ttu-id="94967-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="94967-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span></span> |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | <span data-ttu-id="94967-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="94967-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span></span> | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | <span data-ttu-id="94967-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="94967-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span></span>| `Strict` |
| <span data-ttu-id="94967-152">[Cookie 인증은](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span><span class="sxs-lookup"><span data-stu-id="94967-152">[Cookie Authentication](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span></span> | <span data-ttu-id="94967-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span><span class="sxs-lookup"><span data-stu-id="94967-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span></span> | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | <span data-ttu-id="94967-154">[TwitterOptions. 상태 Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span><span class="sxs-lookup"><span data-stu-id="94967-154">[TwitterOptions.StateCookie ](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span></span> | `Lax`  |
| <span data-ttu-id="94967-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. 상관 관계Cookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="94967-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | <span data-ttu-id="94967-156">[OpenIdConnectOptions. NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span><span class="sxs-lookup"><span data-stu-id="94967-156">[OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span></span>| `None` |
| <span data-ttu-id="94967-157">[HttpContext 응답입니다. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="94967-157">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span> | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="94967-158">ASP.NET Core 3.1 이상에서는 다음 SameSite 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="94967-159">내보내기에 대 한 동작을 다시 정의 합니다. `SameSiteMode.None``SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="94967-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="94967-160">SameSite 특성을 생략 하는 새 값을 추가 `SameSiteMode.Unspecified` 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="94967-161">모든 cookie s api의 기본값은 `Unspecified` 입니다.</span><span class="sxs-lookup"><span data-stu-id="94967-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="94967-162">S를 사용 하는 일부 구성 요소는 cookie 해당 시나리오에 보다 구체적인 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="94967-163">예는 위의 표를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="94967-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="94967-164">ASP.NET Core 3.0 이상에서 SameSite 기본값은 일관 되지 않은 클라이언트 기본값과 충돌 하지 않도록 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="94967-165">다음 Api는의 기본값을로 변경 `SameSiteMode.Lax ` `-1` 하 여 이러한에 대 한 SameSite 특성을 내보내지 않도록 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="94967-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="94967-166"><xref:Microsoft.AspNetCore.Http.CookieOptions>[HttpContext 응답과 함께 사용 됩니다. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="94967-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="94967-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  의 팩터리로 사용 됩니다. `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="94967-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* <span data-ttu-id="94967-168">[CookiePolicyOptions. MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="94967-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="94967-169">기록 및 변경 내용</span><span class="sxs-lookup"><span data-stu-id="94967-169">History and changes</span></span>

<span data-ttu-id="94967-170">SameSite 지원은 [2016 초안 표준을](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)사용 하 여 2.0의 ASP.NET Core에서 처음 구현 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="94967-171">2016 표준은 옵트인 (opt in) 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="94967-172">기본적으로 여러을로 설정 하 여 옵트인 (opt in cookie )을 ASP.NET Core `Lax` 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="94967-173">인증에 몇 가지 [문제가 발생](https://github.com/aspnet/Announcements/issues/318) 한 후에는 대부분의 SameSite 사용이 [사용 되지 않도록 설정](https://github.com/aspnet/Announcements/issues/348)되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="94967-174">11 월 2019에 [패치](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) 를 실행 하 여 2016 표준에서 2019 standard로 업데이트 했습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="94967-175">[SameSite 사양의 2019 초안](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="94967-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="94967-176">는 이전 버전과 호환 **되지 않습니다** . 2016 초안.</span><span class="sxs-lookup"><span data-stu-id="94967-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="94967-177">자세한 내용은이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="94967-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="94967-178">cookie는 기본적으로로 처리 되는를 지정 합니다 `SameSite=Lax` .</span><span class="sxs-lookup"><span data-stu-id="94967-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="94967-179">cookie `SameSite=None` 사이트 간 배달을 가능 하 게 하기 위해 명시적으로 어설션하는를로 표시 합니다 `Secure` .</span><span class="sxs-lookup"><span data-stu-id="94967-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="94967-180">`None` 옵트아웃 (opt out) 할 새 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="94967-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="94967-181">는 ASP.NET Core 2.1, 2.2 및 3.0에 대해 발급 된 패치에 의해 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="94967-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="94967-182">ASP.NET Core 3.1에는 추가 SameSite 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="94967-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="94967-183">는 기본적으로 [2 월 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)에 [Chrome](https://chromestatus.com/feature/5088147346030592) 에서 사용 하도록 예약 됩니다.</span><span class="sxs-lookup"><span data-stu-id="94967-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="94967-184">브라우저에서 2019의이 표준으로 이동 하기 시작 했습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="94967-185">2016 SameSite draft standard에서 2019 초안 표준으로 변경 되어 영향을 받는 Api</span><span class="sxs-lookup"><span data-stu-id="94967-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="94967-186">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="94967-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* <span data-ttu-id="94967-187">[CookieSameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span><span class="sxs-lookup"><span data-stu-id="94967-187">[CookieOptions.SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span></span>
* <span data-ttu-id="94967-188">[CookieSameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span><span class="sxs-lookup"><span data-stu-id="94967-188">[CookieBuilder.SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span></span>
* <span data-ttu-id="94967-189">[CookiePolicyOptions. MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="94967-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="94967-190">이전 브라우저 지원</span><span class="sxs-lookup"><span data-stu-id="94967-190">Supporting older browsers</span></span>

<span data-ttu-id="94967-191">2016 SameSite 표준에서는 알 수 없는 값을 값으로 처리 해야 합니다 `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="94967-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="94967-192">2016 SameSite 표준을 지 원하는 이전 브라우저에서 액세스 된 앱은 값이 인 SameSite 속성을 가져올 때 손상 될 수 있습니다 `None` .</span><span class="sxs-lookup"><span data-stu-id="94967-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="94967-193">웹 앱은 이전 브라우저를 지원 하려는 경우 브라우저 검색을 구현 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="94967-194">ASP.NET Core은 User-Agents 값이 매우 휘발성 이며 자주 변경 되기 때문에 브라우저 검색을 구현 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="94967-195">의 확장 지점은 User-Agent 특정 논리를 연결 하는 것을 <xref:Microsoft.AspNetCore.CookiePolicy> 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="94967-196">에서 `Startup.Configure` 를 <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> 호출 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 하거나을 (를) 쓰는  메서드를 호출 하는 코드를 추가 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="94967-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="94967-197">에서 `Startup.ConfigureServices` 다음과 유사한 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range=">= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="94967-198">위의 샘플에서는 사용자 `MyUserAgentDetectionLib.DisallowsSameSiteNone` 에이전트가 SameSite을 지원 하지 않는지 검색 하는 사용자 제공 라이브러리입니다 `None` .</span><span class="sxs-lookup"><span data-stu-id="94967-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="94967-199">다음 코드는 샘플 메서드를 보여 줍니다 `DisallowsSameSiteNone` .</span><span class="sxs-lookup"><span data-stu-id="94967-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="94967-200">다음 코드는 데모용 으로만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="94967-201">완료 되지 않은 것으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="94967-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="94967-202">유지 관리 되거나 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="94967-203">SameSite 문제에 대 한 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="94967-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="94967-204">타사 로그인을 통해와 같은 원격 사이트와 상호 작용 하는 앱은 다음 작업을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="94967-205">여러 브라우저에서 상호 작용을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="94967-206">이 문서에서 설명 [ Cookie 하는 정책 브라우저 검색 및 완화](#sob) 를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="94967-207">새 SameSite 동작을 옵트인 (opt in) 할 수 있는 클라이언트 버전을 사용 하 여 웹 앱을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="94967-208">Chrome, Firefox 및 Chromium Edge 모두에는 테스트에 사용할 수 있는 새로운 옵트인 기능 플래그가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="94967-209">앱이 SameSite 패치를 적용 한 후 이전 클라이언트 버전, 특히 Safari를 사용 하 여 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="94967-210">자세한 내용은이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="94967-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="94967-211">Chrome으로 테스트</span><span class="sxs-lookup"><span data-stu-id="94967-211">Test with Chrome</span></span>

<span data-ttu-id="94967-212">Chrome 78 +는 일시적인 완화를 제공 하기 때문에 잘못 된 결과를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="94967-213">Chrome 78 + 임시 완화를 사용 하면 2 분 이내에 s를 사용할 수 있습니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="94967-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="94967-214">적절 한 테스트 플래그가 설정 된 Chrome 76 또는 77은 보다 정확한 결과를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="94967-215">새 SameSite 동작을 테스트 하려면 `chrome://flags/#same-site-by-default-cookies` **사용** 으로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="94967-216">이전 버전의 Chrome (75 및 아래)은 새 설정으로 인해 실패 하는 것으로 보고 됩니다 `None` .</span><span class="sxs-lookup"><span data-stu-id="94967-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="94967-217">이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="94967-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="94967-218">Google은 이전 chrome 버전을 사용할 수 없도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="94967-219">[Chromium 다운로드](https://www.chromium.org/getting-involved/download-chromium) 의 지침에 따라 이전 버전의 Chrome을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="94967-220">이전 버전의 chrome을 검색 하 여 제공 된 링크에서 Chrome을 다운로드 **하지** 마세요.</span><span class="sxs-lookup"><span data-stu-id="94967-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="94967-221">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="94967-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="94967-222">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="94967-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="94967-223">카나리아 버전부터 `80.0.3975.0` 완화 된 + 사후 임시 완화는 새 플래그를 사용 하 여 테스트 목적으로 사용 하지 않도록 설정 하 여 `--enable-features=SameSiteDefaultChecksMethodRigorously` 완화가 제거 된 기능의 최종 종료 상태에서 사이트 및 서비스를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="94967-224">자세한 내용은 Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="94967-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="94967-225">Safari를 사용 하 여 테스트</span><span class="sxs-lookup"><span data-stu-id="94967-225">Test with Safari</span></span>

<span data-ttu-id="94967-226">Safari 12는 이전 초안을 엄격 하 게 구현 했으며 새 `None` 값이에 있는 경우 실패 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="94967-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="94967-227">`None` 이 문서에서 [이전 브라우저를 지 원하는](#sob) 브라우저 검색 코드를 통해가 방지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="94967-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="94967-228">MSAL, ADAL 또는 사용 중인 라이브러리를 사용 하 여 Safari 12, Safari 13 및 WebKit 기반 OS 스타일 로그인을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="94967-229">문제는 기본 OS 버전에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="94967-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="94967-230">OSX Mojave (10.14) 및 iOS 12는 새로운 SameSite 동작의 호환성 문제를 해결 하는 것으로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="94967-231">OS를 OSX Catalina.properties (10.15) 또는 iOS 13로 업그레이드 하면 문제가 해결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="94967-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="94967-232">Safari에는 현재 새 사양 동작 테스트를 위한 옵트인 플래그가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="94967-233">Firefox로 테스트</span><span class="sxs-lookup"><span data-stu-id="94967-233">Test with Firefox</span></span>

<span data-ttu-id="94967-234">새 표준에 대 한 Firefox 지원은 기능 플래그를 사용 하 여 페이지에서 옵트인 하 여 버전 68 이상에서 테스트할 수 있습니다 `about:config` `network.cookie.sameSite.laxByDefault` .</span><span class="sxs-lookup"><span data-stu-id="94967-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="94967-235">이전 버전의 Firefox와의 호환성 문제에 대 한 보고서가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="94967-236">Edge 브라우저를 사용 하 여 테스트</span><span class="sxs-lookup"><span data-stu-id="94967-236">Test with Edge browser</span></span>

<span data-ttu-id="94967-237">Edge는 이전 SameSite 표준을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="94967-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="94967-238">Edge 버전 44에는 새로운 표준과의 알려진 호환성 문제가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="94967-239">Edge를 사용 하 여 테스트 (Chromium)</span><span class="sxs-lookup"><span data-stu-id="94967-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="94967-240">SameSite 플래그는 페이지에 설정 되어 `edge://flags/#same-site-by-default-cookies` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="94967-241">Edge Chromium에서 호환성 문제가 검색 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="94967-242">테스트 Electron</span><span class="sxs-lookup"><span data-stu-id="94967-242">Test with Electron</span></span>

<span data-ttu-id="94967-243">버전에는 Electron 이전 버전의 Chromium가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94967-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="94967-244">예를 들어 팀에서 사용 하는의 버전은 Electron Chromium 66 이며,이는 이전 동작을 보여 주는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="94967-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="94967-245">제품 버전을 사용 하 여 사용자 고유의 호환성 테스트를 수행 해야 합니다 Electron .</span><span class="sxs-lookup"><span data-stu-id="94967-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="94967-246">다음 섹션에서 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="94967-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94967-247">추가 자료</span><span class="sxs-lookup"><span data-stu-id="94967-247">Additional resources</span></span>

* [<span data-ttu-id="94967-248">Chromium 블로그: 개발자: 새 SameSite를 사용할 준비가 되었습니다. 보안 Cookie 설정</span><span class="sxs-lookup"><span data-stu-id="94967-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* <span data-ttu-id="94967-249">[SameSite cookie s 설명](https://web.dev/samesite-cookies-explained/)</span><span class="sxs-lookup"><span data-stu-id="94967-249">[SameSite cookies explained](https://web.dev/samesite-cookies-explained/)</span></span>
* [<span data-ttu-id="94967-250">11 월 2019 패치</span><span class="sxs-lookup"><span data-stu-id="94967-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="94967-251">샘플</span><span class="sxs-lookup"><span data-stu-id="94967-251">Sample</span></span>               | <span data-ttu-id="94967-252">문서</span><span class="sxs-lookup"><span data-stu-id="94967-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="94967-253">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="94967-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="94967-254">[.NET Core Razor 페이지](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="94967-254">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="94967-255">샘플</span><span class="sxs-lookup"><span data-stu-id="94967-255">Sample</span></span>               | <span data-ttu-id="94967-256">문서</span><span class="sxs-lookup"><span data-stu-id="94967-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="94967-257">[.NET Core Razor 페이지](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="94967-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
