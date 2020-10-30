---
title: ASP.NET Core에서 multi-factor authentication
author: damienbod
description: ASP.NET Core 앱에서 MFA (multi-factor authentication)를 설정 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
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
uid: security/authentication/mfa
ms.openlocfilehash: 873f7d113df84c931ad7fbf2c72aa292e4e87c48
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060393"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a>ASP.NET Core에서 multi-factor authentication

[Damien Bowden](https://github.com/damienbod)

[샘플 코드 보기 또는 다운로드 (damienbod/AspNetCoreHybridFlowWithApi GitHub 리포지토리)](https://github.com/damienbod/AspNetCoreHybridFlowWithApi)

MFA (multi-factor authentication)는 추가 형태의 식별을 위해 로그인 이벤트 중에 사용자가 요청 되는 프로세스입니다. 이 메시지는 휴대폰에서 코드를 입력 하거나, FIDO2 키를 사용 하거나, 지문 검색을 제공 하는 것일 수 있습니다. 두 번째 인증 형식이 필요한 경우 보안이 강화 됩니다. 공격자가 추가 요소를 쉽게 얻거나 중복 하는 것은 아닙니다.

이 문서에서는 다음 영역에 대해 설명 합니다.

* MFA 정의 및 권장 되는 MFA 흐름
* 를 사용 하 여 관리 페이지에 대 한 MFA 구성 ASP.NET Core Identity
* Openid connect Connect 서버에 MFA 로그인 요구 사항 보내기
* 강제로 ASP.NET Core Openid connect Connect 클라이언트에 MFA를 요구 합니다.

## <a name="mfa-2fa"></a>MFA, 2FA

MFA를 사용 하려면 사용자가 알고 있는 것과 같은 id에 대 한 둘 이상의 증명 유형이 필요 하거나 사용자가 인증을 받을 수 있도록 생체 인식 유효성 검사가 필요 합니다.

2 단계 인증 (2FA)은 MFA의 하위 집합과 유사 하지만 MFA가 id를 증명 하는 두 개 이상의 요인을 요구할 수 있다는 차이점이 있습니다.

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a>MFA TOTP (시간 기반 일회용 암호 알고리즘)

TOTP를 사용 하는 MFA는를 사용 하 여 지원 되는 구현입니다 ASP.NET Core Identity . 다음을 포함 하 여 모든 규격 인증자 앱과 함께 사용할 수 있습니다.

* Microsoft Authenticator 앱
* Google Authenticator 앱

구현 세부 정보는 다음 링크를 참조 하세요.

[ASP.NET Core에서 TOTP authenticator 앱에 대 한 QR 코드 생성 사용](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a>MFA FIDO2 또는 암호 없는

FIDO2은 현재 다음과 같습니다.

* MFA를 가장 안전 하 게 달성 하는 방법입니다.
* 피싱 공격 으로부터 보호 하는 유일한 MFA 흐름입니다.

현재 ASP.NET Core는 FIDO2을 직접 지원 하지 않습니다. FIDO2는 MFA 또는 암호 없는 흐름에 사용할 수 있습니다.

Azure Active Directory는 FIDO2 및 암호 없는 흐름에 대 한 지원을 제공 합니다. 자세한 내용은 [Azure Active Directory에 대 한 암호 없는 인증 옵션](/azure/active-directory/authentication/concept-authentication-passwordless)을 참조 하세요.

### <a name="mfa-sms"></a>MFA SMS

SMS를 사용 하는 MFA는 암호 인증 (단일 요소)과 비교 하 여 보안 대규모 높아집니다. 그러나 SMS를 두 번째 요소로 사용 하는 것은 더 이상 권장 되지 않습니다. 이 형식의 구현에 대해 알려진 공격 벡터가 너무 많습니다.

[NIST 지침](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a>를 사용 하 여 관리 페이지에 대 한 MFA 구성 ASP.NET Core Identity

사용자는 MFA를 사용 하 여 앱 내의 중요 한 페이지에 액세스할 수 있습니다 ASP.NET Core Identity . 이는 다양 한 id에 대해 서로 다른 수준의 액세스 권한이 있는 앱에 유용할 수 있습니다. 예를 들어 사용자가 암호 로그인을 사용 하 여 프로필 데이터를 볼 수 있지만 관리자가 MFA를 사용 하 여 관리 페이지에 액세스 해야 합니다.

### <a name="extend-the-login-with-an-mfa-claim"></a>MFA 클레임을 사용 하 여 로그인 확장

데모 코드는 및 페이지와 ASP.NET Core를 사용 하 여 설정 Identity Razor 합니다. `AddIdentity`메서드는 하나 대신 사용 `AddDefaultIdentity` 되므로 `IUserClaimsPrincipalFactory` 성공적인 로그인 후에는 구현을 사용 하 여 클레임을 id에 추가할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

`AdditionalUserClaimsPrincipalFactory`클래스는 로그인에 `amr` 성공한 후에만 사용자 클레임에 클레임을 추가 합니다. 클레임의 값을 데이터베이스에서 읽습니다. Id가 MFA로 로그인 한 경우에는 사용자가 더 높은 수준의 보호 된 보기에만 액세스 해야 하므로 클레임은 여기에 추가 됩니다. 클레임을 사용 하는 대신 데이터베이스에서 직접 데이터베이스 뷰를 읽는 경우 MFA를 활성화 한 후 MFA를 사용 하지 않고 보기에 직접 액세스할 수 있습니다.

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

Identity클래스에서 서비스 설정이 변경 되었으므로의 `Startup` 레이아웃을 Identity 업데이트 해야 합니다. 페이지를 Identity 앱으로 스 캐 폴드. *Identity /Account/Manage/_Layout. cshtml* 파일에 레이아웃을 정의 합니다.

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

또한 페이지에서 모든 관리 페이지에 대 한 레이아웃을 할당 합니다 Identity .

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a>관리 페이지에서 MFA 요구 사항의 유효성을 검사 합니다.

관리 Razor 페이지에서는 사용자가 MFA를 사용 하 여 로그인 했는지 확인 합니다. `OnGet`메서드에서 id는 사용자 클레임에 액세스 하는 데 사용 됩니다. `amr`값에 대 한 클레임을 확인 합니다 `mfa` . Id가이 클레임을 누락 하거나 인 경우 `false` 페이지는 MFA 사용 페이지로 리디렉션됩니다. 사용자가 이미 로그인 했지만 MFA를 사용 하지 않았기 때문에이를 수행할 수 있습니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a>사용자 로그인 정보를 설정/해제 하는 UI 논리

시작 시 권한 부여 정책이 추가 되었습니다. 정책에는 `amr` 값이 포함 된 클레임이 필요 `mfa` 합니다.

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

그러면 보기에서이 정책을 사용 하 여 `_Layout` 경고와 함께 **관리** 메뉴를 표시 하거나 숨길 수 있습니다.

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

Id가 MFA를 사용 하 여 로그인 한 경우에는 도구 설명 경고 없이 **Admin** 메뉴가 표시 됩니다. 사용자가 MFA 없이 로그인 하면 **관리자 (사용 안 함)** 메뉴가 사용자에 게 알리는 도구 설명 (경고 설명)과 함께 표시 됩니다.

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

사용자가 MFA 없이 로그인 하는 경우 경고가 표시 됩니다.

![관리자 MFA 인증](mfa/_static/identitystandalonemfa_01.png)

**관리자** 링크를 클릭 하면 사용자가 MFA 사용 보기로 리디렉션됩니다.

![관리자가 MFA 인증 활성화](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a>Openid connect Connect 서버에 MFA 로그인 요구 사항 보내기 

`acr_values`매개 변수를 사용 하 여 `mfa` 인증 요청에서 클라이언트의 필수 값을 서버에 전달할 수 있습니다.

> [!NOTE]
> `acr_values`이 작업을 수행 하려면 Openid connect Connect 서버에서 매개 변수를 처리 해야 합니다.

### <a name="openid-connect-aspnet-core-client"></a>Openid connect Connect ASP.NET Core 클라이언트

ASP.NET Core Razor Pages Openid connect connect 클라이언트 앱은 메서드를 사용 하 여 `AddOpenIdConnect` openid connect connect 서버에 로그인 합니다. `acr_values`매개 변수는 값으로 설정 되 `mfa` 고 인증 요청과 함께 전송 됩니다. 는 `OpenIdConnectEvents` 이를 추가 하는 데 사용 됩니다.

권장 `acr_values` 매개 변수 값은 [인증 방법 참조 값](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)을 참조 하세요.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a>예 Openid connect Identity 서버 4 서버 연결 ASP.NET Core Identity

MVC 뷰를 사용 하 여 구현 되는 Openid connect Connect 서버에서 ASP.NET Core Identity *ErrorEnable2FA* 라는 새 보기가 만들어집니다. 보기:

* Identity가 MFA를 필요로 하는 앱에서 제공 되지만 사용자가에서이를 활성화 하지 않은 경우를 표시 Identity 합니다.
* 사용자에 게 알리고이를 활성화 하는 링크를 추가 합니다.

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

`Login`메서드에서 `IIdentityServerInteractionService` 인터페이스 구현은 `_interaction` openid connect Connect 요청 매개 변수에 액세스 하는 데 사용 됩니다. `acr_values`매개 변수는 속성을 사용 하 여 액세스 됩니다 `AcrValues` . 클라이언트에서 set를 사용 하 여이 `mfa` 를 보내면이를 확인할 수 있습니다.

MFA가 필요 하 고의 사용자에 게 ASP.NET Core Identity mfa를 사용 하도록 설정한 경우 로그인은 계속 됩니다. 사용자가 MFA를 사용 하도록 설정 하지 않은 경우 사용자는 사용자 지정 보기 *ErrorEnable2FA* 리디렉션됩니다. 그런 다음 ASP.NET Core Identity 에서 사용자에 게 서명 합니다.

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

`ExternalLoginCallback`메서드는 로컬 로그인과 유사 하 게 작동 합니다 Identity . `AcrValues`속성은 값을 확인 `mfa` 합니다. `mfa`이 값이 있는 경우 로그인이 완료 되기 전에 MFA가 강제로 수행 됩니다 (예: 뷰로 리디렉션 됨 `ErrorEnable2FA` ).

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

사용자가 이미 로그인 한 경우 클라이언트 앱은 다음과 같습니다.

* 여전히 클레임의 유효성을 검사 `amr` 합니다.
* 보기에 대 한 링크를 사용 하 여 MFA를 설정할 수 있습니다 ASP.NET Core Identity .

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a>강제로 ASP.NET Core Openid connect Connect 클라이언트에 MFA를 요구 합니다.

이 예제에서는 Razor Openid connect Connect를 사용 하 여 로그인 하는 ASP.NET Core 페이지 앱이 사용자에 게 MFA를 사용 하 여 인증 되도록 요구할 수 있는 방법을 보여 줍니다.

MFA 요구 사항의 유효성을 검사 하기 위해 `IAuthorizationRequirement` 요구 사항이 생성 됩니다. MFA를 요구 하는 정책을 사용 하 여 페이지에 추가 됩니다.

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

`AuthorizationHandler`클레임을 사용 하 고 값을 확인 하는이 구현 됩니다 `amr` `mfa` . 는 `amr` `id_token` 성공적인 인증의에서 반환 되며 [인증 방법 참조 값](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) 사양에 정의 된 대로 다양 한 값을 가질 수 있습니다.

반환 되는 값은 id가 인증 되는 방법 및 Openid connect Connect 서버 구현에 따라 달라 집니다.

는 `AuthorizationHandler` `RequireMfa` 요구 사항을 사용 하 고 클레임의 유효성을 검사 `amr` 합니다. Openid connect Connect 서버는 Server4를 사용 하 여 구현할 수 있습니다 Identity ASP.NET Core Identity . 사용자가 TOTP를 사용 하 여 로그인 하는 경우에는 `amr` MFA 값을 사용 하 여 클레임이 반환 됩니다. 다른 Openid connect Connect 서버 구현 또는 다른 MFA 유형을 사용 하는 경우 클레임은 `amr` 다른 값을 가질 수 있습니다. 또한이를 허용 하려면 코드를 확장 해야 합니다.

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

`Startup.ConfigureServices`메서드에서 `AddOpenIdConnect` 메서드는 기본 챌린지 체계로 사용 됩니다. 클레임을 확인 하는 데 사용 되는 권한 부여 처리기는 `amr` 제어 컨테이너의 반전에 추가 됩니다. 그런 다음 요구 사항을 추가 하는 정책이 생성 됩니다 `RequireMfa` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

이 정책은 필요에 따라 페이지에서 사용 됩니다 Razor . 정책은 전체 앱에 대해서도 전역적으로 추가할 수 있습니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

사용자가 MFA 없이 인증 하는 경우 `amr` 클레임에 값이 있을 수 있습니다 `pwd` . 이 요청에는 페이지에 대 한 액세스 권한이 부여 되지 않습니다. 기본값을 사용 하 여 사용자가 *계정/s i d/거부* 페이지로 리디렉션됩니다. 이 동작을 변경 하거나 사용자 고유의 사용자 지정 논리를 구현할 수 있습니다. 이 예제에서는 유효한 사용자가 계정에 대해 MFA를 설정할 수 있도록 링크가 추가 됩니다.

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

이제 MFA로 인증 하는 사용자만 페이지 또는 웹 사이트에 액세스할 수 있습니다. 다른 MFA 유형을 사용 하거나 2FA가 양호 하면 `amr` 클레임은 다른 값을 가지 며 올바르게 처리 해야 합니다. 다른 Openid connect Connect 서버도이 클레임에 대해 다른 값을 반환 하며 [인증 방법 참조 값](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) 사양을 따르지 않을 수 있습니다.

MFA 없이 로그인 하는 경우 (예: 암호 사용):

* 의 `amr` 값은 `pwd` 다음과 같습니다.

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* 액세스가 거부 되었습니다.

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

또는 다음을 사용 하 여 OTP를 사용 하 여 로그인 합니다 Identity .

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a>추가 리소스

* [ASP.NET Core에서 TOTP authenticator 앱에 대 한 QR 코드 생성 사용](xref:security/authentication/identity-enable-qrcodes)
* [Azure Active Directory에 대 한 암호 없는 인증 옵션](/azure/active-directory/authentication/concept-authentication-passwordless)
* [.NET을 사용 하 여 FIDO2/WebAuthn 증명 및 어설션에 대 한 FIDO2 .NET 라이브러리](https://github.com/abergs/fido2-net-lib)
* [WebAuthn 놀라운](https://github.com/herrjemand/awesome-webauthn)
