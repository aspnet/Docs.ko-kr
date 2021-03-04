---
title: ASP.NET Core의 외부 공급자에서 추가 클레임 및 토큰 유지
author: rick-anderson
description: 외부 공급자에서 추가 클레임 및 토큰을 설정 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2021
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 9c04ca466566e956b5e6dfec8131096c3995bc14
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110146"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>ASP.NET Core의 외부 공급자에서 추가 클레임 및 토큰 유지

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 앱은 Facebook, Google, Microsoft, Twitter 등의 외부 인증 공급자에서 추가 클레임 및 토큰을 설정할 수 있습니다. 각 공급자는 해당 플랫폼의 사용자에 대 한 다양 한 정보를 표시 하지만 사용자 데이터를 추가 클레임으로 수신 및 변환 하는 패턴은 동일 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>사전 요구 사항

앱에서 지원할 외부 인증 공급자를 결정 합니다. 각 공급자에 대해 앱을 등록 하 고 클라이언트 ID 및 클라이언트 암호를 가져옵니다. 자세한 내용은 <xref:security/authentication/social/index>를 참조하세요. 샘플 앱은 [Google 인증 공급자](xref:security/authentication/google-logins)를 사용 합니다.

## <a name="set-the-client-id-and-client-secret"></a>클라이언트 ID 및 클라이언트 암호를 설정 합니다.

OAuth 인증 공급자는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 앱과의 트러스트 관계를 설정 합니다. 앱이 공급자에 등록 되 면 외부 인증 공급자가 앱에 대 한 클라이언트 ID 및 클라이언트 암호 값을 만듭니다. 앱에서 사용 하는 각 외부 공급자는 공급자의 클라이언트 ID 및 클라이언트 암호와 독립적으로 구성 해야 합니다. 자세한 내용은 시나리오에 적용 되는 외부 인증 공급자 항목을 참조 하세요.

* [Facebook 인증](xref:security/authentication/facebook-logins)
* [Google 인증](xref:security/authentication/google-logins)
* [Microsoft 인증](xref:security/authentication/microsoft-logins)
* [Twitter 인증](xref:security/authentication/twitter-logins)
* [기타 인증 공급자](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

샘플 앱은 Google에서 제공 하는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 Google 인증 공급자를 구성 합니다.

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>인증 범위 설정

을 지정 하 여 공급자에서 검색할 사용 권한 목록을 지정 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 합니다. 일반적인 외부 공급자에 대 한 인증 범위는 다음 표에 나와 있습니다.

| 공급자  | Scope                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `profile`, `email`, `openid`                                     |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

샘플 앱에서 Google의 `profile` , `email` 및 범위는에서 `openid` 가 호출 될 때 프레임 워크에서 자동으로 추가 됩니다 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> . 앱에 추가 범위가 필요한 경우 옵션에 추가 합니다. 다음 예제에서는 Google `https://www.googleapis.com/auth/user.birthday.read` scope를 추가 하 여 사용자의 생일을 검색 합니다.

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>사용자 데이터 키 매핑 및 클레임 만들기

공급자의 옵션에서 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 로그인 시 읽을 앱 id에 대 한 외부 공급자 JSON 사용자 데이터의 각 키/하위 키에 대해 또는를 지정 합니다. 클레임 유형에 대 한 자세한 내용은을 참조 하십시오 <xref:System.Security.Claims.ClaimTypes> .

샘플 앱은 `urn:google:locale` `urn:google:picture` `locale` `picture` Google 사용자 데이터의 및 키에서 로캘 () 및 그림 () 클레임을 만듭니다.

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

에서 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ()는 `ApplicationUser` 를 사용 하 여 앱에 로그인 됩니다 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> . 로그인 프로세스 중에는 <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 에서 사용할 수 있는 사용자 데이터에 대 한 클레임을 저장할 수 있습니다 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .

샘플 앱에서 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*)는 `urn:google:locale` 에 대 한 클레임을 포함 하 여 로그인에 대 한 로캘 () 및 그림 ( `urn:google:picture` ) 클레임을 `ApplicationUser` 설정 합니다. <xref:System.Security.Claims.ClaimTypes.GivenName>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

