---
title: ASP.NET Core :::no-loc(Blazor):::에 콘텐츠 보안 정책 적용
author: guardrex
description: 'ASP.NET Core :::no-loc(Blazor)::: 앱에 CSP(콘텐츠 보안 정책)를 사용하여 XSS(교차 사이트 스크립팅) 공격으로부터 보호하는 방법을 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: 66fd41abe4f85071797bacc0a5531bbab35bd227
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055596"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-no-locblazor"></a><span data-ttu-id="a8720-103">ASP.NET Core :::no-loc(Blazor):::에 콘텐츠 보안 정책 적용</span><span class="sxs-lookup"><span data-stu-id="a8720-103">Enforce a Content Security Policy for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="a8720-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a8720-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a8720-105">[XSS(교차 사이트 스크립팅)](xref:security/cross-site-scripting)는 공격자가 앱의 렌더링된 콘텐츠에 하나 이상의 악성 클라이언트 쪽 스크립트를 배치하는 보안 취약성입니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="a8720-106">CSP(콘텐츠 보안 정책)는 브라우저에 다음을 알림으로써 XSS 공격을 막아내는 데 도움을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="a8720-107">로드된 콘텐츠의 유효한 원본(스크립트, 스타일시트, 이미지 포함)</span><span class="sxs-lookup"><span data-stu-id="a8720-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="a8720-108">페이지에 의해 취해진 유효한 동작(양식의 허용되는 URL 대상 지정)</span><span class="sxs-lookup"><span data-stu-id="a8720-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="a8720-109">로드할 수 있는 유효한 플러그인</span><span class="sxs-lookup"><span data-stu-id="a8720-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="a8720-110">앱에 CSP를 적용하려면 개발자가 하나 이상의 `Content-Security-Policy` 헤더 또는 `<meta>` 태그에 여러 CSP 콘텐츠 보안 ‘지시문’을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="a8720-111">정책은 페이지가 로드되는 동안 브라우저에 의해 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="a8720-112">브라우저가 페이지의 원본을 검사하여 콘텐츠 보안 지시문의 요구 사항을 충족하는지 여부를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="a8720-113">특정 리소스에 대해 정책 지시문이 충족되지 않을 경우 브라우저는 해당 리소스를 로드하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="a8720-114">타사 스크립트를 허용하지 않는 정책을 예로 들면,</span><span class="sxs-lookup"><span data-stu-id="a8720-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="a8720-115">`src` 특성에 타사 원본이 있는 `<script>` 태그가 페이지에 포함되어 있는 경우 브라우저는 스크립트가 로드되는 것을 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="a8720-116">CSP는 Chrome, Edge, Firefox, Opera, Safari를 비롯한 대부분의 최신 데스크톱 및 모바일 브라우저에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="a8720-117">CSP는 :::no-loc(Blazor)::: 앱에서 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-117">CSP is recommended for :::no-loc(Blazor)::: apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="a8720-118">정책 지시문</span><span class="sxs-lookup"><span data-stu-id="a8720-118">Policy directives</span></span>

<span data-ttu-id="a8720-119">최소한 :::no-loc(Blazor)::: 앱에 대해 다음 지시문 및 원본을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-119">Minimally, specify the following directives and sources for :::no-loc(Blazor)::: apps.</span></span> <span data-ttu-id="a8720-120">필요에 따라 추가 지시문 및 원본을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="a8720-121">다음 지시문은 이 문서의 [정책 적용](#apply-the-policy) 섹션에서 사용됩니다. 해당 섹션에서 :::no-loc(Blazor WebAssembly)::: 및 :::no-loc(Blazor Server):::의 보안 정책 예제도 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server)::: are provided:</span></span>

