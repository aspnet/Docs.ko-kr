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
ms.openlocfilehash: 744449240fabc3dae317d0d7bc9090311521c224
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94570122"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-no-locblazor"></a>ASP.NET Core Blazor에 콘텐츠 보안 정책 적용

작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)

[XSS(교차 사이트 스크립팅)](xref:security/cross-site-scripting)는 공격자가 앱의 렌더링된 콘텐츠에 하나 이상의 악성 클라이언트 쪽 스크립트를 배치하는 보안 취약성입니다. CSP(콘텐츠 보안 정책)는 브라우저에 다음을 알림으로써 XSS 공격을 막아내는 데 도움을 줍니다.

* 로드된 콘텐츠의 유효한 원본(스크립트, 스타일시트, 이미지 포함)
* 페이지에 의해 취해진 유효한 동작(양식의 허용되는 URL 대상 지정)
* 로드할 수 있는 유효한 플러그인

앱에 CSP를 적용하려면 개발자가 하나 이상의 `Content-Security-Policy` 헤더 또는 `<meta>` 태그에 여러 CSP 콘텐츠 보안 ‘지시문’을 지정합니다.

정책은 페이지가 로드되는 동안 브라우저에 의해 평가됩니다. 브라우저가 페이지의 원본을 검사하여 콘텐츠 보안 지시문의 요구 사항을 충족하는지 여부를 확인합니다. 특정 리소스에 대해 정책 지시문이 충족되지 않을 경우 브라우저는 해당 리소스를 로드하지 않습니다. 타사 스크립트를 허용하지 않는 정책을 예로 들면, `src` 특성에 타사 원본이 있는 `<script>` 태그가 페이지에 포함되어 있는 경우 브라우저는 스크립트가 로드되는 것을 차단합니다.

CSP는 Chrome, Edge, Firefox, Opera, Safari를 비롯한 대부분의 최신 데스크톱 및 모바일 브라우저에서 지원됩니다. CSP는 Blazor 앱에서 권장됩니다.

## <a name="policy-directives"></a>정책 지시문

최소한 Blazor 앱에 대해 다음 지시문 및 원본을 지정합니다. 필요에 따라 추가 지시문 및 원본을 추가합니다. 다음 지시문은 이 문서의 [정책 적용](#apply-the-policy) 섹션에서 사용됩니다. 해당 섹션에서 Blazor WebAssembly 및 Blazor Server의 보안 정책 예제도 제공됩니다.

* [base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): 페이지의 `<base>` 태그에 대한 URL을 제한합니다. 체계 및 포트 번호를 포함하여 앱의 원본이 유효한 원본임을 나타내려면 `self`를 지정합니다.
* [block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): 혼합된 HTTP 및 HTTPS 콘텐츠가 로드되지 않도록 차단합니다.
* [default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): 정책에서 명시적으로 지정하지 않은 원본 지시문에 대한 대체를 나타냅니다. 체계 및 포트 번호를 포함하여 앱의 원본이 유효한 원본임을 나타내려면 `self`를 지정합니다.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): 이미지의 유효한 원본을 나타냅니다.
  * `data:` URL에서 이미지가 로드되는 것을 허용하려면 `data:`를 지정합니다.
  * HTTPS 엔드포인트에서 이미지가 로드되는 것을 허용하려면 `https:`를 지정합니다.
* [object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): `<object>`, `<embed>`, `<applet>` 태그의 유효한 원본을 나타냅니다. 모든 URL 원본을 차단하려면 `none`을 지정합니다.
* [script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): 스크립트의 유효한 원본을 나타냅니다.
  * 부트스트랩 스크립트의 경우 `https://stackpath.bootstrapcdn.com/` 호스트 원본을 지정합니다.
  * 체계 및 포트 번호를 포함하여 앱의 원본이 유효한 원본임을 나타내려면 `self`를 지정합니다.
  * Blazor WebAssembly 앱에서:
    * 필요한 스크립트의 로드를 허용하는 해시를 지정합니다.
    * 문자열로부터 코드를 만들기 위해 `eval()` 및 메서드를 사용하려면 `unsafe-eval`을 지정합니다.
  * Blazor Server 앱에서 필요한 스크립트의 로드를 허용하는 해시를 지정합니다.
* [style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): 스타일시트의 유효한 원본을 나타냅니다.
  * 부트스트랩 스타일시트의 경우 `https://stackpath.bootstrapcdn.com/` 호스트 원본을 지정합니다.
  * 체계 및 포트 번호를 포함하여 앱의 원본이 유효한 원본임을 나타내려면 `self`를 지정합니다.
  * 인라인 스타일의 사용을 허용하려면 `unsafe-inline`을 지정합니다. 초기 요청 후에 클라이언트와 서버를 다시 연결하려면 Blazor Server 앱에서 UI에 대한 인라인 선언이 필요합니다. 이후 릴리스에서는 `unsafe-inline`이 더 이상 필요하지 않도록 인라인 스타일이 제거될 수 있습니다.
* [upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): 비보안(HTTP) 소스의 콘텐츠 URL을 HTTPS를 통해 안전하게 획득해야 함을 나타냅니다.

위에 나온 지시문은 Microsoft Internet Explorer를 제외한 모든 브라우저에서 지원됩니다.

추가 인라인 스크립트를 위한 SHA 해시를 획득하려면:

* [정책 적용](#apply-the-policy) 섹션에 나와 있는 CSP를 적용합니다.
* 앱을 로컬로 실행하는 상태에서 브라우저의 개발자 도구 콘솔에 액세스합니다. CSP 헤더 또는 `meta` 태그가 있는 경우 브라우저가 차단된 스크립트에 대해 해시를 계산하고 표시합니다.
* 브라우저에 표시된 해시를 `script-src` 소스로 복사합니다. 해시를 작은따옴표로 묶습니다.

콘텐츠 보안 정책 수준 2 브라우저 지원 매트릭스는 [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)(콘텐츠 보안 정책 수준 2를 사용해도 될까요?)를 참조하세요.

## <a name="apply-the-policy"></a>정책 적용

`<meta>` 태그를 사용하여 정책을 적용합니다.

* `http-equiv` 특성의 값을 `Content-Security-Policy`로 설정합니다.
* `content` 특성 값에 지시문을 배치합니다. 지시문을 세미콜론(`;`)으로 구분합니다.
* `meta` 태그는 항상 `<head>` 콘텐츠 안에 배치합니다.

다음 섹션에서는 Blazor WebAssembly 및 Blazor Server의 예제 정책을 보여 줍니다. 이 문서에서 예제는 Blazor의 각 릴리스에 대해 버전으로 관리됩니다. 사용 중인 릴리스에 적합한 버전을 사용하려면 이 웹 페이지의 **버전** 드롭다운 선택기를 사용하여 문서 버전을 선택하세요.

### Blazor WebAssembly

`wwwroot/index.html` 호스트 페이지의 `<head>` 콘텐츠에서 [정책 지시문](#policy-directives) 섹션에 설명된 지시문을 적용합니다.

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

앱에 필요한 더 많은 `script-src` 및 `style-src` 해시를 추가합니다. 개발하는 동안 온라인 도구나 브라우저 개발자 도구를 사용하여 해시를 계산합니다. 예를 들어 다음 브라우저 도구 콘솔 오류는 정책에서 다루지 않는 필수 스크립트의 해시를 보고합니다.

> 다음 콘텐츠 보안 정책 지시문을 위반하기 때문에 인라인 스크립트 실행이 거부되었습니다. “ ... ”. 인라인 실행을 사용하려면 ‘unsafe-inline’ 키워드, 해시(‘sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=’) 또는 nonce(‘nonce-...’) 중 하나가 필요합니다.

오류와 관련된 특정 스크립트가 콘솔에서 오류 옆에 표시됩니다.

### Blazor Server

`Pages/_Host.cshtml` 호스트 페이지의 `<head>` 콘텐츠에서 [정책 지시문](#policy-directives) 섹션에 설명된 지시문을 적용합니다.

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

앱에 필요한 더 많은 `script-src` 및 `style-src` 해시를 추가합니다. 개발하는 동안 온라인 도구나 브라우저 개발자 도구를 사용하여 해시를 계산합니다. 예를 들어 다음 브라우저 도구 콘솔 오류는 정책에서 다루지 않는 필수 스크립트의 해시를 보고합니다.

> 다음 콘텐츠 보안 정책 지시문을 위반하기 때문에 인라인 스크립트 실행이 거부되었습니다. “ ... ”. 인라인 실행을 사용하려면 ‘unsafe-inline’ 키워드, 해시(‘sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=’) 또는 nonce(‘nonce-...’) 중 하나가 필요합니다.

오류와 관련된 특정 스크립트가 콘솔에서 오류 옆에 표시됩니다.

## <a name="meta-tag-limitations"></a>Meta 태그 제한 사항

`<meta>` 태그 정책은 다음 지시문을 지원하지 않습니다.

* [frame-ancestors](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [report-to](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [sandbox](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

위의 지시문을 지원하려면 이름이 `Content-Security-Policy`인 헤더를 사용하세요. 지시문 문자열은 헤더의 값입니다.

## <a name="test-a-policy-and-receive-violation-reports"></a>정책 테스트 및 위반 보고서 받기

테스트를 사용하면 초기 정책을 빌드할 때 타사 스크립트가 실수로 차단되지 않는지 확인할 수 있습니다.

정책 지시문을 적용하지 않고 일정 시간 동안 정책을 테스트하려면 `<meta>` 태그의 `http-equiv` 특성이나 헤더 기반 정책의 헤더 이름을 `Content-Security-Policy-Report-Only`로 설정합니다. 오류 보고서는 지정된 URL로 JSON 문서로 전송됩니다. 자세한 내용은 [MDN 웹 문서: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)를 참조하세요.

정책이 활성 상태일 때의 위반 보고에 대해 알아보려면 다음 문서를 참조하세요.

* [report-to](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

`report-uri`는 더 이상 사용하지 않는 것이 좋지만 모든 주요 브라우저에서 `report-to`가 지원될 때까지 두 지시문을 모두 사용해야 합니다. `report-uri`에 대한 지원은 브라우저에서 ‘언제든지’ 제거될 수 있으므로 `report-uri`만 단독으로 사용하지 마세요. `report-to`가 완전히 지원되는 시점에 정책에서 `report-uri`에 대한 지원을 제거하세요. `report-to`의 채택 여부를 추적하려면 [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)(report-to를 사용해도 될까요?)를 참조하세요.

릴리스마다 앱의 정책을 테스트하고 업데이트하시기 바랍니다.

## <a name="troubleshoot"></a>문제 해결

* 오류는 브라우저의 개발자 도구 콘솔에 표시됩니다. 브라우저에서는 다음과 같은 정보를 제공합니다.
  * 정책을 준수하지 않는 요소.
  * 차단된 항목을 허용하도록 정책을 수정하는 방법.
* 정책은 클라이언트의 브라우저에서 포함된 모든 지시문을 지원하는 경우에만 완전히 유효합니다. 최신 브라우저 지원 매트릭스는 [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy)(Content-Security-Policy를 사용해도 될까요?)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [MDN 웹 문서: 콘텐츠 보안 정책](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [콘텐츠 보안 정책 수준 2](https://www.w3.org/TR/CSP2/)
* [Google CSP 계산기](https://csp-evaluator.withgoogle.com/)