기본적으로 사용자의 클레임은 인증에 저장 됩니다 cookie . 인증이 cookie 너무 크면 다음 이유로 인해 앱이 실패할 수 있습니다.

* 브라우저가 cookie 헤더가 너무 긴 것을 감지 합니다.
* 요청의 전체 크기가 너무 깁니다.

사용자 요청을 처리 하기 위해 많은 양의 사용자 데이터가 필요한 경우:

* 요청 처리에 대 한 사용자 클레임 수와 크기를 앱에 필요한 것 으로만 제한 합니다.
* 인증 미들웨어의 사용자 지정를 사용 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> Cookie 하 여 요청에 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> id를 저장 합니다. 클라이언트에 작은 세션 식별자 키를 보내는 경우에만 서버에서 많은 양의 id 정보를 유지 합니다.

## <a name="save-the-access-token"></a>액세스 토큰 저장

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 인증에 성공한 후에 액세스 및 새로 고침 토큰을에 저장 해야 하는지 여부를 정의 합니다 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> . `SaveTokens` 는 `false` 최종 인증의 크기를 줄이기 위해 기본적으로로 설정 됩니다 cookie .

샘플 앱은의 값 `SaveTokens` 을의로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> .

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

`OnPostConfirmationAsync`을 실행하는 경우 `ApplicationUser`의 `AuthenticationProperties` 외부 공급자에 액세스 토큰 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*))을 저장 합니다.

샘플 앱은 `OnPostConfirmationAsync` `OnGetCallbackAsync` *계정/d* s o s t o s t o s o s.

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

> [!NOTE]
> 응용 프로그램의 구성 요소에 토큰을 전달 하는 방법에 대 한 자세한 내용은 Razor Blazor Server 을 참조 하십시오 <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> .

## <a name="how-to-add-additional-custom-tokens"></a>추가 사용자 지정 토큰을 추가 하는 방법

