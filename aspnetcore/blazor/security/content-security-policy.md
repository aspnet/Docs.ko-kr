---
title: ASP.NET Core Blazor에 콘텐츠 보안 정책 적용
author: guardrex
description: ASP.NET Core Blazor 앱에 CSP(콘텐츠 보안 정책)를 사용하여 XSS(교차 사이트 스크립팅) 공격으로부터 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: f44f348947e31864f5d0d44c9caf1a3aa9c3b1d4
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280343"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="2b00e-103">ASP.NET Core Blazor에 콘텐츠 보안 정책 적용</span><span class="sxs-lookup"><span data-stu-id="2b00e-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="2b00e-104">[XSS(교차 사이트 스크립팅)](xref:security/cross-site-scripting)는 공격자가 앱의 렌더링된 콘텐츠에 하나 이상의 악성 클라이언트 쪽 스크립트를 배치하는 보안 취약성입니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-104">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="2b00e-105">CSP(콘텐츠 보안 정책)는 브라우저에 다음을 알림으로써 XSS 공격을 막아내는 데 도움을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-105">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="2b00e-106">로드된 콘텐츠의 유효한 원본(스크립트, 스타일시트, 이미지 포함)</span><span class="sxs-lookup"><span data-stu-id="2b00e-106">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="2b00e-107">페이지에 의해 취해진 유효한 동작(양식의 허용되는 URL 대상 지정)</span><span class="sxs-lookup"><span data-stu-id="2b00e-107">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="2b00e-108">로드할 수 있는 유효한 플러그인</span><span class="sxs-lookup"><span data-stu-id="2b00e-108">Plugins that can be loaded.</span></span>

<span data-ttu-id="2b00e-109">앱에 CSP를 적용하려면 개발자가 하나 이상의 `Content-Security-Policy` 헤더 또는 `<meta>` 태그에 여러 CSP 콘텐츠 보안 ‘지시문’을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-109">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="2b00e-110">정책은 페이지가 로드되는 동안 브라우저에 의해 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-110">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="2b00e-111">브라우저가 페이지의 원본을 검사하여 콘텐츠 보안 지시문의 요구 사항을 충족하는지 여부를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-111">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="2b00e-112">특정 리소스에 대해 정책 지시문이 충족되지 않을 경우 브라우저는 해당 리소스를 로드하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-112">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="2b00e-113">타사 스크립트를 허용하지 않는 정책을 예로 들면,</span><span class="sxs-lookup"><span data-stu-id="2b00e-113">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="2b00e-114">`src` 특성에 타사 원본이 있는 `<script>` 태그가 페이지에 포함되어 있는 경우 브라우저는 스크립트가 로드되는 것을 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-114">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="2b00e-115">CSP는 Chrome, Edge, Firefox, Opera, Safari를 비롯한 대부분의 최신 데스크톱 및 모바일 브라우저에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-115">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="2b00e-116">CSP는 Blazor 앱에서 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-116">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="2b00e-117">정책 지시문</span><span class="sxs-lookup"><span data-stu-id="2b00e-117">Policy directives</span></span>

