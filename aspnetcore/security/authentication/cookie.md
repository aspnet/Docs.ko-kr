---
title: 인증을 사용 cookie 하지 않고 사용 ASP.NET Core Identity
author: rick-anderson
description: 를 사용 하지 않고 인증을 사용 하는 방법을 알아봅니다 cookie ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: security/authentication/cookie
ms.openlocfilehash: 5b1f1bb3de7126c401a81b89b99a45c7e45f8f8d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586295"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>인증을 사용 cookie 하지 않고 사용 ASP.NET Core Identity

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Identity 는 로그인을 만들고 유지 관리 하기 위한 완전 한 기능을 갖춘 완전 한 인증 공급자입니다. 그러나를 cookie 사용 하지 않는 기반 인증 공급자를 ASP.NET Core Identity 사용할 수 있습니다. 자세한 내용은 <xref:security/authentication/identity>를 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/cookie/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱의 데모용으로, 가상 사용자 (민 Rodriguez)의 사용자 계정이 앱에 하드 코딩 됩니다. **전자 메일** 주소 `maria.rodriguez@contoso.com` 및 암호를 사용 하 여 사용자를 로그인 합니다. 사용자는 `AuthenticateUser` *Pages/Account/Login. cshtml* 파일의 메서드에서 인증 됩니다. 실제 예제에서는 사용자가 데이터베이스에 대해 인증 됩니다.

## <a name="configuration"></a>구성

`Startup.ConfigureServices`메서드에서 및 메서드를 사용 하 여 인증 미들웨어 서비스를 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 만듭니다 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> .

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 에 전달 된는 `AddAuthentication` 앱에 대 한 기본 인증 체계를 설정 합니다. `AuthenticationScheme` 는 인증 인스턴스가 여러 개 있고 cookie [특정 스키마를 사용 하 여 권한을 부여](xref:security/authorization/limitingidentitybyscheme)하려는 경우에 유용 합니다. `AuthenticationScheme`를 [ Cookie authenticationdefaults로 설정 합니다. authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) 은 Cookie 스키마에 대해 "s" 값을 제공 합니다. 체계를 구별 하는 모든 문자열 값을 제공할 수 있습니다.

앱의 인증 체계가 앱의 인증 체계와 다릅니다 cookie . cookie인증 체계가에 제공 되지 않는 경우 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s")를 사용 합니다.

인증 cookie 의 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 속성은 기본적으로로 설정 됩니다 `true` . cookie사이트 방문자가 데이터 수집에 동의한 하지 않은 경우에는 인증을 사용할 수 있습니다. 자세한 내용은 <xref:security/gdpr#essential-cookies>를 참조하세요.

에서 `Startup.Configure` 및를 `UseAuthentication` 호출 `UseAuthorization` 하 여 속성을 설정 하 `HttpContext.User` 고 요청에 대 한 권한 부여 미들웨어를 실행 합니다. `UseAuthentication`를 `UseAuthorization` 호출 하기 전에 및 메서드를 호출 합니다 `UseEndpoints` .

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>클래스는 인증 공급자 옵션을 구성 하는 데 사용 됩니다.

`CookieAuthenticationOptions`메서드의 인증에 대 한 서비스 구성에서 설정 합니다 `Startup.ConfigureServices` .

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Cookie 정책 미들웨어

[ Cookie 정책 미들웨어](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) cookie 를 통해 정책 기능을 사용할 수 있습니다. 응용 프로그램 처리 파이프라인에 미들웨어를 추가 하는 것은 순서를 구분 하기 때문에 &mdash; 파이프라인에 등록 된 다운스트림 구성 요소에만 영향을 줍니다.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>정책 미들웨어에 제공 된를 사용 Cookie 하 여 cookie cookie cookie 가 추가 되거나 삭제 될 때 처리 및 처리 처리기에 대 한 전역 특성을 제어할 수 있습니다.