의 일부로 저장 된 사용자 지정 토큰을 추가 하는 방법을 보여 주기 위해 `SaveTokens` 샘플 앱은 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> AuthenticationToken.Name의 현재와 함께를 추가 합니다 <xref:System.DateTime> [](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` .

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>클레임 만들기 및 추가

프레임 워크는 클레임을 만들고 컬렉션에 추가 하기 위한 일반적인 동작 및 확장 메서드를 제공 합니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 및 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>를 참조하세요.

사용자는 추상 메서드를 파생 시키고 구현 하 여 사용자 지정 작업을 정의할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .

자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>를 참조하세요.

## <a name="add-and-update-user-claims"></a>사용자 클레임 추가 및 업데이트

클레임은 로그인이 아닌 첫 번째 등록 시 외부 공급자에서 사용자 데이터베이스로 복사 됩니다. 사용자가 앱을 사용 하도록 등록 한 후 앱에서 추가 클레임을 사용 하도록 설정 하는 경우 사용자에 대해 [SignInManager](xref:Microsoft.AspNetCore.Identity.SignInManager%601) 를 호출 하 여 새 인증을 강제로 생성 합니다 cookie .

테스트 사용자 계정을 사용 하 여 작업 하는 개발 환경에서는 사용자 계정을 삭제 하 고 다시 만들 수 있습니다. 프로덕션 시스템의 경우 앱에 추가 된 새 클레임을 사용자 계정으로 백필 수 있습니다. 에서 응용 프로그램으로 [ `ExternalLogin` 페이지의 스 캐 폴딩](xref:security/authentication/scaffold-identity) 후 `Areas/Pages/Identity/Account/Manage` 파일의에 다음 코드를 추가 합니다 `ExternalLoginModel` `ExternalLogin.cshtml.cs` .

추가 된 클레임의 사전을 추가 합니다. 사전 키를 사용 하 여 클레임 유형을 보유 하 고 값을 사용 하 여 기본값을 유지 합니다. 클래스의 맨 위에 다음 줄을 추가 합니다. 다음 예에서는 일반 헤드 샷 이미지를 기본값으로 사용 하 여 사용자의 Google 그림에 대해 하나의 클레임이 추가 된 것으로 가정 합니다.

```csharp
private readonly IReadOnlyDictionary<string, string> _claimsToSync = 
    new Dictionary<string, string>()
    {
        { "urn:google:picture", "https://localhost:5001/headshot.png" },
    };
```

메서드의 기본 코드를 `OnGetCallbackAsync` 다음 코드로 바꿉니다. 이 코드는 클레임 사전을 반복 합니다. 각 사용자에 대해 클레임이 추가 (백필) 되거나 업데이트 됩니다. 클레임이 추가 되거나 업데이트 되 면 사용자 로그인은를 사용 하 여 새로 고쳐지고 <xref:Microsoft.AspNetCore.Identity.SignInManager%601> 기존 인증 속성 ()이 유지 됩니다 `AuthenticationProperties` .

```csharp
public async Task<IActionResult> OnGetCallbackAsync(
    string returnUrl = null, string remoteError = null)
{
    returnUrl = returnUrl ?? Url.Content("~/");

    if (remoteError != null)
    {
        ErrorMessage = $"Error from external provider: {remoteError}";

        return RedirectToPage("./Login", new {ReturnUrl = returnUrl });
    }

    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        ErrorMessage = "Error loading external login information.";
        return RedirectToPage("./Login", new { ReturnUrl = returnUrl });
    }

    // Sign in the user with this external login provider if the user already has a 
    // login.
    var result = await _signInManager.ExternalLoginSignInAsync(info.LoginProvider, 
        info.ProviderKey, isPersistent: false, bypassTwoFactor : true);

    if (result.Succeeded)
    {
        _logger.LogInformation("{Name} logged in with {LoginProvider} provider.", 
            info.Principal.Identity.Name, info.LoginProvider);

        if (_claimsToSync.Count > 0)
        {
            var user = await _userManager.FindByLoginAsync(info.LoginProvider, 
                info.ProviderKey);
            var userClaims = await _userManager.GetClaimsAsync(user);
            bool refreshSignIn = false;

            foreach (var addedClaim in _claimsToSync)
            {
                var userClaim = userClaims
                    .FirstOrDefault(c => c.Type == addedClaim.Key);

                if (info.Principal.HasClaim(c => c.Type == addedClaim.Key))
                {
                    var externalClaim = info.Principal.FindFirst(addedClaim.Key);

                    if (userClaim == null)
                    {
                        await _userManager.AddClaimAsync(user, 
                            new Claim(addedClaim.Key, externalClaim.Value));
                        refreshSignIn = true;
                    }
                    else if (userClaim.Value != externalClaim.Value)
                    {
                        await _userManager
                            .ReplaceClaimAsync(user, userClaim, externalClaim);
                        refreshSignIn = true;
                    }
                }
                else if (userClaim == null)
                {
                    // Fill with a default value
                    await _userManager.AddClaimAsync(user, new Claim(addedClaim.Key, 
                        addedClaim.Value));
                    refreshSignIn = true;
                }
            }

            if (refreshSignIn)
            {
                await _signInManager.RefreshSignInAsync(user);
            }
        }

        return LocalRedirect(returnUrl);
    }

    if (result.IsLockedOut)
    {
        return RedirectToPage("./Lockout");
    }
    else
    {
        // If the user does not have an account, then ask the user to create an 
        // account.
        ReturnUrl = returnUrl;
        ProviderDisplayName = info.ProviderDisplayName;

        if (info.Principal.HasClaim(c => c.Type == ClaimTypes.Email))
        {
            Input = new InputModel
            {
                Email = info.Principal.FindFirstValue(ClaimTypes.Email)
            };
        }

        return Page();
    }
}
```

사용자가 로그인 하는 동안 클레임이 변경 되는 경우에도 유사한 방법을 사용할 수 있지만 백필 단계가 필요 하지 않습니다. 사용자의 클레임을 업데이트 하려면 사용자에 대해 다음을 호출 합니다.

* Id 데이터베이스에 저장 된 클레임에 대 한 사용자 [ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) 입니다.
* 새 인증을 강제로 생성 하도록 사용자에 대 한 [RefreshSignInAsync를 SignInManager.](xref:Microsoft.AspNetCore.Identity.SignInManager%601) cookie

## <a name="removal-of-claim-actions-and-claims"></a>클레임 작업 및 클레임 제거

[Claimactioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 는 컬렉션에서 지정 된에 대 한 모든 클레임 작업을 제거 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> [Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 은 id에서 지정 된의 클레임을 삭제 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 는 기본적으로 [OIDC (Openid connect Connect)](/azure/active-directory/develop/v2-protocols-oidc) 에서 프로토콜 생성 클레임을 제거 하는 데 사용 됩니다.

## <a name="sample-app-output"></a>샘플 앱 출력

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 앱은 Facebook, Google, Microsoft, Twitter 등의 외부 인증 공급자에서 추가 클레임 및 토큰을 설정할 수 있습니다. 각 공급자는 해당 플랫폼의 사용자에 대 한 다양 한 정보를 표시 하지만 사용자 데이터를 추가 클레임으로 수신 및 변환 하는 패턴은 동일 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>사전 요구 사항

앱에서 지원할 외부 인증 공급자를 결정 합니다. 각 공급자에 대해 앱을 등록 하 고 클라이언트 ID 및 클라이언트 암호를 가져옵니다. 자세한 내용은 <xref:security/authentication/social/index>를 참조하세요. 샘플 앱은 [Google 인증 공급자](xref:security/authentication/google-logins)를 사용 합니다.

## <a name="set-the-client-id-and-client-secret"></a>클라이언트 ID 및 클라이언트 암호를 설정 합니다.

OAuth 인증 공급자는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 앱과의 트러스트 관계를 설정 합니다. 앱이 공급자에 등록 되 면 외부 인증 공급자가 앱에 대 한 클라이언트 ID 및 클라이언트 암호 값을 만듭니다. 앱에서 사용 하는 각 외부 공급자는 공급자의 클라이언트 ID 및 클라이언트 암호와 독립적으로 구성 해야 합니다. 자세한 내용은 시나리오에 적용 되는 외부 인증 공급자 항목을 참조 하세요.

* [Facebook 인증](xref:security/authentication/facebook-logins)
* [Google 인증](xref:security/authentication/google-logins)
* [Microsoft 인증](xref:security/authentication/microsoft-logins)
* [Twitter 인증](xref:security/authentication/twitter-logins)
* [기타 인증 공급자](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

샘플 앱은 Google에서 제공 하는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 Google 인증 공급자를 구성 합니다.

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>인증 범위 설정

을 지정 하 여 공급자에서 검색할 사용 권한 목록을 지정 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 합니다. 일반적인 외부 공급자에 대 한 인증 범위는 다음 표에 나와 있습니다.

| 공급자  | Scope                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

샘플 앱에서 Google의 범위는 `userinfo.profile` 에서가 호출 될 때 프레임 워크에서 자동으로 추가 됩니다 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> . 앱에 추가 범위가 필요한 경우 옵션에 추가 합니다. 다음 예제에서는 `https://www.googleapis.com/auth/user.birthday.read` 사용자의 생일을 검색 하기 위해 Google scope를 추가 합니다.

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>사용자 데이터 키 매핑 및 클레임 만들기

