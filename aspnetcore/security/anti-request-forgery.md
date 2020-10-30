---
title: ASP.NET Core에서 교차 사이트 요청 위조 (XSRF/CSRF) 공격 방지
author: steve-smith
description: 악의적인 웹 사이트가 클라이언트 브라우저와 앱 간의 상호 작용에 영향을 줄 수 있는 웹 앱에 대 한 공격을 방지 하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc, devx-track-js
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
uid: security/anti-request-forgery
ms.openlocfilehash: 197954965ee57b2a44ad0217d79ba142114e7df6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060848"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>ASP.NET Core에서 교차 사이트 요청 위조 (XSRF/CSRF) 공격 방지

작성자, [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)및 [Steve Smith](https://ardalis.com/)

사이트 간 요청 위조 (XSRF 또는 CSRF 라고도 함)는 악의적인 웹 앱이 클라이언트 브라우저와 해당 브라우저를 신뢰 하는 웹 앱 간의 상호 작용에 영향을 줄 수 있는 웹 호스팅 앱에 대 한 공격입니다. 웹 브라우저에서 웹 사이트에 대 한 모든 요청과 함께 일부 형식의 인증 토큰을 자동으로 보내기 때문에 이러한 공격이 가능 합니다. 이러한 형태의 악용은 공격이 사용자의 이전에 인증 된 세션을 활용 하기 때문에 *한 번 클릭 공격* 또는 *세션 riding* 라고도 합니다.

CSRF 공격의 예는 다음과 같습니다.

1. 사용자가 `www.good-banking-site.com` 폼 인증을 사용 하 여에 로그인 합니다. 서버가 사용자를 인증 하 고 인증을 포함 하는 응답을 발급 합니다 cookie . 사이트는 수신 하는 모든 요청을 유효한 인증으로 신뢰 하기 때문에 공격에 취약 cookie 합니다.
1. 사용자가 악의적인 사이트인를 방문 합니다 `www.bad-crook-site.com` .

   악의적인 사이트인는 `www.bad-crook-site.com` 다음과 유사한 HTML 양식을 포함 합니다.

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   양식이 `action` 악의적인 사이트가 아닌 취약 한 사이트에 게시 되는 것을 볼 수 있습니다. CSRF의 "교차 사이트" 부분입니다.

1. 사용자가 제출 단추를 선택 합니다. 브라우저가 요청을 만들고 요청 된 cookie 도메인에 대 한 인증을 자동으로 포함 `www.good-banking-site.com` 합니다.
1. 요청은 `www.good-banking-site.com` 사용자의 인증 컨텍스트를 사용 하 여 서버에서 실행 되며 인증 된 사용자가 수행할 수 있는 모든 작업을 수행할 수 있습니다.

사용자가 폼을 제출 하는 단추를 선택 하는 시나리오 외에도 악의적인 사이트는 다음과 같은 일을 할 수 있습니다.

* 양식을 자동으로 전송 하는 스크립트를 실행 합니다.
* 양식 제출을 AJAX 요청으로 보냅니다.
* CSS를 사용 하 여 폼을 숨깁니다.

이러한 대체 시나리오에는 처음에 악성 사이트를 방문 하는 것 외에 다른 작업 또는 사용자 입력이 필요 하지 않습니다.

HTTPS를 사용 하는 경우 CSRF 공격을 방지 하지 않습니다. 악의적인 사이트는 안전 하지 않은 요청을 보낼 수 있는 것과 같은 방법으로 요청을 쉽게 보낼 수 있습니다 `https://www.good-banking-site.com/` .

일부 공격은 GET 요청에 응답 하는 끝점을 대상으로 하며,이 경우 이미지 태그를 사용 하 여 작업을 수행할 수 있습니다. 이러한 형태의 공격은 이미지를 허용 하지만 JavaScript를 차단 하는 포럼 사이트에서 일반적입니다. 변수 또는 리소스가 변경 되는 GET 요청에 대 한 상태를 변경 하는 앱은 악의적인 공격에 취약 합니다. **변경 상태가 안전 하지 않은 가져오기 요청입니다. 모범 사례는 GET 요청에 대 한 상태를 변경 하지 않는 것입니다.**

CSRF 공격은 인증에 s를 사용 하는 웹 앱에 대해 cookie 다음과 같은 이유로 가능 합니다.

* 브라우저 cookie 는 웹 앱에 의해 발급 된를 저장 합니다.
* 저장 된 cookie 에는 cookie 인증 된 사용자에 대 한 세션 s가 포함 됩니다.
* 브라우저 cookie 는 브라우저에서 앱에 대 한 요청이 생성 된 방법에 관계 없이 모든 요청에 대해 도메인에 연결 된 모든를 웹 앱에 보냅니다.

그러나 CSRF 공격은 s를 악용 하는 것으로 제한 되지 않습니다 cookie . 예를 들어, 기본 및 다이제스트 인증에도 취약 합니다. 사용자가 기본 또는 다이제스트 인증을 사용 하 여 로그인 한 후에는 세션이 종료 될 때까지 브라우저에서 자동으로 자격 증명을 보냅니다 &dagger; .

&dagger;이 컨텍스트에서 *세션* 은 사용자를 인증 하는 데 사용 되는 클라이언트 쪽 세션을 나타냅니다. 서버 쪽 세션 또는 [ASP.NET Core 세션 미들웨어](xref:fundamentals/app-state)와 관련이 없습니다.

사용자는 예방 조치를 취하여 CSRF 취약성 으로부터 보호할 수 있습니다.

* 웹 앱 사용을 마치면 웹 앱에서 로그 오프 합니다.
* cookie정기적으로 브라우저를 지웁니다.

그러나 CSRF 취약점은 기본적으로 최종 사용자가 아닌 웹 앱에 문제가 있습니다.

## <a name="authentication-fundamentals"></a>인증 기본 사항

Cookie기반 인증은 널리 사용 되는 인증 형식입니다. 토큰 기반 인증 시스템은 특히 SPAs (단일 페이지 응용 프로그램)에 널리 사용 됩니다.

### <a name="no-loccookie-based-authentication"></a>Cookie기반 인증

사용자가 사용자 이름 및 암호를 사용 하 여 인증 하는 경우 인증 및 권한 부여에 사용할 수 있는 인증 티켓이 포함 된 토큰이 발급 됩니다. 토큰은 cookie 클라이언트에서 수행 하는 모든 요청과 함께 제공 되는으로 저장 됩니다. 이를 생성 하 고 유효성을 검사 하 cookie 는 것은 인증 미들웨어에 의해 수행 됩니다 Cookie . [미들웨어](xref:fundamentals/middleware/index) 는 사용자 보안 주체를 암호화 된로 serialize 합니다 cookie . 후속 요청에서 미들웨어는의 유효성을 검사 cookie 하 고, 보안 주체를 다시 만들고, 사용자를 [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)의 [사용자](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) 속성에 할당 합니다.

### <a name="token-based-authentication"></a>토큰 기반 인증

사용자가 인증 되 면 토큰 (위조 방지 토큰이 아님)이 발급 됩니다. 토큰에는 [클레임](/dotnet/framework/security/claims-based-identity-model) 형식의 사용자 정보 또는 앱이 앱에서 유지 관리 되는 사용자 상태를 가리키는 참조 토큰이 포함 됩니다. 사용자가 인증을 요구 하는 리소스에 액세스 하려고 하면 토큰은 전달자 토큰의 형태로 추가 권한 부여 헤더를 사용 하 여 앱으로 전송 됩니다. 그러면 앱 상태 비저장이 수행 됩니다. 각 후속 요청에서 토큰은 서버 쪽 유효성 검사에 대 한 요청에 전달 됩니다. 이 토큰은 *암호화* 되지 않습니다. *인코딩됩니다* . 서버에서 토큰은 정보에 액세스 하기 위해 디코딩됩니다. 후속 요청에서 토큰을 보내려면 브라우저의 로컬 저장소에 토큰을 저장 합니다. 토큰이 브라우저의 로컬 저장소에 저장 된 경우 CSRF 취약성에 대해 걱정 하지 마세요. CSRF는 토큰을에 저장 하는 경우에 문제가 됩니다 cookie . 자세한 내용은 GitHub 문제 [SPA 코드 샘플 2 개 추가 cookie ](https://github.com/dotnet/AspNetCore.Docs/issues/13369)를 참조 하세요.

### <a name="multiple-apps-hosted-at-one-domain"></a>한 도메인에서 호스트 되는 여러 앱

공유 호스팅 환경은 세션 하이재킹, 로그인 CSRF 및 기타 공격에 취약 합니다.

`example1.contoso.net`및 `example2.contoso.net` 는 서로 다른 호스트 이지만 도메인의 호스트 간에 암시적 트러스트 관계가 있습니다 `*.contoso.net` . 이 암시적 트러스트 관계를 사용 하면 신뢰할 수 없는 호스트가 서로에 게 영향을 줄 수 있습니다 cookie . AJAX 요청을 제어 하는 동일한 원본 정책은 반드시 HTTP s에 적용 되는 것은 아닙니다 cookie .

동일한 도메인에서 호스트 되는 앱 간에 트러스트 된를 활용 하는 공격은 cookie 도메인을 공유 하지 않도록 방지할 수 있습니다. 각 앱이 자체 도메인에서 호스트 되는 경우에는 악용할 암시적 cookie 트러스트 관계가 없습니다.

## <a name="aspnet-core-antiforgery-configuration"></a>위조 방지 구성 ASP.NET Core

> [!WARNING]
> ASP.NET Core은 [ASP.NET Core 데이터 보호](xref:security/data-protection/introduction)를 사용 하 여 위조 방지를 구현 합니다. 서버 팜에서 작동 하도록 데이터 보호 스택을 구성 해야 합니다. 자세한 내용은 [데이터 보호 구성](xref:security/data-protection/configuration/overview) 을 참조 하세요.

::: moniker range=">= aspnetcore-3.0"

에서 다음 Api 중 하나가 호출 될 때 위조 방지 미들웨어는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가 됩니다 `Startup.ConfigureServices` .

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

에서가 호출 되 면 위조 방지 미들웨어가 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가 됩니다. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>`Startup.ConfigureServices`

::: moniker-end

ASP.NET Core 2.0 이상에서 [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) 는 위조 방지 토큰을 HTML 양식 요소에 삽입 합니다. 파일의 다음 태그는 Razor 위조 방지 토큰을 자동으로 생성 합니다.

```cshtml
<form method="post">
    ...
</form>
```

마찬가지로, [IHtmlHelper html.beginform](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) 는 폼의 메서드가 GET이 아닌 경우 기본적으로 위조 방지 토큰을 생성 합니다.

HTML 양식 요소에 대 한 위조 방지 토큰 자동 생성은 태그에 `<form>` 특성이 포함 되어 있고 다음 중 하나에 해당 하는 경우에 발생 합니다 `method="post"` .

* Action 특성이 비어 있는 경우 ( `action=""` )
* Action 특성을 제공 하지 않습니다 ( `<form method="post">` ).

HTML 양식 요소에 대 한 위조 방지 토큰 자동 생성을 사용 하지 않도록 설정할 수 있습니다.

* 특성을 사용 하 여 위조 방지 토큰을 명시적으로 사용 하지 않도록 설정 합니다 `asp-antiforgery` .

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* Form 요소는 태그 도우미 [! 옵트아웃 기호](xref:mvc/views/tag-helpers/intro#opt-out)를 사용 하 여 태그 도우미에서 옵트아웃 (opt out) 합니다.

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* 뷰에서를 제거 합니다 `FormTagHelper` . 뷰에 `FormTagHelper` 다음 지시문을 추가 하 여 뷰에서를 제거할 수 있습니다 Razor .

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [ Razor 페이지](xref:razor-pages/index) 는 XSRF/csrf에서 자동으로 보호 됩니다. 자세한 내용은 [XSRF/CSRF 및 Razor Pages](xref:razor-pages/index#xsrf)를 참조 하세요.

CSRF 공격 으로부터 보호 하는 가장 일반적인 방법은 STP ( *동기화 장치 토큰 패턴* )를 사용 하는 것입니다. 사용자가 양식 데이터를 사용 하 여 페이지를 요청 하면 STP가 사용 됩니다.

1. 서버는 현재 사용자의 id와 연결 된 토큰을 클라이언트에 보냅니다.
1. 클라이언트에서 확인을 위해 토큰을 서버에 다시 보냅니다.
1. 서버가 인증 된 사용자의 id와 일치 하지 않는 토큰을 받으면 요청이 거부 됩니다.

토큰은 고유 하 고 예측할 수 없습니다. 토큰을 사용 하 여 일련의 요청을 적절 하 게 시퀀싱 할 수도 있습니다. 예를 들어 1 페이지 > 페이지 2 > 페이지 3)에 대 한 요청 시퀀스를 확인 합니다. ASP.NET Core MVC 및 Razor 페이지 템플릿의 모든 양식은 위조 방지 토큰을 생성 합니다. 다음 뷰 예제에서는 위조 방지 토큰을 생성 합니다.

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

`<form>`HTML 도우미에서 태그 도우미를 사용 하지 않고 요소에 위조 방지 토큰을 명시적으로 추가 합니다 [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) .

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

위의 각 경우에 ASP.NET Core는 다음과 유사한 숨겨진 양식 필드를 추가 합니다.

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

위조 방지 토큰을 사용 하기 위한 세 가지 [필터](xref:mvc/controllers/filters) 를 포함 하는 ASP.NET Core:

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>위조 방지 옵션

[위조 방지 옵션](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) 사용자 지정 `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

&dagger;`Cookie` [ Cookie 작성기](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) 클래스의 속성을 사용 하 여 위조 방지 속성을 설정 합니다.

| 옵션 | 설명 |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 위조 방지를 만드는 데 사용 되는 설정을 결정 합니다 cookie . |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 위조 방지 시스템이 뷰에서 위조 방지 토큰을 렌더링 하는 데 사용 하는 숨겨진 양식 필드의 이름입니다. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 위조 방지 시스템에서 사용 하는 헤더의 이름입니다. 이면 `null` 시스템은 폼 데이터만 고려 합니다. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 헤더 생성을 표시 하지 않을 지 여부를 지정 합니다 `X-Frame-Options` . 기본적으로 헤더는 "SAMEORIGIN" 값을 사용 하 여 생성 됩니다. 기본값은 `false`입니다. |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| 옵션 | 설명 |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 위조 방지를 만드는 데 사용 되는 설정을 결정 합니다 cookie . |
| [Cookie도메인](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | 의 도메인 cookie 입니다. 기본값은 `null`입니다. 이 속성은 사용 되지 않으며 이후 버전에서 제거 될 예정입니다. 대신를 사용할 것을 권장 합니다 Cookie . 도메인. |
| [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | cookie의 이름입니다. 설정 하지 않으면 시스템은 [기본 Cookie 접두사](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (")로 시작 하는 고유한 이름을 생성 합니다. AspNetCore. "). 이 속성은 사용 되지 않으며 이후 버전에서 제거 될 예정입니다. 대신를 사용할 것을 권장 합니다 Cookie . 이름의. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | 에 설정 된 경로 cookie 입니다. 이 속성은 사용 되지 않으며 이후 버전에서 제거 될 예정입니다. 대신를 사용할 것을 권장 합니다 Cookie . Path. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 위조 방지 시스템이 뷰에서 위조 방지 토큰을 렌더링 하는 데 사용 하는 숨겨진 양식 필드의 이름입니다. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 위조 방지 시스템에서 사용 하는 헤더의 이름입니다. 이면 `null` 시스템은 폼 데이터만 고려 합니다. |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | 위조 방지 시스템에 HTTPS가 필요한 지 여부를 지정 합니다. 이면 `true` HTTPS가 아닌 요청이 실패 합니다. 기본값은 `false`입니다. 이 속성은 사용 되지 않으며 이후 버전에서 제거 될 예정입니다. 대신를 설정 하는 것이 좋습니다 Cookie . SecurePolicy. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 헤더 생성을 표시 하지 않을 지 여부를 지정 합니다 `X-Frame-Options` . 기본적으로 헤더는 "SAMEORIGIN" 값을 사용 하 여 생성 됩니다. 기본값은 `false`입니다. |

::: moniker-end

자세한 내용은 [ Cookie authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions)를 참조 하세요.

## <a name="configure-antiforgery-features-with-iantiforgery"></a>I위조 방지를 사용 하 여 위조 방지 기능 구성

[I방지 위조](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 는 위조 방지 기능을 구성 하는 API를 제공 합니다. `IAntiforgery` 클래스의 메서드에서 요청 될 수 있습니다 `Configure` `Startup` . 다음 예제에서는 앱의 홈페이지에서 미들웨어를 사용 하 여 위조 방지 토큰을 생성 하 고 응답에서로 보냅니다 cookie (이 항목의 뒷부분에서 설명 하는 기본 각도 명명 규칙 사용).

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a>위조 방지 유효성 검사 필요

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) 은 개별 작업, 컨트롤러 또는 전역에 적용 될 수 있는 작업 필터입니다. 이 필터가 적용 된 작업에 대 한 요청은 유효한 위조 방지 토큰을 포함 하지 않는 한 차단 됩니다.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

특성에는 `ValidateAntiForgeryToken` HTTP GET 요청을 포함 하 여 표시 되는 작업 메서드에 대 한 요청 토큰이 필요 합니다. 특성을 `ValidateAntiForgeryToken` 앱의 컨트롤러에서 적용 하는 경우 특성을 사용 하 여 재정의할 수 있습니다 `IgnoreAntiforgeryToken` .

> [!NOTE]
> ASP.NET Core는 위조 방지 토큰 추가를 지원 하지 않으므로 요청을 자동으로 가져옵니다.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>안전 하지 않은 HTTP 메서드에 대 한 위조 방지 토큰의 유효성을 자동으로 검사 합니다.

ASP.NET Core 앱은 safe HTTP 메서드 (GET, HEAD, OPTIONS 및 TRACE)에 대해 위조 방지 토큰을 생성 하지 않습니다. 특성을 광범위 하 게 적용 한 `ValidateAntiForgeryToken` 다음 특성을 사용 하 여 재정의 하는 대신 `IgnoreAntiforgeryToken` [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) 특성을 사용할 수 있습니다. 이 특성은 특성에서 동일 하 게 작동 `ValidateAntiForgeryToken` 합니다. 단, 다음 HTTP 메서드를 사용 하 여 생성 된 요청에 대 한 토큰은 필요 하지 않습니다.

* GET
* HEAD
* OPTIONS
* TRACE

비 API 시나리오에는을 광범위 하 게 사용 하는 것이 좋습니다 `AutoValidateAntiforgeryToken` . 이렇게 하면 게시 작업은 기본적으로 보호 됩니다. 대신, `ValidateAntiForgeryToken` 개별 작업 메서드에가 적용 되지 않는 한, 기본적으로 위조 방지 토큰을 무시 하는 것이 좋습니다. 앱이 CSRF 공격에 취약 한 상태로 유지 하 여 POST 작업 메서드를 실수로 보호 되지 않은 상태로 남겨둘 수 있습니다. 모든 게시물은 위조 방지 토큰을 전송 해야 합니다.

Api에는 토큰의 일부가 아닌를 보내기 위한 자동 메커니즘이 없습니다 cookie . 구현은 클라이언트 코드 구현에 따라 달라질 수 있습니다. 몇 가지 예가 아래에 나와 있습니다.

클래스 수준 예제:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

전역 예:

::: moniker range="< aspnetcore-3.0"

서비스. AddMvc (options => 옵션입니다. Filters. Add (new AutoValidateAntiforgeryTokenAttribute ()));

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a>전역 또는 컨트롤러 위조 방지 특성 재정의

[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) 필터는 지정 된 작업 (또는 컨트롤러)에 위조 방지 토큰이 필요 하지 않도록 방지 하는 데 사용 됩니다. 적용 되는 경우이 필터 `ValidateAntiForgeryToken` 는 `AutoValidateAntiforgeryToken` 더 높은 수준 (전역 또는 컨트롤러)으로 지정 된 필터를 재정의 합니다.

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a>인증 후 토큰 새로 고침

사용자를 보기 또는 페이지 페이지로 리디렉션하여 사용자를 인증 한 후에 토큰을 새로 고쳐야 합니다 Razor .

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX 및 SPAs

기존의 HTML 기반 앱에서 위조 방지 토큰은 숨겨진 양식 필드를 사용 하 여 서버로 전달 됩니다. 최신 JavaScript 기반 앱과 SPAs에서 많은 요청은 프로그래밍 방식으로 수행 됩니다. 이러한 AJAX 요청은 다른 기술 (예: 요청 헤더 또는 cookie s)을 사용 하 여 토큰을 보낼 수 있습니다.

cookieS를 사용 하 여 인증 토큰을 저장 하 고 서버에서 API 요청을 인증 하는 경우 CSRF는 잠재적인 문제입니다. 로컬 저장소를 사용 하 여 토큰을 저장 하는 경우 로컬 저장소의 값이 모든 요청을 통해 서버에 자동으로 전송 되지 않으므로 CSRF 취약성이 완화 될 수 있습니다. 따라서 로컬 저장소를 사용 하 여 클라이언트에 위조 방지 토큰을 저장 하 고 요청 헤더로 토큰을 전송 하는 것이 좋습니다.

### <a name="javascript"></a>JavaScript

보기에서 JavaScript를 사용 하 여 뷰 내에서 서비스를 사용 하 여 토큰을 만들 수 있습니다. [AspNetCore](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 를 뷰에 삽입 하 고 [Getandstoretokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)를 호출 합니다.

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

이 접근 방식을 사용 하면 cookie 서버에서를 설정 하거나 클라이언트에서 해당 항목을 읽는 것을 직접 처리 하지 않아도 됩니다.

앞의 예제에서는 JavaScript를 사용 하 여 AJAX POST 헤더에 대 한 숨겨진 필드 값을 읽습니다.

JavaScript는 s에서 토큰에 액세스 하 cookie 고 cookie 의 내용을 사용 하 여 토큰 값을 사용 하 여 헤더를 만들 수도 있습니다.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

스크립트가 라는 헤더에서 토큰을 보내도록 요청 하는 경우 `X-CSRF-TOKEN` 위조 방지 서비스를 구성 하 여 헤더를 찾습니다 `X-CSRF-TOKEN` .

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

다음 예제에서는 JavaScript를 사용 하 여 적절 한 헤더를 사용 하는 AJAX 요청을 만듭니다.

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a>AngularJS

AngularJS는 규칙을 사용 하 여 CSRF를 처리 합니다. 서버에서 cookie 이름이 인을 보내면 `XSRF-TOKEN` AngularJS `$http` 서비스는 cookie 서버에 요청을 보낼 때 헤더에 값을 추가 합니다. 이 프로세스는 자동입니다. 헤더는 클라이언트에서 명시적으로 설정할 필요가 없습니다. 헤더 이름은 `X-XSRF-TOKEN` 입니다. 서버에서이 헤더를 검색 하 고 내용의 유효성을 검사 해야 합니다.

ASP.NET Core API가 응용 프로그램 시작 시이 규칙을 사용 하려면 다음을 수행 합니다.

* 호출 된에서 토큰을 제공 하도록 앱을 cookie 구성 `XSRF-TOKEN` 합니다.
* 라는 헤더를 찾도록 위조 방지 서비스를 구성 `X-XSRF-TOKEN` 합니다.

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="extend-antiforgery"></a>위조 방지 확장

[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) 형식을 사용 하면 개발자가 각 토큰에서 추가 데이터를 라운드트립 하 여 csrf 시스템의 동작을 확장할 수 있습니다. [Getadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) 메서드는 필드 토큰이 생성 될 때마다 호출 되며 반환 값은 생성 된 토큰에 포함 됩니다. 구현자는 타임 스탬프, nonce 또는 다른 값을 반환 하 고, 토큰의 유효성을 검사할 때 [Validateadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) 를 호출 하 여이 데이터의 유효성을 검사할 수 있습니다. 클라이언트의 사용자 이름이 이미 생성 된 토큰에 포함 되어 있으므로이 정보를 포함할 필요가 없습니다. 토큰에 추가 데이터가 포함 되어 있지만가 구성 되지 않은 경우 `IAntiForgeryAdditionalDataProvider` 보충 데이터의 유효성이 검사 되지 않습니다.

## <a name="additional-resources"></a>추가 리소스

* OWASP ( [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) )의 [csrf](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))
* <xref:host-and-deploy/web-farm>