기본값은 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> `SameSiteMode.Lax` OAuth2 인증을 허용 하는 것입니다. 의 동일한 사이트 정책을 엄격 하 게 적용 하려면를 `SameSiteMode.Strict` 설정 `MinimumSameSitePolicy` 합니다. 이 설정은 OAuth2 및 다른 크로스-원본 인증 스키마를 중단 하지만 cookie 원본 간 요청 처리에 의존 하지 않는 다른 유형의 앱에 대 한 보안 수준을 강화 합니다.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Cookie의 정책 미들웨어 설정은 `MinimumSameSitePolicy` `Cookie.SameSite` `CookieAuthenticationOptions` 아래 행렬에 따라 설정의 설정에 영향을 줄 수 있습니다.

| MinimumSameSitePolicy | Cookie. SameSite | 결과 Cookie . SameSite 설정 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>인증 만들기 cookie

cookie보유 사용자 정보를 만들려면를 구성 <xref:System.Security.Claims.ClaimsPrincipal> 합니다. 사용자 정보는 serialize 되 고에 저장 됩니다 cookie . 

필요한를 사용 하 여를 만들고를 <xref:System.Security.Claims.ClaimsIdentity> <xref:System.Security.Claims.Claim> 호출 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 하 여 사용자를 로그인 합니다.

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

`SignInAsync` 암호화 된를 만들어 cookie 현재 응답에 추가 합니다. `AuthenticationScheme`을 지정 하지 않으면 기본 체계가 사용 됩니다.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> 는 기본적으로 몇 가지 특정 경로 (예: 로그인 경로 및 로그 아웃 경로) 에서만 사용 됩니다. 자세한 내용은 [ Cookie authenticationhandler 원본](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)을 참조 하세요.

ASP.NET Core의 [데이터 보호](xref:security/data-protection/using-data-protection) 시스템이 암호화에 사용 됩니다. 여러 컴퓨터에서 호스트 되는 앱의 경우, 앱 간에 부하를 분산 하거나 웹 팜을 사용 하 여 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 하 여 동일한 키 링 및 앱 식별자를 사용 하도록 구성 합니다.

## <a name="sign-out"></a>로그아웃

현재 사용자를 로그 아웃 하 고 삭제 하려면 cookie 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> .

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

`CookieAuthenticationDefaults.AuthenticationScheme`(또는 " Cookie s")가 구성표 (예: "Contoso")로 사용 되지 않는 경우 Cookie 인증 공급자를 구성할 때 사용 되는 체계를 제공 합니다. 그렇지 않으면 기본 체계가 사용 됩니다.

브라우저가 닫히면 세션 기반 cookie s (비영구 s)가 자동으로 삭제 cookie 되지만 cookie 개별 탭이 닫히면 s는 제거 되지 않습니다. 서버에 탭 또는 브라우저 닫기 이벤트에 대 한 알림이 표시 되지 않습니다.

## <a name="react-to-back-end-changes"></a>백 엔드 변경 내용에 대응

가 cookie 만들어지면는 cookie id의 단일 원본입니다. 백 엔드 시스템에서 사용자 계정을 사용할 수 없는 경우:

* 앱의 cookie 인증 시스템은 인증에 따라 요청을 계속 처리 합니다 cookie .
* 인증이 유효한 경우 사용자는 앱에 로그인 상태를 유지 합니다 cookie .

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>이벤트를 사용 하 여 id의 유효성 검사를 가로채 고 재정의할 수 있습니다 cookie . 모든 요청에서의 유효성을 검사 하면 cookie 해지 된 사용자가 앱에 액세스 하는 위험을 완화할 수 있습니다.

유효성 검사에 대 한 한 가지 방법은 cookie 사용자 데이터베이스가 변경 되는 시기를 추적 하는 것입니다. 사용자가 발급 된 이후에 데이터베이스가 변경 되지 않은 경우에 cookie 도 해당 사용자를 다시 인증할 필요가 없습니다 cookie . 샘플 앱에서 데이터베이스는에서 구현 되 `IUserRepository` 고 값을 저장 합니다 `LastChanged` . 데이터베이스에서 사용자를 업데이트 하는 경우 값은 `LastChanged` 현재 시간으로 설정 됩니다.

cookie값에 따라 데이터베이스가 변경 될 때를 무효화 하려면 데이터베이스의 `LastChanged` 현재 값이 포함 된 클레임을 사용 하 여를 만듭니다 cookie `LastChanged` `LastChanged` .

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