공급자의 옵션에서 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 로그인 시 읽을 앱 id에 대 한 외부 공급자 JSON 사용자 데이터의 각 키/하위 키에 대해 또는를 지정 합니다. 클레임 유형에 대 한 자세한 내용은을 참조 하십시오 <xref:System.Security.Claims.ClaimTypes> .

샘플 앱은 `urn:google:locale` `urn:google:picture` `locale` `picture` Google 사용자 데이터의 및 키에서 로캘 () 및 그림 () 클레임을 만듭니다.

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

에서 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ()는 `ApplicationUser` 를 사용 하 여 앱에 로그인 됩니다 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> . 로그인 프로세스 중에는 <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 에서 사용할 수 있는 사용자 데이터에 대 한 클레임을 저장할 수 있습니다 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .

샘플 앱에서 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*)는 `urn:google:locale` 에 대 한 클레임을 포함 하 여 로그인에 대 한 로캘 () 및 그림 ( `urn:google:picture` ) 클레임을 `ApplicationUser` 설정 합니다. <xref:System.Security.Claims.ClaimTypes.GivenName>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

기본적으로 사용자의 클레임은 인증에 저장 됩니다 cookie . 인증이 cookie 너무 크면 다음 이유로 인해 앱이 실패할 수 있습니다.

* 브라우저가 cookie 헤더가 너무 긴 것을 감지 합니다.
* 요청의 전체 크기가 너무 깁니다.