* <span data-ttu-id="a8720-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): 페이지의 `<base>` 태그에 대한 URL을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="a8720-123">체계 및 포트 번호를 포함하여 앱의 원본이 유효한 원본임을 나타내려면 `self`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="a8720-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): 혼합된 HTTP 및 HTTPS 콘텐츠가 로드되지 않도록 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="a8720-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): 정책에서 명시적으로 지정하지 않은 원본 지시문에 대한 대체를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="a8720-126">체계 및 포트 번호를 포함하여 앱의 원본이 유효한 원본임을 나타내려면 `self`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="a8720-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): 이미지의 유효한 원본을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="a8720-128">`data:` URL에서 이미지가 로드되는 것을 허용하려면 `data:`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="a8720-129">HTTPS 엔드포인트에서 이미지가 로드되는 것을 허용하려면 `https:`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="a8720-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): `<object>`, `<embed>`, `<applet>` 태그의 유효한 원본을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="a8720-131">모든 URL 원본을 차단하려면 `none`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="a8720-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): 스크립트의 유효한 원본을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="a8720-133">부트스트랩 스크립트의 경우 `https://stackpath.bootstrapcdn.com/` 호스트 원본을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="a8720-134">체계 및 포트 번호를 포함하여 앱의 원본이 유효한 원본임을 나타내려면 `self`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="a8720-135">:::no-loc(Blazor WebAssembly)::: 앱에서:</span><span class="sxs-lookup"><span data-stu-id="a8720-135">In a :::no-loc(Blazor WebAssembly)::: app:</span></span>
    * <span data-ttu-id="a8720-136">필요한 :::no-loc(Blazor WebAssembly)::: 인라인 스크립트가 로드되는 것을 허용하려면 다음 해시를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-136">Specify the following hashes to permit the required :::no-loc(Blazor WebAssembly)::: inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="a8720-137">문자열로부터 코드를 만들기 위해 `eval()` 및 메서드를 사용하려면 `unsafe-eval`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="a8720-138">:::no-loc(Blazor Server)::: 앱에서 스타일시트의 대체 검색을 수행하는 인라인 스크립트에 대해 `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` 해시를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-138">In a :::no-loc(Blazor Server)::: app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="a8720-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): 스타일시트의 유효한 원본을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="a8720-140">부트스트랩 스타일시트의 경우 `https://stackpath.bootstrapcdn.com/` 호스트 원본을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="a8720-141">체계 및 포트 번호를 포함하여 앱의 원본이 유효한 원본임을 나타내려면 `self`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="a8720-142">인라인 스타일의 사용을 허용하려면 `unsafe-inline`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="a8720-143">초기 요청 후에 클라이언트와 서버를 다시 연결하려면 :::no-loc(Blazor Server)::: 앱에서 UI에 대한 인라인 선언이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-143">The inline declaration is required for the UI in :::no-loc(Blazor Server)::: apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="a8720-144">이후 릴리스에서는 `unsafe-inline`이 더 이상 필요하지 않도록 인라인 스타일이 제거될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="a8720-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): 비보안(HTTP) 소스의 콘텐츠 URL을 HTTPS를 통해 안전하게 획득해야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="a8720-146">위에 나온 지시문은 Microsoft Internet Explorer를 제외한 모든 브라우저에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="a8720-147">추가 인라인 스크립트를 위한 SHA 해시를 획득하려면:</span><span class="sxs-lookup"><span data-stu-id="a8720-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="a8720-148">[정책 적용](#apply-the-policy) 섹션에 나와 있는 CSP를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="a8720-149">앱을 로컬로 실행하는 상태에서 브라우저의 개발자 도구 콘솔에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="a8720-150">CSP 헤더 또는 `meta` 태그가 있는 경우 브라우저가 차단된 스크립트에 대해 해시를 계산하고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="a8720-151">브라우저에 표시된 해시를 `script-src` 소스로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="a8720-152">해시를 작은따옴표로 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="a8720-153">콘텐츠 보안 정책 수준 2 브라우저 지원 매트릭스는 [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)(콘텐츠 보안 정책 수준 2를 사용해도 될까요?)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a8720-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="a8720-154">정책 적용</span><span class="sxs-lookup"><span data-stu-id="a8720-154">Apply the policy</span></span>

<span data-ttu-id="a8720-155">`<meta>` 태그를 사용하여 정책을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="a8720-156">`http-equiv` 특성의 값을 `Content-Security-Policy`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="a8720-157">`content` 특성 값에 지시문을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="a8720-158">지시문을 세미콜론(`;`)으로 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="a8720-159">`meta` 태그는 항상 `<head>` 콘텐츠 안에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="a8720-160">다음 섹션에서는 :::no-loc(Blazor WebAssembly)::: 및 :::no-loc(Blazor Server):::의 예제 정책을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-160">The following sections show example policies for :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server):::.</span></span> <span data-ttu-id="a8720-161">이 문서에서 예제는 :::no-loc(Blazor):::의 각 릴리스에 대해 버전으로 관리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-161">These examples are versioned with this article for each release of :::no-loc(Blazor):::.</span></span> <span data-ttu-id="a8720-162">사용 중인 릴리스에 적합한 버전을 사용하려면 이 웹 페이지의 **버전** 드롭다운 선택기를 사용하여 문서 버전을 선택하세요.</span><span class="sxs-lookup"><span data-stu-id="a8720-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="a8720-163">`wwwroot/index.html` 호스트 페이지의 `<head>` 콘텐츠에서 [정책 지시문](#policy-directives) 섹션에 설명된 지시문을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-163">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

### :::no-loc(Blazor Server):::

<span data-ttu-id="a8720-164">`Pages/_Host.cshtml` 호스트 페이지의 `<head>` 콘텐츠에서 [정책 지시문](#policy-directives) 섹션에 설명된 지시문을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-164">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

## <a name="meta-tag-limitations"></a><span data-ttu-id="a8720-165">Meta 태그 제한 사항</span><span class="sxs-lookup"><span data-stu-id="a8720-165">Meta tag limitations</span></span>

<span data-ttu-id="a8720-166">`<meta>` 태그 정책은 다음 지시문을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-166">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="a8720-167">frame-ancestors</span><span class="sxs-lookup"><span data-stu-id="a8720-167">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="a8720-168">report-to</span><span class="sxs-lookup"><span data-stu-id="a8720-168">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="a8720-169">report-uri</span><span class="sxs-lookup"><span data-stu-id="a8720-169">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="a8720-170">sandbox</span><span class="sxs-lookup"><span data-stu-id="a8720-170">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="a8720-171">위의 지시문을 지원하려면 이름이 `Content-Security-Policy`인 헤더를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="a8720-171">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="a8720-172">지시문 문자열은 헤더의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-172">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="a8720-173">정책 테스트 및 위반 보고서 받기</span><span class="sxs-lookup"><span data-stu-id="a8720-173">Test a policy and receive violation reports</span></span>

<span data-ttu-id="a8720-174">테스트를 사용하면 초기 정책을 빌드할 때 타사 스크립트가 실수로 차단되지 않는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-174">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="a8720-175">정책 지시문을 적용하지 않고 일정 시간 동안 정책을 테스트하려면 `<meta>` 태그의 `http-equiv` 특성이나 헤더 기반 정책의 헤더 이름을 `Content-Security-Policy-Report-Only`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-175">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="a8720-176">오류 보고서는 지정된 URL로 JSON 문서로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-176">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="a8720-177">자세한 내용은 [MDN 웹 문서: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a8720-177">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="a8720-178">정책이 활성 상태일 때의 위반 보고에 대해 알아보려면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a8720-178">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="a8720-179">report-to</span><span class="sxs-lookup"><span data-stu-id="a8720-179">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="a8720-180">report-uri</span><span class="sxs-lookup"><span data-stu-id="a8720-180">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="a8720-181">`report-uri`는 더 이상 사용하지 않는 것이 좋지만 모든 주요 브라우저에서 `report-to`가 지원될 때까지 두 지시문을 모두 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-181">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="a8720-182">`report-uri`에 대한 지원은 브라우저에서 ‘언제든지’ 제거될 수 있으므로 `report-uri`만 단독으로 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="a8720-182">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="a8720-183">`report-to`가 완전히 지원되는 시점에 정책에서 `report-uri`에 대한 지원을 제거하세요.</span><span class="sxs-lookup"><span data-stu-id="a8720-183">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="a8720-184">`report-to`의 채택 여부를 추적하려면 [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)(report-to를 사용해도 될까요?)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a8720-184">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="a8720-185">릴리스마다 앱의 정책을 테스트하고 업데이트하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-185">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a8720-186">문제 해결</span><span class="sxs-lookup"><span data-stu-id="a8720-186">Troubleshoot</span></span>

* <span data-ttu-id="a8720-187">오류는 브라우저의 개발자 도구 콘솔에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-187">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="a8720-188">브라우저에서는 다음과 같은 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-188">Browsers provide information about:</span></span>
  * <span data-ttu-id="a8720-189">정책을 준수하지 않는 요소.</span><span class="sxs-lookup"><span data-stu-id="a8720-189">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="a8720-190">차단된 항목을 허용하도록 정책을 수정하는 방법.</span><span class="sxs-lookup"><span data-stu-id="a8720-190">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="a8720-191">정책은 클라이언트의 브라우저에서 포함된 모든 지시문을 지원하는 경우에만 완전히 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="a8720-191">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="a8720-192">최신 브라우저 지원 매트릭스는 [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy)(Content-Security-Policy를 사용해도 될까요?)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a8720-192">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a8720-193">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a8720-193">Additional resources</span></span>

* [<span data-ttu-id="a8720-194">MDN 웹 문서: 콘텐츠 보안 정책</span><span class="sxs-lookup"><span data-stu-id="a8720-194">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="a8720-195">콘텐츠 보안 정책 수준 2</span><span class="sxs-lookup"><span data-stu-id="a8720-195">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="a8720-196">Google CSP 계산기</span><span class="sxs-lookup"><span data-stu-id="a8720-196">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
