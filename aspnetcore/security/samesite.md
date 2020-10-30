---
title: :::no-loc(cookie):::ASP.NET Core에서 SameSite s 작업
author: rick-anderson
description: '을 사용 하 여 ASP.NET Core를 SameSite 하는 방법을 알아봅니다. :::no-loc(cookie):::'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
- ':::no-loc(Electron):::'
uid: security/samesite
ms.openlocfilehash: 6f826416e3045df32abf41e94e667120e71ae717
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051618"
---
# <a name="work-with-samesite-no-loccookies-in-aspnet-core"></a><span data-ttu-id="fc786-103">:::no-loc(cookie):::ASP.NET Core에서 SameSite s 작업</span><span class="sxs-lookup"><span data-stu-id="fc786-103">Work with SameSite :::no-loc(cookie):::s in ASP.NET Core</span></span>

<span data-ttu-id="fc786-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fc786-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fc786-105">SameSite은 CSRF (교차 사이트 요청 위조) 공격에 대 한 보호를 제공 하도록 설계 된 [IETF](https://ietf.org/about/) 초안 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="fc786-106">원래 [2016](https://tools.ietf.org/html/draft-west-first-party-:::no-loc(cookie):::s-07)에서는 초안 표준이 [2019](https://tools.ietf.org/html/draft-west-:::no-loc(cookie):::-incrementalism-00)에서 업데이트 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-:::no-loc(cookie):::s-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-:::no-loc(cookie):::-incrementalism-00).</span></span> <span data-ttu-id="fc786-107">업데이트 된 표준은 이전 표준과 호환 되지 않으며 다음과 같은 가장 눈에 띄는 차이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="fc786-108">:::no-loc(Cookie):::SameSite 헤더가 없는 s는 기본적으로로 처리 됩니다 `SameSite=Lax` .</span><span class="sxs-lookup"><span data-stu-id="fc786-108">:::no-loc(Cookie):::s without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="fc786-109">`SameSite=None` 는 사이트 간 사용을 허용 하는 데 사용 해야 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="fc786-109">`SameSite=None` must be used to allow cross-site :::no-loc(cookie)::: use.</span></span>
* <span data-ttu-id="fc786-110">:::no-loc(Cookie):::assert `SameSite=None` 도로 표시 해야 합니다 `Secure` .</span><span class="sxs-lookup"><span data-stu-id="fc786-110">:::no-loc(Cookie):::s that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="fc786-111">를 사용 하는 응용 프로그램 [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) `sameSite=Lax` `sameSite=Strict` :::no-loc(cookie)::: `<iframe>` 은 사이트 간 시나리오로 처리 되기 때문에 또는와 관련 된 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` :::no-loc(cookie):::s because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="fc786-112">값은 `SameSite=None` [2016 표준](https://tools.ietf.org/html/draft-west-first-party-:::no-loc(cookie):::s-07) 에서 허용 되지 않으며 일부 구현에서 이러한를 :::no-loc(cookie)::: 로 처리 `SameSite=Strict` 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-:::no-loc(cookie):::s-07) and causes some implementations to treat such :::no-loc(cookie):::s as `SameSite=Strict`.</span></span> <span data-ttu-id="fc786-113">이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc786-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="fc786-114">이 `SameSite=Lax` 설정은 대부분의 응용 프로그램에서 작동 :::no-loc(cookie)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-114">The `SameSite=Lax` setting works for most application :::no-loc(cookie):::s.</span></span> <span data-ttu-id="fc786-115">Oidc ( [Openid connect Connect](https://openid.net/connect/) )와 같은 일부 형태의 인증 및 [ws-federation](https://auth0.com/docs/protocols/ws-fed) 은 게시 기반 리디렉션에 대해 기본적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="fc786-116">사후 기반 리디렉션은 SameSite 브라우저 보호를 트리거하고 이러한 구성 요소에 대해 SameSite을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="fc786-117">대부분의 [OAuth](https://oauth.net/) 로그인은 요청 흐름의 차이로 인해 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="fc786-118">를 내보내는 각 ASP.NET Core 구성 요소는 :::no-loc(cookie)::: SameSite가 적절 한지 결정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-118">Each ASP.NET Core component that emits :::no-loc(cookie):::s needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-and-no-locidentity"></a><span data-ttu-id="fc786-119">SameSite 및 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="fc786-119">SameSite and :::no-loc(Identity):::</span></span>

[!INCLUDE[](~/includes/SameSite:::no-loc(Identity):::.md)]

## <a name="samesite-test-sample-code"></a><span data-ttu-id="fc786-120">SameSite 테스트 샘플 코드</span><span class="sxs-lookup"><span data-stu-id="fc786-120">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="fc786-121">다음 샘플을 다운로드 하 고 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-121">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="fc786-122">예제</span><span class="sxs-lookup"><span data-stu-id="fc786-122">Sample</span></span>               | <span data-ttu-id="fc786-123">문서</span><span class="sxs-lookup"><span data-stu-id="fc786-123">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="fc786-124">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="fc786-124">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="fc786-125">[.NET Core :::no-loc(Razor)::: 페이지](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21:::no-loc(Razor):::Pages)</span><span class="sxs-lookup"><span data-stu-id="fc786-125">[.NET Core :::no-loc(Razor)::: Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21:::no-loc(Razor):::Pages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fc786-126">다음 샘플을 다운로드 하 고 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-126">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="fc786-127">예제</span><span class="sxs-lookup"><span data-stu-id="fc786-127">Sample</span></span>               | <span data-ttu-id="fc786-128">문서</span><span class="sxs-lookup"><span data-stu-id="fc786-128">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="fc786-129">[.NET Core :::no-loc(Razor)::: 페이지](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31:::no-loc(Razor):::Pages)</span><span class="sxs-lookup"><span data-stu-id="fc786-129">[.NET Core :::no-loc(Razor)::: Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31:::no-loc(Razor):::Pages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="fc786-130">SameSite 특성에 대 한 .NET Core 지원</span><span class="sxs-lookup"><span data-stu-id="fc786-130">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="fc786-131">.NET Core 2.2 이상에서는 12 월 2019의 업데이트 출시 이후 SameSite의 2019 초안 표준을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-131">.NET Core 2.2 and later support the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="fc786-132">개발자는 속성을 사용 하 여 sameSite 특성의 값을 프로그래밍 방식으로 제어할 수 있습니다 `Http:::no-loc(Cookie):::.SameSite` .</span><span class="sxs-lookup"><span data-stu-id="fc786-132">Developers are able to programmatically control the value of the sameSite attribute using the `Http:::no-loc(Cookie):::.SameSite` property.</span></span> <span data-ttu-id="fc786-133">속성을 `SameSite` Strict, Strict 또는 None으로 설정 하면 해당 값이로 네트워크에 기록 됩니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="fc786-133">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the :::no-loc(cookie):::.</span></span> <span data-ttu-id="fc786-134">이 값을로 설정 `(SameSiteMode)(-1)` 하면 네트워크에 sameSite 특성이 포함 되지 않음을 나타냅니다. :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="fc786-134">Setting it equal to `(SameSiteMode)(-1)` indicates that no sameSite attribute should be included on the network with the :::no-loc(cookie):::</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="fc786-135">.NET Core 3.0 이상에서는 업데이트 된 SameSite 값을 지원 하 고 열거형에 추가 열거형 값을 추가 합니다 `SameSiteMode.Unspecified` `SameSiteMode` .</span><span class="sxs-lookup"><span data-stu-id="fc786-135">.NET Core 3.0 and later support the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="fc786-136">이 새 값은를 사용 하 여 sameSite를 전송 하지 않음을 나타냅니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="fc786-136">This new value indicates no sameSite should be sent with the :::no-loc(cookie):::.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="fc786-137">12 월 패치 동작 변경 내용</span><span class="sxs-lookup"><span data-stu-id="fc786-137">December patch behavior changes</span></span>

<span data-ttu-id="fc786-138">.NET Framework 및 .NET Core 2.1의 특정 동작 변경 내용은 `SameSite` 속성이 값을 해석 하는 방법입니다 `None` .</span><span class="sxs-lookup"><span data-stu-id="fc786-138">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="fc786-139">패치 후의 값이 " `None` 특성을 전혀 내보내지 않습니다." 라는 값을 패치 한 후에는 "값을 사용 하 여 특성을 내보냅니다." 라는 의미입니다 `None` .</span><span class="sxs-lookup"><span data-stu-id="fc786-139">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="fc786-140">패치를 값으로 `SameSite` 설정 하면 `(SameSiteMode)(-1)` 특성을 내보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-140">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="fc786-141">폼 인증 및 세션 상태의 기본 SameSite 값 :::no-loc(cookie)::: 이에서로 변경 되었습니다 `None` `Lax` .</span><span class="sxs-lookup"><span data-stu-id="fc786-141">The default SameSite value for forms authentication and session state :::no-loc(cookie):::s was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="fc786-142">SameSite를 사용 하는 API 사용</span><span class="sxs-lookup"><span data-stu-id="fc786-142">API usage with SameSite</span></span>

<span data-ttu-id="fc786-143">[HttpContext 응답입니다. :::no-loc(Cookie)::: s.](xref:Microsoft.AspNetCore.Http.IResponse:::no-loc(Cookie):::s.Append*) 의 기본값은에 `Unspecified` 추가 됩니다. 즉,에 SameSite 특성이 추가 되지 않고 :::no-loc(cookie)::: 클라이언트는 기본 동작을 사용 합니다 (새 브라우저의 경우는 안 되며 이전 버전의 경우에는 없음).</span><span class="sxs-lookup"><span data-stu-id="fc786-143">[HttpContext.Response.:::no-loc(Cookie):::s.Append](xref:Microsoft.AspNetCore.Http.IResponse:::no-loc(Cookie):::s.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the :::no-loc(cookie)::: and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="fc786-144">다음 코드에서는 :::no-loc(cookie)::: SameSite 값을로 변경 하는 방법을 보여 줍니다 `SameSiteMode.Lax` .</span><span class="sxs-lookup"><span data-stu-id="fc786-144">The following code shows how to change the :::no-loc(cookie)::: SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="fc786-145">S를 내보내는 모든 ASP.NET Core 구성 요소는 :::no-loc(cookie)::: 해당 시나리오에 적합 한 설정을 사용 하 여 이전 기본값을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-145">All ASP.NET Core components that emit :::no-loc(cookie):::s override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="fc786-146">재정의 된 이전 기본값은 변경 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-146">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="fc786-147">구성 요소</span><span class="sxs-lookup"><span data-stu-id="fc786-147">Component</span></span> | :::no-loc(cookie)::: | <span data-ttu-id="fc786-148">기본값</span><span class="sxs-lookup"><span data-stu-id="fc786-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder> | <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | <span data-ttu-id="fc786-149">[SessionOptions.:::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Builder.SessionOptions.:::no-loc(Cookie):::)</span><span class="sxs-lookup"><span data-stu-id="fc786-149">[SessionOptions.:::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Builder.SessionOptions.:::no-loc(Cookie):::)</span></span> |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.:::no-loc(Cookie):::TempDataProvider>  | <span data-ttu-id="fc786-150">[:::no-loc(Cookie):::TempDataProviderOptions.:::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Mvc.:::no-loc(Cookie):::TempDataProviderOptions.:::no-loc(Cookie):::)</span><span class="sxs-lookup"><span data-stu-id="fc786-150">[:::no-loc(Cookie):::TempDataProviderOptions.:::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Mvc.:::no-loc(Cookie):::TempDataProviderOptions.:::no-loc(Cookie):::)</span></span> | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | <span data-ttu-id="fc786-151">[AntiforgeryOptions.:::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.:::no-loc(Cookie):::)</span><span class="sxs-lookup"><span data-stu-id="fc786-151">[AntiforgeryOptions.:::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.:::no-loc(Cookie):::)</span></span>| `Strict` |
| <span data-ttu-id="fc786-152">[:::no-loc(Cookie)::: 인증은](xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*)</span><span class="sxs-lookup"><span data-stu-id="fc786-152">[:::no-loc(Cookie)::: Authentication](xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*)</span></span> | <span data-ttu-id="fc786-153">[:::no-loc(Cookie):::AuthenticationOptions.:::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.:::no-loc(Cookie):::Name)</span><span class="sxs-lookup"><span data-stu-id="fc786-153">[:::no-loc(Cookie):::AuthenticationOptions.:::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.:::no-loc(Cookie):::Name)</span></span> | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | <span data-ttu-id="fc786-154">[TwitterOptions. 상태 :::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.State:::no-loc(Cookie):::)</span><span class="sxs-lookup"><span data-stu-id="fc786-154">[TwitterOptions.State:::no-loc(Cookie)::: ](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.State:::no-loc(Cookie):::)</span></span> | `Lax`  |
| <span data-ttu-id="fc786-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. 상관 관계:::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.Correlation:::no-loc(Cookie):::)  | `None`</span><span class="sxs-lookup"><span data-stu-id="fc786-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.Correlation:::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.Correlation:::no-loc(Cookie):::)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | <span data-ttu-id="fc786-156">[OpenIdConnectOptions. Nonce:::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.Nonce:::no-loc(Cookie):::)</span><span class="sxs-lookup"><span data-stu-id="fc786-156">[OpenIdConnectOptions.Nonce:::no-loc(Cookie):::](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.Nonce:::no-loc(Cookie):::)</span></span>| `None` |
| <span data-ttu-id="fc786-157">[HttpContext 응답입니다. :::no-loc(Cookie)::: s. Append](xref:Microsoft.AspNetCore.Http.IResponse:::no-loc(Cookie):::s.Append*)</span><span class="sxs-lookup"><span data-stu-id="fc786-157">[HttpContext.Response.:::no-loc(Cookie):::s.Append](xref:Microsoft.AspNetCore.Http.IResponse:::no-loc(Cookie):::s.Append*)</span></span> | <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Options> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="fc786-158">ASP.NET Core 3.1 이상에서는 다음 SameSite 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="fc786-159">내보내기에 대 한 동작을 다시 정의 합니다. `SameSiteMode.None``SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="fc786-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="fc786-160">SameSite 특성을 생략 하는 새 값을 추가 `SameSiteMode.Unspecified` 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="fc786-161">모든 :::no-loc(cookie)::: s api의 기본값은 `Unspecified` 입니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-161">All :::no-loc(cookie):::s APIs default to `Unspecified`.</span></span> <span data-ttu-id="fc786-162">S를 사용 하는 일부 구성 요소는 :::no-loc(cookie)::: 해당 시나리오에 보다 구체적인 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-162">Some components that use :::no-loc(cookie):::s set values more specific to their scenarios.</span></span> <span data-ttu-id="fc786-163">예는 위의 표를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc786-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fc786-164">ASP.NET Core 3.0 이상에서 SameSite 기본값은 일관 되지 않은 클라이언트 기본값과 충돌 하지 않도록 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="fc786-165">다음 Api는의 기본값을로 변경 `SameSiteMode.Lax ` `-1` 하 여 이러한에 대 한 SameSite 특성을 내보내지 않도록 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="fc786-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these :::no-loc(cookie):::s:</span></span>

* <span data-ttu-id="fc786-166"><xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Options>[HttpContext 응답과 함께 사용 됩니다. :::no-loc(Cookie)::: s. Append](xref:Microsoft.AspNetCore.Http.IResponse:::no-loc(Cookie):::s.Append*)</span><span class="sxs-lookup"><span data-stu-id="fc786-166"><xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Options> used with [HttpContext.Response.:::no-loc(Cookie):::s.Append](xref:Microsoft.AspNetCore.Http.IResponse:::no-loc(Cookie):::s.Append*)</span></span>
* <span data-ttu-id="fc786-167"><xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder>  의 팩터리로 사용 됩니다. `:::no-loc(Cookie):::Options`</span><span class="sxs-lookup"><span data-stu-id="fc786-167"><xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder>  used as a factory for `:::no-loc(Cookie):::Options`</span></span>
* <span data-ttu-id="fc786-168">[:::no-loc(Cookie):::PolicyOptions. MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="fc786-168">[:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy)</span></span>

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="fc786-169">기록 및 변경 내용</span><span class="sxs-lookup"><span data-stu-id="fc786-169">History and changes</span></span>

<span data-ttu-id="fc786-170">SameSite 지원은 [2016 초안 표준을](https://tools.ietf.org/html/draft-west-first-party-:::no-loc(cookie):::s-07#section-4.1)사용 하 여 2.0의 ASP.NET Core에서 처음 구현 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-:::no-loc(cookie):::s-07#section-4.1).</span></span> <span data-ttu-id="fc786-171">2016 표준은 옵트인 (opt in) 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="fc786-172">기본적으로 여러을로 설정 하 여 옵트인 (opt in :::no-loc(cookie)::: )을 ASP.NET Core `Lax` 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-172">ASP.NET Core opted-in by setting several :::no-loc(cookie):::s to `Lax` by default.</span></span> <span data-ttu-id="fc786-173">인증에 몇 가지 [문제가 발생](https://github.com/aspnet/Announcements/issues/318) 한 후에는 대부분의 SameSite 사용이 [사용 되지 않도록 설정](https://github.com/aspnet/Announcements/issues/348)되었습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="fc786-174">11 월 2019에 [패치](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) 를 실행 하 여 2016 표준에서 2019 standard로 업데이트 했습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="fc786-175">[SameSite 사양의 2019 초안](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="fc786-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="fc786-176">는 이전 버전과 호환 **되지 않습니다** . 2016 초안.</span><span class="sxs-lookup"><span data-stu-id="fc786-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="fc786-177">자세한 내용은이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc786-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="fc786-178">:::no-loc(cookie):::는 기본적으로로 처리 되는를 지정 합니다 `SameSite=Lax` .</span><span class="sxs-lookup"><span data-stu-id="fc786-178">Specifies :::no-loc(cookie):::s are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="fc786-179">:::no-loc(cookie)::: `SameSite=None` 사이트 간 배달을 가능 하 게 하기 위해 명시적으로 어설션하는를로 표시 합니다 `Secure` .</span><span class="sxs-lookup"><span data-stu-id="fc786-179">Specifies :::no-loc(cookie):::s that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="fc786-180">`None` 옵트아웃 (opt out) 할 새 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="fc786-181">는 ASP.NET Core 2.1, 2.2 및 3.0에 대해 발급 된 패치에 의해 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="fc786-182">ASP.NET Core 3.1에는 추가 SameSite 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="fc786-183">는 기본적으로 [2 월 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)에 [Chrome](https://chromestatus.com/feature/5088147346030592) 에서 사용 하도록 예약 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="fc786-184">브라우저에서 2019의이 표준으로 이동 하기 시작 했습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="fc786-185">2016 SameSite draft standard에서 2019 초안 표준으로 변경 되어 영향을 받는 Api</span><span class="sxs-lookup"><span data-stu-id="fc786-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="fc786-186">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="fc786-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* <span data-ttu-id="fc786-187">[:::no-loc(Cookie):::SameSite](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Options.SameSite)</span><span class="sxs-lookup"><span data-stu-id="fc786-187">[:::no-loc(Cookie):::Options.SameSite](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Options.SameSite)</span></span>
* <span data-ttu-id="fc786-188">[:::no-loc(Cookie):::SameSite](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.SameSite)</span><span class="sxs-lookup"><span data-stu-id="fc786-188">[:::no-loc(Cookie):::Builder.SameSite](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.SameSite)</span></span>
* <span data-ttu-id="fc786-189">[:::no-loc(Cookie):::PolicyOptions. MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="fc786-189">[:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy)</span></span>
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.Set:::no-loc(Cookie):::HeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="fc786-190">이전 브라우저 지원</span><span class="sxs-lookup"><span data-stu-id="fc786-190">Supporting older browsers</span></span>

<span data-ttu-id="fc786-191">2016 SameSite 표준에서는 알 수 없는 값을 값으로 처리 해야 합니다 `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="fc786-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="fc786-192">2016 SameSite 표준을 지 원하는 이전 브라우저에서 액세스 된 앱은 값이 인 SameSite 속성을 가져올 때 손상 될 수 있습니다 `None` .</span><span class="sxs-lookup"><span data-stu-id="fc786-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="fc786-193">웹 앱은 이전 브라우저를 지원 하려는 경우 브라우저 검색을 구현 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="fc786-194">ASP.NET Core은 User-Agents 값이 매우 휘발성 이며 자주 변경 되기 때문에 브라우저 검색을 구현 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="fc786-195">의 확장 지점은 User-Agent 특정 논리를 연결 하는 것을 <xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy> 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-195">An extension point in <xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="fc786-196">에서 `Startup.Configure` 를 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyAppBuilderExtensions.Use:::no-loc(Cookie):::Policy*> 호출 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 하거나을 (를) 쓰는 *any* 메서드를 호출 하는 코드를 추가 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="fc786-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyAppBuilderExtensions.Use:::no-loc(Cookie):::Policy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes :::no-loc(cookie):::s:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="fc786-197">에서 `Startup.ConfigureServices` 다음과 유사한 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="fc786-198">위의 샘플에서는 사용자 `MyUserAgentDetectionLib.DisallowsSameSiteNone` 에이전트가 SameSite을 지원 하지 않는지 검색 하는 사용자 제공 라이브러리입니다 `None` .</span><span class="sxs-lookup"><span data-stu-id="fc786-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="fc786-199">다음 코드는 샘플 메서드를 보여 줍니다 `DisallowsSameSiteNone` .</span><span class="sxs-lookup"><span data-stu-id="fc786-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="fc786-200">다음 코드는 데모용 으로만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="fc786-201">완료 되지 않은 것으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="fc786-202">유지 관리 되거나 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="fc786-203">SameSite 문제에 대 한 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="fc786-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="fc786-204">타사 로그인을 통해와 같은 원격 사이트와 상호 작용 하는 앱은 다음 작업을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="fc786-205">여러 브라우저에서 상호 작용을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="fc786-206">이 문서에서 설명 [ :::no-loc(Cookie)::: 하는 정책 브라우저 검색 및 완화](#sob) 를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-206">Apply the [:::no-loc(Cookie):::Policy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="fc786-207">새 SameSite 동작을 옵트인 (opt in) 할 수 있는 클라이언트 버전을 사용 하 여 웹 앱을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="fc786-208">Chrome, Firefox 및 Chromium Edge 모두에는 테스트에 사용할 수 있는 새로운 옵트인 기능 플래그가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="fc786-209">앱이 SameSite 패치를 적용 한 후 이전 클라이언트 버전, 특히 Safari를 사용 하 여 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="fc786-210">자세한 내용은이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc786-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="fc786-211">Chrome으로 테스트</span><span class="sxs-lookup"><span data-stu-id="fc786-211">Test with Chrome</span></span>

<span data-ttu-id="fc786-212">Chrome 78 +는 일시적인 완화를 제공 하기 때문에 잘못 된 결과를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="fc786-213">Chrome 78 + 임시 완화를 사용 하면 2 분 이내에 s를 사용할 수 있습니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="fc786-213">The Chrome 78+ temporary mitigation allows :::no-loc(cookie):::s less than two minutes old.</span></span> <span data-ttu-id="fc786-214">적절 한 테스트 플래그가 설정 된 Chrome 76 또는 77은 보다 정확한 결과를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="fc786-215">새 SameSite 동작을 테스트 하려면 `chrome://flags/#same-site-by-default-:::no-loc(cookie):::s` **사용** 으로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-:::no-loc(cookie):::s` to **Enabled** .</span></span> <span data-ttu-id="fc786-216">이전 버전의 Chrome (75 및 아래)은 새 설정으로 인해 실패 하는 것으로 보고 됩니다 `None` .</span><span class="sxs-lookup"><span data-stu-id="fc786-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="fc786-217">이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc786-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="fc786-218">Google은 이전 chrome 버전을 사용할 수 없도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="fc786-219">[Chromium 다운로드](https://www.chromium.org/getting-involved/download-chromium) 의 지침에 따라 이전 버전의 Chrome을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="fc786-220">이전 버전의 chrome을 검색 하 여 제공 된 링크에서 Chrome을 다운로드 **하지** 마세요.</span><span class="sxs-lookup"><span data-stu-id="fc786-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="fc786-221">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="fc786-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="fc786-222">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="fc786-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="fc786-223">카나리아 버전부터 `80.0.3975.0` 완화 된 + 사후 임시 완화는 새 플래그를 사용 하 여 테스트 목적으로 사용 하지 않도록 설정 하 여 `--enable-features=SameSiteDefaultChecksMethodRigorously` 완화가 제거 된 기능의 최종 종료 상태에서 사이트 및 서비스를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="fc786-224">자세한 내용은 Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc786-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="fc786-225">Safari를 사용 하 여 테스트</span><span class="sxs-lookup"><span data-stu-id="fc786-225">Test with Safari</span></span>

<span data-ttu-id="fc786-226">Safari 12는 이전 초안을 엄격 하 게 구현 했으며 새 `None` 값이에 있는 경우 실패 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="fc786-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a :::no-loc(cookie):::.</span></span> <span data-ttu-id="fc786-227">`None` 이 문서에서 [이전 브라우저를 지 원하는](#sob) 브라우저 검색 코드를 통해가 방지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="fc786-228">MSAL, ADAL 또는 사용 중인 라이브러리를 사용 하 여 Safari 12, Safari 13 및 WebKit 기반 OS 스타일 로그인을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="fc786-229">문제는 기본 OS 버전에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="fc786-230">OSX Mojave (10.14) 및 iOS 12는 새로운 SameSite 동작의 호환성 문제를 해결 하는 것으로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="fc786-231">OS를 OSX Catalina.properties (10.15) 또는 iOS 13로 업그레이드 하면 문제가 해결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="fc786-232">Safari에는 현재 새 사양 동작 테스트를 위한 옵트인 플래그가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="fc786-233">Firefox로 테스트</span><span class="sxs-lookup"><span data-stu-id="fc786-233">Test with Firefox</span></span>

<span data-ttu-id="fc786-234">새 표준에 대 한 Firefox 지원은 기능 플래그를 사용 하 여 페이지에서 옵트인 하 여 버전 68 이상에서 테스트할 수 있습니다 `about:config` `network.:::no-loc(cookie):::.sameSite.laxByDefault` .</span><span class="sxs-lookup"><span data-stu-id="fc786-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.:::no-loc(cookie):::.sameSite.laxByDefault`.</span></span> <span data-ttu-id="fc786-235">이전 버전의 Firefox와의 호환성 문제에 대 한 보고서가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="fc786-236">Edge 브라우저를 사용 하 여 테스트</span><span class="sxs-lookup"><span data-stu-id="fc786-236">Test with Edge browser</span></span>

<span data-ttu-id="fc786-237">Edge는 이전 SameSite 표준을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="fc786-238">Edge 버전 44에는 새로운 표준과의 알려진 호환성 문제가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="fc786-239">Edge를 사용 하 여 테스트 (Chromium)</span><span class="sxs-lookup"><span data-stu-id="fc786-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="fc786-240">SameSite 플래그는 페이지에 설정 되어 `edge://flags/#same-site-by-default-:::no-loc(cookie):::s` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-240">SameSite flags are set on the `edge://flags/#same-site-by-default-:::no-loc(cookie):::s` page.</span></span> <span data-ttu-id="fc786-241">Edge Chromium에서 호환성 문제가 검색 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-no-locelectron"></a><span data-ttu-id="fc786-242">테스트 :::no-loc(Electron):::</span><span class="sxs-lookup"><span data-stu-id="fc786-242">Test with :::no-loc(Electron):::</span></span>

<span data-ttu-id="fc786-243">버전에는 :::no-loc(Electron)::: 이전 버전의 Chromium가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-243">Versions of :::no-loc(Electron)::: include older versions of Chromium.</span></span> <span data-ttu-id="fc786-244">예를 들어 팀에서 사용 하는의 버전은 :::no-loc(Electron)::: Chromium 66 이며,이는 이전 동작을 보여 주는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="fc786-244">For example, the version of :::no-loc(Electron)::: used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="fc786-245">제품 버전을 사용 하 여 사용자 고유의 호환성 테스트를 수행 해야 합니다 :::no-loc(Electron)::: .</span><span class="sxs-lookup"><span data-stu-id="fc786-245">You must perform your own compatibility testing with the version of :::no-loc(Electron)::: your product uses.</span></span> <span data-ttu-id="fc786-246">다음 섹션에서 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc786-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc786-247">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="fc786-247">Additional resources</span></span>

* [<span data-ttu-id="fc786-248">Chromium 블로그: 개발자: 새 SameSite를 사용할 준비가 되었습니다. 보안 :::no-loc(Cookie)::: 설정</span><span class="sxs-lookup"><span data-stu-id="fc786-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure :::no-loc(Cookie)::: Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* <span data-ttu-id="fc786-249">[SameSite :::no-loc(cookie)::: s 설명](https://web.dev/samesite-:::no-loc(cookie):::s-explained/)</span><span class="sxs-lookup"><span data-stu-id="fc786-249">[SameSite :::no-loc(cookie):::s explained](https://web.dev/samesite-:::no-loc(cookie):::s-explained/)</span></span>
* [<span data-ttu-id="fc786-250">11 월 2019 패치</span><span class="sxs-lookup"><span data-stu-id="fc786-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="fc786-251">예제</span><span class="sxs-lookup"><span data-stu-id="fc786-251">Sample</span></span>               | <span data-ttu-id="fc786-252">문서</span><span class="sxs-lookup"><span data-stu-id="fc786-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="fc786-253">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="fc786-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="fc786-254">[.NET Core :::no-loc(Razor)::: 페이지](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21:::no-loc(Razor):::Pages)</span><span class="sxs-lookup"><span data-stu-id="fc786-254">[.NET Core :::no-loc(Razor)::: Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21:::no-loc(Razor):::Pages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="fc786-255">예제</span><span class="sxs-lookup"><span data-stu-id="fc786-255">Sample</span></span>               | <span data-ttu-id="fc786-256">문서</span><span class="sxs-lookup"><span data-stu-id="fc786-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="fc786-257">[.NET Core :::no-loc(Razor)::: 페이지](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31:::no-loc(Razor):::Pages)</span><span class="sxs-lookup"><span data-stu-id="fc786-257">[.NET Core :::no-loc(Razor)::: Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31:::no-loc(Razor):::Pages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