사용자 요청을 처리 하기 위해 많은 양의 사용자 데이터가 필요한 경우:

* 요청 처리에 대 한 사용자 클레임 수와 크기를 앱에 필요한 것 으로만 제한 합니다.
* 인증 미들웨어의 사용자 지정를 사용 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> Cookie 하 여 요청에 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> id를 저장 합니다. 클라이언트에 작은 세션 식별자 키를 보내는 경우에만 서버에서 많은 양의 id 정보를 유지 합니다.

## <a name="save-the-access-token"></a>액세스 토큰 저장

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 인증에 성공한 후에 액세스 및 새로 고침 토큰을에 저장 해야 하는지 여부를 정의 합니다 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> . `SaveTokens` 는 `false` 최종 인증의 크기를 줄이기 위해 기본적으로로 설정 됩니다 cookie .

샘플 앱은의 값 `SaveTokens` 을의로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> .

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

`OnPostConfirmationAsync`을 실행하는 경우 `ApplicationUser`의 `AuthenticationProperties` 외부 공급자에 액세스 토큰 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*))을 저장 합니다.

샘플 앱은 `OnPostConfirmationAsync` `OnGetCallbackAsync` *계정/d* s o s t o s t o s o s.

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>추가 사용자 지정 토큰을 추가 하는 방법

의 일부로 저장 된 사용자 지정 토큰을 추가 하는 방법을 보여 주기 위해 `SaveTokens` 샘플 앱은 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> AuthenticationToken.Name의 현재와 함께를 추가 합니다 <xref:System.DateTime> [](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` .

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>클레임 만들기 및 추가

프레임 워크는 클레임을 만들고 컬렉션에 추가 하기 위한 일반적인 동작 및 확장 메서드를 제공 합니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 및 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>를 참조하세요.

사용자는 추상 메서드를 파생 시키고 구현 하 여 사용자 지정 작업을 정의할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .

자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>를 참조하세요.

## <a name="removal-of-claim-actions-and-claims"></a>클레임 작업 및 클레임 제거

[Claimactioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 는 컬렉션에서 지정 된에 대 한 모든 클레임 작업을 제거 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> [Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 은 id에서 지정 된의 클레임을 삭제 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 는 기본적으로 [OIDC (Openid connect Connect)](/azure/active-directory/develop/v2-protocols-oidc) 에서 프로토콜 생성 클레임을 제거 하는 데 사용 됩니다.

## <a name="sample-app-output"></a>샘플 앱 출력

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* [dotnet/AspNetCore 공학적 AspNetCore Alsample 앱](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): 연결 된 샘플 앱은 [Dotnet/GitHub 리포지토리의](https://github.com/dotnet/AspNetCore) `master` 엔지니어링 분기에 있습니다. 분기에는 `master` ASP.NET Core의 다음 릴리스에 대해 활성 개발 중인 코드가 포함 됩니다. ASP.NET Core의 릴리스 버전에 대 한 샘플 앱 버전을 보려면 **분기** 드롭다운 목록을 사용 하 여 릴리스 분기 (예:)를 선택 `release/{X.Y}` 합니다.