이벤트에 대 한 재정의를 구현 하려면 `ValidatePrincipal` 에서 파생 되는 클래스에 다음 시그니처를 사용 하 여 메서드를 작성 합니다 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> .

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

다음은의 구현 예제입니다 `CookieAuthenticationEvents` .

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

메서드에서 서비스를 등록 하는 동안 events 인스턴스를 등록 cookie `Startup.ConfigureServices` 합니다. 클래스에 대해 범위가 지정 된 [서비스 등록](xref:fundamentals/dependency-injection#service-lifetimes) 을 제공 합니다 `CustomCookieAuthenticationEvents` .

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

사용자의 이름이 &mdash; 보안에 영향을 주지 않는 결정을 업데이트 하는 상황을 고려 합니다. 사용자 보안 주체를 destructively 업데이트 하려면를 호출 하 `context.ReplacePrincipal` 고 `context.ShouldRenew` 속성을로 설정 `true` 합니다.

> [!WARNING]
> 여기에 설명 된 방법은 모든 요청에서 트리거됩니다. 모든 cookie 요청에서 모든 사용자에 대 한 인증의 유효성을 검사 하면 앱에 대 한 성능 저하가 발생할 수 있습니다.

## <a name="persistent-cookies"></a>영구 cookie s

가 cookie 브라우저 세션에서 지속 되도록 할 수 있습니다. 이 지 속성은 로그인 또는 유사한 메커니즘에서 "사용자 이름" 확인란을 사용 하 여 명시적인 사용자 동의로만 설정 해야 합니다. 

다음 코드 조각에서는 id를 만들고 cookie 브라우저 클로저를 통해 해당 하는 해당 하는를 만듭니다. 이전에 구성 된 슬라이딩 만료 설정은 모두 적용 됩니다. 브라우저를 cookie 닫을 때가 만료 되 면 브라우저가 cookie 다시 시작 되 면를 지웁니다.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>에서로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> .

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>절대 cookie 만료

절대 만료 시간은를 사용 하 여 설정할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> . 영구을 만들려면 cookie `IsPersistent` 도 설정 해야 합니다. 그렇지 않은 경우는 cookie 세션 기반 수명으로 생성 되며 보유 한 인증 티켓 전이나 후에 만료 될 수 있습니다. `ExpiresUtc`가 설정 되 면 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> 설정 된 경우의 옵션 값을 재정의 합니다 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> .

다음 코드 조각에서는 id를 만들고 해당 id를 cookie 20 분 동안 지속 합니다. 이렇게 하면 이전에 구성 된 슬라이딩 만료 설정이 무시 됩니다.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core Identity 는 로그인을 만들고 유지 관리 하기 위한 완전 한 기능을 갖춘 완전 한 인증 공급자입니다. 그러나를 cookie 사용 하지 않는 기반 인증 공급자를 ASP.NET Core Identity 사용할 수 있습니다. 자세한 내용은 <xref:security/authentication/identity>를 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/cookie/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱의 데모용으로, 가상 사용자 (민 Rodriguez)의 사용자 계정이 앱에 하드 코딩 됩니다. **전자 메일** 주소 `maria.rodriguez@contoso.com` 및 암호를 사용 하 여 사용자를 로그인 합니다. 사용자는 `AuthenticateUser` *Pages/Account/Login. cshtml* 파일의 메서드에서 인증 됩니다. 실제 예제에서는 사용자가 데이터베이스에 대해 인증 됩니다.

## <a name="configuration"></a>구성

앱이 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 사용 하지 않는 경우 프로젝트 파일에서 [AspNetCore Cookie 에 대 한 패키지 참조를 만듭니다. s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 패키지.

`Startup.ConfigureServices`메서드에서 및 메서드를 사용 하 여 인증 미들웨어 서비스를 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 만듭니다 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> .

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 에 전달 된는 `AddAuthentication` 앱에 대 한 기본 인증 체계를 설정 합니다. `AuthenticationScheme` 는 인증 인스턴스가 여러 개 있고 cookie [특정 스키마를 사용 하 여 권한을 부여](xref:security/authorization/limitingidentitybyscheme)하려는 경우에 유용 합니다. `AuthenticationScheme`를 [ Cookie authenticationdefaults로 설정 합니다. authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) 은 Cookie 스키마에 대해 "s" 값을 제공 합니다. 체계를 구별 하는 모든 문자열 값을 제공할 수 있습니다.

앱의 인증 체계가 앱의 인증 체계와 다릅니다 cookie . cookie인증 체계가에 제공 되지 않는 경우 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s")를 사용 합니다.

인증 cookie 의 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 속성은 기본적으로로 설정 됩니다 `true` . cookie사이트 방문자가 데이터 수집에 동의한 하지 않은 경우에는 인증을 사용할 수 있습니다. 자세한 내용은 <xref:security/gdpr#essential-cookies>를 참조하세요.

`Startup.Configure`메서드에서 메서드를 호출 하 여 `UseAuthentication` 속성을 설정 하는 인증 미들웨어를 호출 합니다 `HttpContext.User` . 또는를 `UseAuthentication` 호출 하기 전에 메서드를 호출 합니다 `UseMvcWithDefaultRoute` `UseMvc` .

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>클래스는 인증 공급자 옵션을 구성 하는 데 사용 됩니다.

`CookieAuthenticationOptions`메서드의 인증에 대 한 서비스 구성에서 설정 합니다 `Startup.ConfigureServices` .

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Cookie 정책 미들웨어

[ Cookie 정책 미들웨어](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) cookie 를 통해 정책 기능을 사용할 수 있습니다. 응용 프로그램 처리 파이프라인에 미들웨어를 추가 하는 것은 순서를 구분 하기 때문에 &mdash; 파이프라인에 등록 된 다운스트림 구성 요소에만 영향을 줍니다.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>정책 미들웨어에 제공 된를 사용 Cookie 하 여 cookie cookie cookie 가 추가 되거나 삭제 될 때 처리 및 처리 처리기에 대 한 전역 특성을 제어할 수 있습니다.

기본값은 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> `SameSiteMode.Lax` OAuth2 인증을 허용 하는 것입니다. 의 동일한 사이트 정책을 엄격 하 게 적용 하려면를 `SameSiteMode.Strict` 설정 `MinimumSameSitePolicy` 합니다. 이 설정은 OAuth2 및 다른 크로스-원본 인증 스키마를 중단 하지만 cookie 원본 간 요청 처리에 의존 하지 않는 다른 유형의 앱에 대 한 보안 수준을 강화 합니다.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Cookie의 정책 미들웨어 설정은 `MinimumSameSitePolicy` `Cookie.SameSite` `CookieAuthenticationOptions` 아래 행렬에 따라 설정의 설정에 영향을 줄 수 있습니다.

| MinimumSameSitePolicy | Cookie. SameSite | 결과 Cookie . SameSite 설정 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>인증 만들기 cookie

cookie보유 사용자 정보를 만들려면를 구성 <xref:System.Security.Claims.ClaimsPrincipal> 합니다. 사용자 정보는 serialize 되 고에 저장 됩니다 cookie . 

필요한를 사용 하 여를 만들고를 <xref:System.Security.Claims.ClaimsIdentity> <xref:System.Security.Claims.Claim> 호출 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 하 여 사용자를 로그인 합니다.

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` 암호화 된를 만들어 cookie 현재 응답에 추가 합니다. `AuthenticationScheme`을 지정 하지 않으면 기본 체계가 사용 됩니다.

ASP.NET Core의 [데이터 보호](xref:security/data-protection/using-data-protection) 시스템이 암호화에 사용 됩니다. 여러 컴퓨터에서 호스트 되는 앱의 경우, 앱 간에 부하를 분산 하거나 웹 팜을 사용 하 여 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 하 여 동일한 키 링 및 앱 식별자를 사용 하도록 구성 합니다.

## <a name="sign-out"></a>로그아웃

현재 사용자를 로그 아웃 하 고 삭제 하려면 cookie 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> .

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

`CookieAuthenticationDefaults.AuthenticationScheme`(또는 " Cookie s")가 구성표 (예: "Contoso")로 사용 되지 않는 경우 Cookie 인증 공급자를 구성할 때 사용 되는 체계를 제공 합니다. 그렇지 않으면 기본 체계가 사용 됩니다.

## <a name="react-to-back-end-changes"></a>백 엔드 변경 내용에 대응

가 cookie 만들어지면는 cookie id의 단일 원본입니다. 백 엔드 시스템에서 사용자 계정을 사용할 수 없는 경우:

* 앱의 cookie 인증 시스템은 인증에 따라 요청을 계속 처리 합니다 cookie .
* 인증이 유효한 경우 사용자는 앱에 로그인 상태를 유지 합니다 cookie .

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>이벤트를 사용 하 여 id의 유효성 검사를 가로채 고 재정의할 수 있습니다 cookie . 모든 요청에서의 유효성을 검사 하면 cookie 해지 된 사용자가 앱에 액세스 하는 위험을 완화할 수 있습니다.

유효성 검사에 대 한 한 가지 방법은 cookie 사용자 데이터베이스가 변경 되는 시기를 추적 하는 것입니다. 사용자가 발급 된 이후에 데이터베이스가 변경 되지 않은 경우에 cookie 도 해당 사용자를 다시 인증할 필요가 없습니다 cookie . 샘플 앱에서 데이터베이스는에서 구현 되 `IUserRepository` 고 값을 저장 합니다 `LastChanged` . 데이터베이스에서 사용자를 업데이트 하는 경우 값은 `LastChanged` 현재 시간으로 설정 됩니다.

cookie값에 따라 데이터베이스가 변경 될 때를 무효화 하려면 데이터베이스의 `LastChanged` 현재 값이 포함 된 클레임을 사용 하 여를 만듭니다 cookie `LastChanged` `LastChanged` .

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

이벤트에 대 한 재정의를 구현 하려면 `ValidatePrincipal` 에서 파생 되는 클래스에 다음 시그니처를 사용 하 여 메서드를 작성 합니다 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> .

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

다음은의 구현 예제입니다 `CookieAuthenticationEvents` .

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

메서드에서 서비스를 등록 하는 동안 events 인스턴스를 등록 cookie `Startup.ConfigureServices` 합니다. 클래스에 대해 범위가 지정 된 [서비스 등록](xref:fundamentals/dependency-injection#service-lifetimes) 을 제공 합니다 `CustomCookieAuthenticationEvents` .

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

사용자의 이름이 &mdash; 보안에 영향을 주지 않는 결정을 업데이트 하는 상황을 고려 합니다. 사용자 보안 주체를 destructively 업데이트 하려면를 호출 하 `context.ReplacePrincipal` 고 `context.ShouldRenew` 속성을로 설정 `true` 합니다.

> [!WARNING]
> 여기에 설명 된 방법은 모든 요청에서 트리거됩니다. 모든 cookie 요청에서 모든 사용자에 대 한 인증의 유효성을 검사 하면 앱에 대 한 성능 저하가 발생할 수 있습니다.

## <a name="persistent-cookies"></a>영구 cookie s

가 cookie 브라우저 세션에서 지속 되도록 할 수 있습니다. 이 지 속성은 로그인 또는 유사한 메커니즘에서 "사용자 이름" 확인란을 사용 하 여 명시적인 사용자 동의로만 설정 해야 합니다. 

다음 코드 조각에서는 id를 만들고 cookie 브라우저 클로저를 통해 해당 하는 해당 하는를 만듭니다. 이전에 구성 된 슬라이딩 만료 설정은 모두 적용 됩니다. 브라우저를 cookie 닫을 때가 만료 되 면 브라우저가 cookie 다시 시작 되 면를 지웁니다.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>에서로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> .

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>절대 cookie 만료

절대 만료 시간은를 사용 하 여 설정할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> . 영구을 만들려면 cookie `IsPersistent` 도 설정 해야 합니다. 그렇지 않은 경우는 cookie 세션 기반 수명으로 생성 되며 보유 한 인증 티켓 전이나 후에 만료 될 수 있습니다. `ExpiresUtc`가 설정 되 면 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> 설정 된 경우의 옵션 값을 재정의 합니다 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> .

다음 코드 조각에서는 id를 만들고 해당 id를 cookie 20 분 동안 지속 합니다. 이렇게 하면 이전에 구성 된 슬라이딩 만료 설정이 무시 됩니다.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [정책 기반 역할 검사](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