<span data-ttu-id="2b00e-118">최소한 Blazor 앱에 대해 다음 지시문 및 원본을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-118">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="2b00e-119">필요에 따라 추가 지시문 및 원본을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-119">Add additional directives and sources as needed.</span></span> <span data-ttu-id="2b00e-120">다음 지시문은 이 문서의 [정책 적용](#apply-the-policy) 섹션에서 사용됩니다. 해당 섹션에서 Blazor WebAssembly 및 Blazor Server의 보안 정책 예제도 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-120">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="2b00e-121">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): 페이지의 `<base>` 태그에 대한 URL을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-121">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="2b00e-122">체계 및 포트 번호를 포함하여 앱의 원본이 유효한 원본임을 나타내려면 `self`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-122">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="2b00e-123">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): 혼합된 HTTP 및 HTTPS 콘텐츠가 로드되지 않도록 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-123">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="2b00e-124">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): 정책에서 명시적으로 지정하지 않은 원본 지시문에 대한 대체를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-124">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="2b00e-125">체계 및 포트 번호를 포함하여 앱의 원본이 유효한 원본임을 나타내려면 `self`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-125">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="2b00e-126">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): 이미지의 유효한 원본을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-126">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="2b00e-127">`data:` URL에서 이미지가 로드되는 것을 허용하려면 `data:`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-127">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="2b00e-128">HTTPS 엔드포인트에서 이미지가 로드되는 것을 허용하려면 `https:`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-128">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="2b00e-129">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): `<object>`, `<embed>`, `<applet>` 태그의 유효한 원본을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-129">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="2b00e-130">모든 URL 원본을 차단하려면 `none`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-130">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="2b00e-131">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): 스크립트의 유효한 원본을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-131">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="2b00e-132">부트스트랩 스크립트의 경우 `https://stackpath.bootstrapcdn.com/` 호스트 원본을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-132">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="2b00e-133">체계 및 포트 번호를 포함하여 앱의 원본이 유효한 원본임을 나타내려면 `self`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-133">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="2b00e-134">Blazor WebAssembly 앱에서:</span><span class="sxs-lookup"><span data-stu-id="2b00e-134">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="2b00e-135">필요한 스크립트의 로드를 허용하는 해시를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-135">Specify hashes to permit required scripts to load.</span></span>
    * <span data-ttu-id="2b00e-136">문자열로부터 코드를 만들기 위해 `eval()` 및 메서드를 사용하려면 `unsafe-eval`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-136">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="2b00e-137">Blazor Server 앱에서 필요한 스크립트의 로드를 허용하는 해시를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-137">In a Blazor Server app, specify hashes to permit required scripts to load.</span></span>
* <span data-ttu-id="2b00e-138">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): 스타일시트의 유효한 원본을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-138">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="2b00e-139">부트스트랩 스타일시트의 경우 `https://stackpath.bootstrapcdn.com/` 호스트 원본을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-139">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="2b00e-140">체계 및 포트 번호를 포함하여 앱의 원본이 유효한 원본임을 나타내려면 `self`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-140">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="2b00e-141">인라인 스타일의 사용을 허용하려면 `unsafe-inline`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-141">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="2b00e-142">초기 요청 후에 클라이언트와 서버를 다시 연결하려면 Blazor Server 앱에서 UI에 대한 인라인 선언이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-142">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="2b00e-143">이후 릴리스에서는 `unsafe-inline`이 더 이상 필요하지 않도록 인라인 스타일이 제거될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-143">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="2b00e-144">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): 비보안(HTTP) 소스의 콘텐츠 URL을 HTTPS를 통해 안전하게 획득해야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-144">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="2b00e-145">위에 나온 지시문은 Microsoft Internet Explorer를 제외한 모든 브라우저에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-145">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="2b00e-146">추가 인라인 스크립트를 위한 SHA 해시를 획득하려면:</span><span class="sxs-lookup"><span data-stu-id="2b00e-146">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="2b00e-147">[정책 적용](#apply-the-policy) 섹션에 나와 있는 CSP를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-147">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="2b00e-148">앱을 로컬로 실행하는 상태에서 브라우저의 개발자 도구 콘솔에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-148">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="2b00e-149">CSP 헤더 또는 `meta` 태그가 있는 경우 브라우저가 차단된 스크립트에 대해 해시를 계산하고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-149">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="2b00e-150">브라우저에 표시된 해시를 `script-src` 소스로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-150">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="2b00e-151">해시를 작은따옴표로 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-151">Use single quotes around each hash.</span></span>

<span data-ttu-id="2b00e-152">콘텐츠 보안 정책 수준 2 브라우저 지원 매트릭스는 [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)(콘텐츠 보안 정책 수준 2를 사용해도 될까요?)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b00e-152">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="2b00e-153">정책 적용</span><span class="sxs-lookup"><span data-stu-id="2b00e-153">Apply the policy</span></span>

<span data-ttu-id="2b00e-154">`<meta>` 태그를 사용하여 정책을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-154">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="2b00e-155">`http-equiv` 특성의 값을 `Content-Security-Policy`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-155">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="2b00e-156">`content` 특성 값에 지시문을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-156">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="2b00e-157">지시문을 세미콜론(`;`)으로 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-157">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="2b00e-158">`meta` 태그는 항상 `<head>` 콘텐츠 안에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-158">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="2b00e-159">다음 섹션에서는 Blazor WebAssembly 및 Blazor Server의 예제 정책을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-159">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="2b00e-160">이 문서에서 예제는 Blazor의 각 릴리스에 대해 버전으로 관리됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-160">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="2b00e-161">사용 중인 릴리스에 적합한 버전을 사용하려면 이 웹 페이지의 **버전** 드롭다운 선택기를 사용하여 문서 버전을 선택하세요.</span><span class="sxs-lookup"><span data-stu-id="2b00e-161">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### Blazor WebAssembly

<span data-ttu-id="2b00e-162">`wwwroot/index.html` 호스트 페이지의 `<head>` 콘텐츠에서 [정책 지시문](#policy-directives) 섹션에 설명된 지시문을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-162">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

<span data-ttu-id="2b00e-163">앱에 필요한 더 많은 `script-src` 및 `style-src` 해시를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-163">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="2b00e-164">개발하는 동안 온라인 도구나 브라우저 개발자 도구를 사용하여 해시를 계산합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-164">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="2b00e-165">예를 들어 다음 브라우저 도구 콘솔 오류는 정책에서 다루지 않는 필수 스크립트의 해시를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-165">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="2b00e-166">다음 콘텐츠 보안 정책 지시문을 위반하기 때문에 인라인 스크립트 실행이 거부되었습니다. “ ... ”.</span><span class="sxs-lookup"><span data-stu-id="2b00e-166">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="2b00e-167">인라인 실행을 사용하려면 ‘unsafe-inline’ 키워드, 해시(‘sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=’) 또는 nonce(‘nonce-...’) 중 하나가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-167">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="2b00e-168">오류와 관련된 특정 스크립트가 콘솔에서 오류 옆에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-168">The particular script associated with the error is displayed in the console next to the error.</span></span>

### Blazor Server

<span data-ttu-id="2b00e-169">`Pages/_Host.cshtml` 호스트 페이지의 `<head>` 콘텐츠에서 [정책 지시문](#policy-directives) 섹션에 설명된 지시문을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-169">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

<span data-ttu-id="2b00e-170">앱에 필요한 더 많은 `script-src` 및 `style-src` 해시를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-170">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="2b00e-171">개발하는 동안 온라인 도구나 브라우저 개발자 도구를 사용하여 해시를 계산합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-171">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="2b00e-172">예를 들어 다음 브라우저 도구 콘솔 오류는 정책에서 다루지 않는 필수 스크립트의 해시를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-172">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="2b00e-173">다음 콘텐츠 보안 정책 지시문을 위반하기 때문에 인라인 스크립트 실행이 거부되었습니다. “ ... ”.</span><span class="sxs-lookup"><span data-stu-id="2b00e-173">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="2b00e-174">인라인 실행을 사용하려면 ‘unsafe-inline’ 키워드, 해시(‘sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=’) 또는 nonce(‘nonce-...’) 중 하나가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-174">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="2b00e-175">오류와 관련된 특정 스크립트가 콘솔에서 오류 옆에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-175">The particular script associated with the error is displayed in the console next to the error.</span></span>

## <a name="meta-tag-limitations"></a><span data-ttu-id="2b00e-176">Meta 태그 제한 사항</span><span class="sxs-lookup"><span data-stu-id="2b00e-176">Meta tag limitations</span></span>

<span data-ttu-id="2b00e-177">`<meta>` 태그 정책은 다음 지시문을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-177">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="2b00e-178">frame-ancestors</span><span class="sxs-lookup"><span data-stu-id="2b00e-178">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="2b00e-179">report-to</span><span class="sxs-lookup"><span data-stu-id="2b00e-179">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="2b00e-180">report-uri</span><span class="sxs-lookup"><span data-stu-id="2b00e-180">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="2b00e-181">sandbox</span><span class="sxs-lookup"><span data-stu-id="2b00e-181">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="2b00e-182">위의 지시문을 지원하려면 이름이 `Content-Security-Policy`인 헤더를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="2b00e-182">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="2b00e-183">지시문 문자열은 헤더의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-183">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="2b00e-184">정책 테스트 및 위반 보고서 받기</span><span class="sxs-lookup"><span data-stu-id="2b00e-184">Test a policy and receive violation reports</span></span>

<span data-ttu-id="2b00e-185">테스트를 사용하면 초기 정책을 빌드할 때 타사 스크립트가 실수로 차단되지 않는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-185">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="2b00e-186">정책 지시문을 적용하지 않고 일정 시간 동안 정책을 테스트하려면 `<meta>` 태그의 `http-equiv` 특성이나 헤더 기반 정책의 헤더 이름을 `Content-Security-Policy-Report-Only`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-186">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="2b00e-187">오류 보고서는 지정된 URL로 JSON 문서로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-187">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="2b00e-188">자세한 내용은 [MDN 웹 문서: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b00e-188">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="2b00e-189">정책이 활성 상태일 때의 위반 보고에 대해 알아보려면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b00e-189">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="2b00e-190">report-to</span><span class="sxs-lookup"><span data-stu-id="2b00e-190">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="2b00e-191">report-uri</span><span class="sxs-lookup"><span data-stu-id="2b00e-191">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="2b00e-192">`report-uri`는 더 이상 사용하지 않는 것이 좋지만 모든 주요 브라우저에서 `report-to`가 지원될 때까지 두 지시문을 모두 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-192">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="2b00e-193">`report-uri`에 대한 지원은 브라우저에서 ‘언제든지’ 제거될 수 있으므로 `report-uri`만 단독으로 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2b00e-193">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="2b00e-194">`report-to`가 완전히 지원되는 시점에 정책에서 `report-uri`에 대한 지원을 제거하세요.</span><span class="sxs-lookup"><span data-stu-id="2b00e-194">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="2b00e-195">`report-to`의 채택 여부를 추적하려면 [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)(report-to를 사용해도 될까요?)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b00e-195">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="2b00e-196">릴리스마다 앱의 정책을 테스트하고 업데이트하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-196">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="2b00e-197">문제 해결</span><span class="sxs-lookup"><span data-stu-id="2b00e-197">Troubleshoot</span></span>

* <span data-ttu-id="2b00e-198">오류는 브라우저의 개발자 도구 콘솔에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-198">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="2b00e-199">브라우저에서는 다음과 같은 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-199">Browsers provide information about:</span></span>
  * <span data-ttu-id="2b00e-200">정책을 준수하지 않는 요소.</span><span class="sxs-lookup"><span data-stu-id="2b00e-200">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="2b00e-201">차단된 항목을 허용하도록 정책을 수정하는 방법.</span><span class="sxs-lookup"><span data-stu-id="2b00e-201">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="2b00e-202">정책은 클라이언트의 브라우저에서 포함된 모든 지시문을 지원하는 경우에만 완전히 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="2b00e-202">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="2b00e-203">최신 브라우저 지원 매트릭스는 [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy)(Content-Security-Policy를 사용해도 될까요?)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b00e-203">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2b00e-204">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2b00e-204">Additional resources</span></span>

* [<span data-ttu-id="2b00e-205">MDN 웹 문서: 콘텐츠 보안 정책</span><span class="sxs-lookup"><span data-stu-id="2b00e-205">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="2b00e-206">콘텐츠 보안 정책 수준 2</span><span class="sxs-lookup"><span data-stu-id="2b00e-206">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="2b00e-207">Google CSP 계산기</span><span class="sxs-lookup"><span data-stu-id="2b00e-207">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
