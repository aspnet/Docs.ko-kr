---
title: Facebook, Google 및 외부 공급자 인증 없이 ASP.NET Core Identity
author: rick-anderson
description: 를 사용 하지 않고 Facebook, Google, Twitter 등의 계정 사용자 인증을 사용 하는 방법에 대 한 ASP.NET Core Identity 설명입니다.
ms.author: riande
ms.date: 12/10/2019
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
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cd7545a3ddaccedfa64ef5e9d5458c21c651257a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060289"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a>다음을 사용 하지 않고 소셜 로그인 공급자 인증 사용 ASP.NET Core Identity

[Kirk Larkin](https://twitter.com/serpent5) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index> 사용자가 외부 인증 공급자의 자격 증명으로 OAuth 2.0를 사용 하 여 로그인 할 수 있게 하는 방법을 설명 합니다. 이 항목에서 설명 하는 접근 방식에는 인증 공급자로가 포함 되어 있습니다 ASP.NET Core Identity .

이 샘플에서는 **없이** 외부 인증 공급자를 사용 하는 방법을 보여 줍니다 ASP.NET Core Identity . 이 기능은의 모든 기능이 필요 하지는 ASP.NET Core Identity 않지만 여전히 신뢰할 수 있는 외부 인증 공급자와의 통합이 필요한 앱에 유용 합니다.

이 샘플에서는 사용자를 인증 하는 데 [Google 인증](xref:security/authentication/google-logins) 을 사용 합니다. Google 인증을 사용 하면 로그인 프로세스를 관리 하는 복잡 한 여러 가지 복잡성이 Google으로 이동 됩니다. 다른 외부 인증 공급자와 통합 하려면 다음 항목을 참조 하십시오.

* [Facebook 인증](xref:security/authentication/facebook-logins)
* [Microsoft 인증](xref:security/authentication/microsoft-logins)
* [Twitter 인증](xref:security/authentication/twitter-logins)
* [기타 공급자](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuration

`ConfigureServices`메서드에서 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> , 및 메서드를 사용 하 여 앱의 인증 체계를 구성 합니다 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> .

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

에 대 한 호출은 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 앱의를 설정 합니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> . 는 `DefaultScheme` 다음 `HttpContext` 인증 확장 프로그램 메서드에서 사용 하는 기본 체계입니다.

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

앱의 `DefaultScheme` 를 [ Cookie authenticationdefaults로 설정 합니다. authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s")는 Cookie 이러한 확장 메서드에 대 한 기본 체계로 s를 사용 하도록 앱을 구성 합니다. 앱을 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google")로 설정 하면에 대 한 호출에 대 한 기본 체계로 Google을 사용 하도록 앱을 구성 합니다 `ChallengeAsync` . `DefaultChallengeScheme`는 `DefaultScheme`를 재정의합니다. <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>설정 시를 재정의 하는 추가 속성은를 참조 하세요 `DefaultScheme` .

에서 및 호출 `Startup.Configure` 사이에 및를 호출 `UseAuthentication` `UseAuthorization` `UseRouting` `UseEndpoints` 합니다. 이렇게 하면 속성을 설정 `HttpContext.User` 하 고 요청에 대 한 권한 부여 미들웨어를 실행 합니다.

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

인증 체계에 대 한 자세한 내용은 [인증 개념](xref:security/authentication/index#authentication-concepts)을 참조 하세요. 인증에 대 한 자세한 cookie 내용은을 참조 하십시오 <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>권한 부여 적용

`AuthorizeAttribute`컨트롤러, 작업 또는 페이지에 특성을 적용 하 여 앱의 인증 구성을 테스트 합니다. 다음 코드는 인증 된 사용자에 대 한 *개인 정보* 페이지에 대 한 액세스를 제한 합니다.

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>로그아웃

현재 사용자를 로그 아웃 하 고 삭제 하려면 cookie [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)를 호출 합니다. 다음 코드에서는 `Logout` 페이지 처리기를 *인덱스* 페이지에 추가 합니다.

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

에 대 한 호출은 `SignOutAsync` 인증 체계를 지정 하지 않습니다. 의 응용 프로그램 `DefaultScheme` 은 `CookieAuthenticationDefaults.AuthenticationScheme` 대체로 사용 됩니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index> 사용자가 외부 인증 공급자의 자격 증명으로 OAuth 2.0를 사용 하 여 로그인 할 수 있게 하는 방법을 설명 합니다. 이 항목에서 설명 하는 접근 방식에는 인증 공급자로가 포함 되어 있습니다 ASP.NET Core Identity .

이 샘플에서는 **없이** 외부 인증 공급자를 사용 하는 방법을 보여 줍니다 ASP.NET Core Identity . 이 기능은의 모든 기능이 필요 하지는 ASP.NET Core Identity 않지만 여전히 신뢰할 수 있는 외부 인증 공급자와의 통합이 필요한 앱에 유용 합니다.

이 샘플에서는 사용자를 인증 하는 데 [Google 인증](xref:security/authentication/google-logins) 을 사용 합니다. Google 인증을 사용 하면 로그인 프로세스를 관리 하는 복잡 한 여러 가지 복잡성이 Google으로 이동 됩니다. 다른 외부 인증 공급자와 통합 하려면 다음 항목을 참조 하십시오.

* [Facebook 인증](xref:security/authentication/facebook-logins)
* [Microsoft 인증](xref:security/authentication/microsoft-logins)
* [Twitter 인증](xref:security/authentication/twitter-logins)
* [기타 공급자](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuration

`ConfigureServices`메서드에서 `AddAuthentication` , 및 메서드를 사용 하 여 앱의 인증 체계를 구성 합니다 `AddCookie` `AddGoogle` .

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

[Addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) 에 대 한 호출은 앱의 [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)을 설정 합니다. 는 `DefaultScheme` 다음 `HttpContext` 인증 확장 프로그램 메서드에서 사용 하는 기본 체계입니다.

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

앱의 `DefaultScheme` 를 [ Cookie authenticationdefaults로 설정 합니다. authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s")는 Cookie 이러한 확장 메서드에 대 한 기본 체계로 s를 사용 하도록 앱을 구성 합니다. 앱을 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google")로 설정 하면에 대 한 호출에 대 한 기본 체계로 Google을 사용 하도록 앱을 구성 합니다 `ChallengeAsync` . `DefaultChallengeScheme`는 `DefaultScheme`를 재정의합니다. <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>설정 시를 재정의 하는 추가 속성은를 참조 하세요 `DefaultScheme` .

`Configure`메서드에서 메서드를 호출 하 여 `UseAuthentication` 속성을 설정 하는 인증 미들웨어를 호출 합니다 `HttpContext.User` . 또는를 `UseAuthentication` 호출 하기 전에 메서드를 호출 합니다 `UseMvcWithDefaultRoute` `UseMvc` .

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

인증 체계에 대 한 자세한 내용은 [인증 개념](xref:security/authentication/index#authentication-concepts)을 참조 하세요. 인증에 대 한 자세한 cookie 내용은을 참조 하십시오 <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>권한 부여 적용

`AuthorizeAttribute`컨트롤러, 작업 또는 페이지에 특성을 적용 하 여 앱의 인증 구성을 테스트 합니다. 다음 코드는 인증 된 사용자에 대 한 *개인 정보* 페이지에 대 한 액세스를 제한 합니다.

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>로그아웃

현재 사용자를 로그 아웃 하 고 삭제 하려면 cookie [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)를 호출 합니다. 다음 코드에서는 `Logout` 페이지 처리기를 *인덱스* 페이지에 추가 합니다.

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

에 대 한 호출은 `SignOutAsync` 인증 체계를 지정 하지 않습니다. 의 응용 프로그램 `DefaultScheme` 은 `CookieAuthenticationDefaults.AuthenticationScheme` 대체로 사용 됩니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
