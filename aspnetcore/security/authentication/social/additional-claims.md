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
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="6c0c1-103">ASP.NET Core의 외부 공급자에서 추가 클레임 및 토큰 유지</span><span class="sxs-lookup"><span data-stu-id="6c0c1-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6c0c1-104">ASP.NET Core 앱은 Facebook, Google, Microsoft, Twitter 등의 외부 인증 공급자에서 추가 클레임 및 토큰을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="6c0c1-105">각 공급자는 해당 플랫폼의 사용자에 대 한 다양 한 정보를 표시 하지만 사용자 데이터를 추가 클레임으로 수신 및 변환 하는 패턴은 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="6c0c1-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6c0c1-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6c0c1-107">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="6c0c1-107">Prerequisites</span></span>

<span data-ttu-id="6c0c1-108">앱에서 지원할 외부 인증 공급자를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="6c0c1-109">각 공급자에 대해 앱을 등록 하 고 클라이언트 ID 및 클라이언트 암호를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="6c0c1-110">자세한 내용은 <xref:security/authentication/social/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="6c0c1-111">샘플 앱은 [Google 인증 공급자](xref:security/authentication/google-logins)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="6c0c1-112">클라이언트 ID 및 클라이언트 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-112">Set the client ID and client secret</span></span>

<span data-ttu-id="6c0c1-113">OAuth 인증 공급자는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 앱과의 트러스트 관계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="6c0c1-114">앱이 공급자에 등록 되 면 외부 인증 공급자가 앱에 대 한 클라이언트 ID 및 클라이언트 암호 값을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="6c0c1-115">앱에서 사용 하는 각 외부 공급자는 공급자의 클라이언트 ID 및 클라이언트 암호와 독립적으로 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="6c0c1-116">자세한 내용은 시나리오에 적용 되는 외부 인증 공급자 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="6c0c1-117">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="6c0c1-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="6c0c1-118">Google 인증</span><span class="sxs-lookup"><span data-stu-id="6c0c1-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="6c0c1-119">Microsoft 인증</span><span class="sxs-lookup"><span data-stu-id="6c0c1-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="6c0c1-120">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="6c0c1-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="6c0c1-121">기타 인증 공급자</span><span class="sxs-lookup"><span data-stu-id="6c0c1-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="6c0c1-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="6c0c1-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="6c0c1-123">샘플 앱은 Google에서 제공 하는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 Google 인증 공급자를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="6c0c1-124">인증 범위 설정</span><span class="sxs-lookup"><span data-stu-id="6c0c1-124">Establish the authentication scope</span></span>

<span data-ttu-id="6c0c1-125">을 지정 하 여 공급자에서 검색할 사용 권한 목록을 지정 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="6c0c1-126">일반적인 외부 공급자에 대 한 인증 범위는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="6c0c1-127">공급자</span><span class="sxs-lookup"><span data-stu-id="6c0c1-127">Provider</span></span>  | <span data-ttu-id="6c0c1-128">Scope</span><span class="sxs-lookup"><span data-stu-id="6c0c1-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="6c0c1-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="6c0c1-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="6c0c1-130">Google</span><span class="sxs-lookup"><span data-stu-id="6c0c1-130">Google</span></span>    | <span data-ttu-id="6c0c1-131">`profile`, `email`, `openid`</span><span class="sxs-lookup"><span data-stu-id="6c0c1-131">`profile`, `email`, `openid`</span></span>                                     |
| <span data-ttu-id="6c0c1-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="6c0c1-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="6c0c1-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="6c0c1-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="6c0c1-134">샘플 앱에서 Google의 `profile` , `email` 및 범위는에서 `openid` 가 호출 될 때 프레임 워크에서 자동으로 추가 됩니다 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-134">In the sample app, Google's `profile`, `email`, and `openid` scopes are automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="6c0c1-135">앱에 추가 범위가 필요한 경우 옵션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="6c0c1-136">다음 예제에서는 Google `https://www.googleapis.com/auth/user.birthday.read` scope를 추가 하 여 사용자의 생일을 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="6c0c1-137">사용자 데이터 키 매핑 및 클레임 만들기</span><span class="sxs-lookup"><span data-stu-id="6c0c1-137">Map user data keys and create claims</span></span>

<span data-ttu-id="6c0c1-138">공급자의 옵션에서 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 로그인 시 읽을 앱 id에 대 한 외부 공급자 JSON 사용자 데이터의 각 키/하위 키에 대해 또는를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="6c0c1-139">클레임 유형에 대 한 자세한 내용은을 참조 하십시오 <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="6c0c1-140">샘플 앱은 `urn:google:locale` `urn:google:picture` `locale` `picture` Google 사용자 데이터의 및 키에서 로캘 () 및 그림 () 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="6c0c1-141">에서 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ()는 `ApplicationUser` 를 사용 하 여 앱에 로그인 됩니다 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="6c0c1-142">로그인 프로세스 중에는 <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 에서 사용할 수 있는 사용자 데이터에 대 한 클레임을 저장할 수 있습니다 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="6c0c1-143">샘플 앱에서 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*)는 `urn:google:locale` 에 대 한 클레임을 포함 하 여 로그인에 대 한 로캘 () 및 그림 ( `urn:google:picture` ) 클레임을 `ApplicationUser` 설정 합니다. <xref:System.Security.Claims.ClaimTypes.GivenName></span><span class="sxs-lookup"><span data-stu-id="6c0c1-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="6c0c1-144">기본적으로 사용자의 클레임은 인증에 저장 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="6c0c1-145">인증이 cookie 너무 크면 다음 이유로 인해 앱이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="6c0c1-146">브라우저가 cookie 헤더가 너무 긴 것을 감지 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="6c0c1-147">요청의 전체 크기가 너무 깁니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="6c0c1-148">사용자 요청을 처리 하기 위해 많은 양의 사용자 데이터가 필요한 경우:</span><span class="sxs-lookup"><span data-stu-id="6c0c1-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="6c0c1-149">요청 처리에 대 한 사용자 클레임 수와 크기를 앱에 필요한 것 으로만 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="6c0c1-150">인증 미들웨어의 사용자 지정를 사용 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> Cookie 하 여 요청에 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> id를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="6c0c1-151">클라이언트에 작은 세션 식별자 키를 보내는 경우에만 서버에서 많은 양의 id 정보를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="6c0c1-152">액세스 토큰 저장</span><span class="sxs-lookup"><span data-stu-id="6c0c1-152">Save the access token</span></span>

<span data-ttu-id="6c0c1-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 인증에 성공한 후에 액세스 및 새로 고침 토큰을에 저장 해야 하는지 여부를 정의 합니다 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="6c0c1-154">`SaveTokens` 는 `false` 최종 인증의 크기를 줄이기 위해 기본적으로로 설정 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="6c0c1-155">샘플 앱은의 값 `SaveTokens` 을의로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="6c0c1-156">`OnPostConfirmationAsync`을 실행하는 경우 `ApplicationUser`의 `AuthenticationProperties` 외부 공급자에 액세스 토큰 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*))을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="6c0c1-157">샘플 앱은 `OnPostConfirmationAsync` `OnGetCallbackAsync` *계정/d* s o s t o s t o s o s.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

> [!NOTE]
> <span data-ttu-id="6c0c1-158">응용 프로그램의 구성 요소에 토큰을 전달 하는 방법에 대 한 자세한 내용은 Razor Blazor Server 을 참조 하십시오 <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-158">For information on passing tokens to the Razor components of a Blazor Server app, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="6c0c1-159">추가 사용자 지정 토큰을 추가 하는 방법</span><span class="sxs-lookup"><span data-stu-id="6c0c1-159">How to add additional custom tokens</span></span>

<span data-ttu-id="6c0c1-160">의 일부로 저장 된 사용자 지정 토큰을 추가 하는 방법을 보여 주기 위해 `SaveTokens` 샘플 앱은 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> AuthenticationToken.Name의 현재와 함께를 추가 합니다 <xref:System.DateTime> [](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-160">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="6c0c1-161">클레임 만들기 및 추가</span><span class="sxs-lookup"><span data-stu-id="6c0c1-161">Creating and adding claims</span></span>

<span data-ttu-id="6c0c1-162">프레임 워크는 클레임을 만들고 컬렉션에 추가 하기 위한 일반적인 동작 및 확장 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-162">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="6c0c1-163">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 및 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-163">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="6c0c1-164">사용자는 추상 메서드를 파생 시키고 구현 하 여 사용자 지정 작업을 정의할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-164">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="6c0c1-165">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-165">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="add-and-update-user-claims"></a><span data-ttu-id="6c0c1-166">사용자 클레임 추가 및 업데이트</span><span class="sxs-lookup"><span data-stu-id="6c0c1-166">Add and update user claims</span></span>

<span data-ttu-id="6c0c1-167">클레임은 로그인이 아닌 첫 번째 등록 시 외부 공급자에서 사용자 데이터베이스로 복사 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-167">Claims are copied from external providers to the user database on first registration, not on sign in.</span></span> <span data-ttu-id="6c0c1-168">사용자가 앱을 사용 하도록 등록 한 후 앱에서 추가 클레임을 사용 하도록 설정 하는 경우 사용자에 대해 [SignInManager](xref:Microsoft.AspNetCore.Identity.SignInManager%601) 를 호출 하 여 새 인증을 강제로 생성 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-168">If additional claims are enabled in an app after a user registers to use the app, call [SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on a user to force the generation of a new authentication cookie.</span></span>

<span data-ttu-id="6c0c1-169">테스트 사용자 계정을 사용 하 여 작업 하는 개발 환경에서는 사용자 계정을 삭제 하 고 다시 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-169">In the Development environment working with test user accounts, you can simply delete and recreate the user account.</span></span> <span data-ttu-id="6c0c1-170">프로덕션 시스템의 경우 앱에 추가 된 새 클레임을 사용자 계정으로 백필 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-170">For production systems, new claims added to the app can be backfilled into user accounts.</span></span> <span data-ttu-id="6c0c1-171">에서 응용 프로그램으로 [ `ExternalLogin` 페이지의 스 캐 폴딩](xref:security/authentication/scaffold-identity) 후 `Areas/Pages/Identity/Account/Manage` 파일의에 다음 코드를 추가 합니다 `ExternalLoginModel` `ExternalLogin.cshtml.cs` .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-171">After [scaffolding the `ExternalLogin` page](xref:security/authentication/scaffold-identity) into the app at `Areas/Pages/Identity/Account/Manage`, add the following code to the `ExternalLoginModel` in the `ExternalLogin.cshtml.cs` file.</span></span>

<span data-ttu-id="6c0c1-172">추가 된 클레임의 사전을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-172">Add a dictionary of added claims.</span></span> <span data-ttu-id="6c0c1-173">사전 키를 사용 하 여 클레임 유형을 보유 하 고 값을 사용 하 여 기본값을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-173">Use the dictionary keys to hold the claim types, and use the values to hold a default value.</span></span> <span data-ttu-id="6c0c1-174">클래스의 맨 위에 다음 줄을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-174">Add the following line to the top of the class.</span></span> <span data-ttu-id="6c0c1-175">다음 예에서는 일반 헤드 샷 이미지를 기본값으로 사용 하 여 사용자의 Google 그림에 대해 하나의 클레임이 추가 된 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-175">The following example assumes that one claim is added for the user's Google picture with a generic headshot image as the default value:</span></span>

```csharp
private readonly IReadOnlyDictionary<string, string> _claimsToSync = 
    new Dictionary<string, string>()
    {
        { "urn:google:picture", "https://localhost:5001/headshot.png" },
    };
```

<span data-ttu-id="6c0c1-176">메서드의 기본 코드를 `OnGetCallbackAsync` 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-176">Replace the default code of the `OnGetCallbackAsync` method with the following code.</span></span> <span data-ttu-id="6c0c1-177">이 코드는 클레임 사전을 반복 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-177">The code loops through the claims dictionary.</span></span> <span data-ttu-id="6c0c1-178">각 사용자에 대해 클레임이 추가 (백필) 되거나 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-178">Claims are added (backfilled) or updated for each user.</span></span> <span data-ttu-id="6c0c1-179">클레임이 추가 되거나 업데이트 되 면 사용자 로그인은를 사용 하 여 새로 고쳐지고 <xref:Microsoft.AspNetCore.Identity.SignInManager%601> 기존 인증 속성 ()이 유지 됩니다 `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-179">When claims are added or updated, the user sign-in is refreshed using the <xref:Microsoft.AspNetCore.Identity.SignInManager%601>, preserving the existing authentication properties (`AuthenticationProperties`).</span></span>

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

<span data-ttu-id="6c0c1-180">사용자가 로그인 하는 동안 클레임이 변경 되는 경우에도 유사한 방법을 사용할 수 있지만 백필 단계가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-180">A similar approach is taken when claims change while a user is signed in but a backfill step isn't required.</span></span> <span data-ttu-id="6c0c1-181">사용자의 클레임을 업데이트 하려면 사용자에 대해 다음을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-181">To update a user's claims, call the following on the user:</span></span>

* <span data-ttu-id="6c0c1-182">Id 데이터베이스에 저장 된 클레임에 대 한 사용자 [ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) 입니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-182">[UserManager.ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) on the user for claims stored in the identity database.</span></span>
* <span data-ttu-id="6c0c1-183">새 인증을 강제로 생성 하도록 사용자에 대 한 [RefreshSignInAsync를 SignInManager.](xref:Microsoft.AspNetCore.Identity.SignInManager%601) cookie</span><span class="sxs-lookup"><span data-stu-id="6c0c1-183">[SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on the user to force the generation of a new authentication cookie.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="6c0c1-184">클레임 작업 및 클레임 제거</span><span class="sxs-lookup"><span data-stu-id="6c0c1-184">Removal of claim actions and claims</span></span>

<span data-ttu-id="6c0c1-185">[Claimactioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 는 컬렉션에서 지정 된에 대 한 모든 클레임 작업을 제거 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType></span><span class="sxs-lookup"><span data-stu-id="6c0c1-185">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="6c0c1-186">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 은 id에서 지정 된의 클레임을 삭제 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType></span><span class="sxs-lookup"><span data-stu-id="6c0c1-186">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="6c0c1-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 는 기본적으로 [OIDC (Openid connect Connect)](/azure/active-directory/develop/v2-protocols-oidc) 에서 프로토콜 생성 클레임을 제거 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="6c0c1-188">샘플 앱 출력</span><span class="sxs-lookup"><span data-stu-id="6c0c1-188">Sample app output</span></span>

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

<span data-ttu-id="6c0c1-189">ASP.NET Core 앱은 Facebook, Google, Microsoft, Twitter 등의 외부 인증 공급자에서 추가 클레임 및 토큰을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-189">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="6c0c1-190">각 공급자는 해당 플랫폼의 사용자에 대 한 다양 한 정보를 표시 하지만 사용자 데이터를 추가 클레임으로 수신 및 변환 하는 패턴은 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-190">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="6c0c1-191">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6c0c1-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6c0c1-192">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="6c0c1-192">Prerequisites</span></span>

<span data-ttu-id="6c0c1-193">앱에서 지원할 외부 인증 공급자를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-193">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="6c0c1-194">각 공급자에 대해 앱을 등록 하 고 클라이언트 ID 및 클라이언트 암호를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-194">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="6c0c1-195">자세한 내용은 <xref:security/authentication/social/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-195">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="6c0c1-196">샘플 앱은 [Google 인증 공급자](xref:security/authentication/google-logins)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-196">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="6c0c1-197">클라이언트 ID 및 클라이언트 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-197">Set the client ID and client secret</span></span>

<span data-ttu-id="6c0c1-198">OAuth 인증 공급자는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 앱과의 트러스트 관계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-198">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="6c0c1-199">앱이 공급자에 등록 되 면 외부 인증 공급자가 앱에 대 한 클라이언트 ID 및 클라이언트 암호 값을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-199">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="6c0c1-200">앱에서 사용 하는 각 외부 공급자는 공급자의 클라이언트 ID 및 클라이언트 암호와 독립적으로 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-200">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="6c0c1-201">자세한 내용은 시나리오에 적용 되는 외부 인증 공급자 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-201">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="6c0c1-202">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="6c0c1-202">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="6c0c1-203">Google 인증</span><span class="sxs-lookup"><span data-stu-id="6c0c1-203">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="6c0c1-204">Microsoft 인증</span><span class="sxs-lookup"><span data-stu-id="6c0c1-204">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="6c0c1-205">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="6c0c1-205">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="6c0c1-206">기타 인증 공급자</span><span class="sxs-lookup"><span data-stu-id="6c0c1-206">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="6c0c1-207">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="6c0c1-207">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="6c0c1-208">샘플 앱은 Google에서 제공 하는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 Google 인증 공급자를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-208">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="6c0c1-209">인증 범위 설정</span><span class="sxs-lookup"><span data-stu-id="6c0c1-209">Establish the authentication scope</span></span>

<span data-ttu-id="6c0c1-210">을 지정 하 여 공급자에서 검색할 사용 권한 목록을 지정 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-210">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="6c0c1-211">일반적인 외부 공급자에 대 한 인증 범위는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-211">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="6c0c1-212">공급자</span><span class="sxs-lookup"><span data-stu-id="6c0c1-212">Provider</span></span>  | <span data-ttu-id="6c0c1-213">Scope</span><span class="sxs-lookup"><span data-stu-id="6c0c1-213">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="6c0c1-214">Facebook</span><span class="sxs-lookup"><span data-stu-id="6c0c1-214">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="6c0c1-215">Google</span><span class="sxs-lookup"><span data-stu-id="6c0c1-215">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="6c0c1-216">Microsoft</span><span class="sxs-lookup"><span data-stu-id="6c0c1-216">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="6c0c1-217">Twitter</span><span class="sxs-lookup"><span data-stu-id="6c0c1-217">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="6c0c1-218">샘플 앱에서 Google의 범위는 `userinfo.profile` 에서가 호출 될 때 프레임 워크에서 자동으로 추가 됩니다 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-218">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="6c0c1-219">앱에 추가 범위가 필요한 경우 옵션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-219">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="6c0c1-220">다음 예제에서는 `https://www.googleapis.com/auth/user.birthday.read` 사용자의 생일을 검색 하기 위해 Google scope를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-220">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="6c0c1-221">사용자 데이터 키 매핑 및 클레임 만들기</span><span class="sxs-lookup"><span data-stu-id="6c0c1-221">Map user data keys and create claims</span></span>

<span data-ttu-id="6c0c1-222">공급자의 옵션에서 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 로그인 시 읽을 앱 id에 대 한 외부 공급자 JSON 사용자 데이터의 각 키/하위 키에 대해 또는를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-222">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="6c0c1-223">클레임 유형에 대 한 자세한 내용은을 참조 하십시오 <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-223">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="6c0c1-224">샘플 앱은 `urn:google:locale` `urn:google:picture` `locale` `picture` Google 사용자 데이터의 및 키에서 로캘 () 및 그림 () 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-224">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="6c0c1-225">에서 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ()는 `ApplicationUser` 를 사용 하 여 앱에 로그인 됩니다 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-225">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="6c0c1-226">로그인 프로세스 중에는 <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 에서 사용할 수 있는 사용자 데이터에 대 한 클레임을 저장할 수 있습니다 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-226">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="6c0c1-227">샘플 앱에서 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*)는 `urn:google:locale` 에 대 한 클레임을 포함 하 여 로그인에 대 한 로캘 () 및 그림 ( `urn:google:picture` ) 클레임을 `ApplicationUser` 설정 합니다. <xref:System.Security.Claims.ClaimTypes.GivenName></span><span class="sxs-lookup"><span data-stu-id="6c0c1-227">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="6c0c1-228">기본적으로 사용자의 클레임은 인증에 저장 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-228">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="6c0c1-229">인증이 cookie 너무 크면 다음 이유로 인해 앱이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-229">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="6c0c1-230">브라우저가 cookie 헤더가 너무 긴 것을 감지 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-230">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="6c0c1-231">요청의 전체 크기가 너무 깁니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-231">The overall size of the request is too large.</span></span>

<span data-ttu-id="6c0c1-232">사용자 요청을 처리 하기 위해 많은 양의 사용자 데이터가 필요한 경우:</span><span class="sxs-lookup"><span data-stu-id="6c0c1-232">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="6c0c1-233">요청 처리에 대 한 사용자 클레임 수와 크기를 앱에 필요한 것 으로만 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-233">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="6c0c1-234">인증 미들웨어의 사용자 지정를 사용 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> Cookie 하 여 요청에 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> id를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-234">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="6c0c1-235">클라이언트에 작은 세션 식별자 키를 보내는 경우에만 서버에서 많은 양의 id 정보를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-235">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="6c0c1-236">액세스 토큰 저장</span><span class="sxs-lookup"><span data-stu-id="6c0c1-236">Save the access token</span></span>

<span data-ttu-id="6c0c1-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 인증에 성공한 후에 액세스 및 새로 고침 토큰을에 저장 해야 하는지 여부를 정의 합니다 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="6c0c1-238">`SaveTokens` 는 `false` 최종 인증의 크기를 줄이기 위해 기본적으로로 설정 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-238">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="6c0c1-239">샘플 앱은의 값 `SaveTokens` 을의로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-239">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="6c0c1-240">`OnPostConfirmationAsync`을 실행하는 경우 `ApplicationUser`의 `AuthenticationProperties` 외부 공급자에 액세스 토큰 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*))을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-240">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="6c0c1-241">샘플 앱은 `OnPostConfirmationAsync` `OnGetCallbackAsync` *계정/d* s o s t o s t o s o s.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-241">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="6c0c1-242">추가 사용자 지정 토큰을 추가 하는 방법</span><span class="sxs-lookup"><span data-stu-id="6c0c1-242">How to add additional custom tokens</span></span>

<span data-ttu-id="6c0c1-243">의 일부로 저장 된 사용자 지정 토큰을 추가 하는 방법을 보여 주기 위해 `SaveTokens` 샘플 앱은 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> AuthenticationToken.Name의 현재와 함께를 추가 합니다 <xref:System.DateTime> [](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-243">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="6c0c1-244">클레임 만들기 및 추가</span><span class="sxs-lookup"><span data-stu-id="6c0c1-244">Creating and adding claims</span></span>

<span data-ttu-id="6c0c1-245">프레임 워크는 클레임을 만들고 컬렉션에 추가 하기 위한 일반적인 동작 및 확장 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-245">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="6c0c1-246">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 및 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-246">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="6c0c1-247">사용자는 추상 메서드를 파생 시키고 구현 하 여 사용자 지정 작업을 정의할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="6c0c1-247">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="6c0c1-248">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-248">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="6c0c1-249">클레임 작업 및 클레임 제거</span><span class="sxs-lookup"><span data-stu-id="6c0c1-249">Removal of claim actions and claims</span></span>

<span data-ttu-id="6c0c1-250">[Claimactioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 는 컬렉션에서 지정 된에 대 한 모든 클레임 작업을 제거 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType></span><span class="sxs-lookup"><span data-stu-id="6c0c1-250">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="6c0c1-251">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 은 id에서 지정 된의 클레임을 삭제 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType></span><span class="sxs-lookup"><span data-stu-id="6c0c1-251">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="6c0c1-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 는 기본적으로 [OIDC (Openid connect Connect)](/azure/active-directory/develop/v2-protocols-oidc) 에서 프로토콜 생성 클레임을 제거 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="6c0c1-253">샘플 앱 출력</span><span class="sxs-lookup"><span data-stu-id="6c0c1-253">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="6c0c1-254">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6c0c1-254">Additional resources</span></span>

* <span data-ttu-id="6c0c1-255">[dotnet/AspNetCore 공학적 AspNetCore Alsample 앱](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): 연결 된 샘플 앱은 [Dotnet/GitHub 리포지토리의](https://github.com/dotnet/AspNetCore) `master` 엔지니어링 분기에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-255">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="6c0c1-256">분기에는 `master` ASP.NET Core의 다음 릴리스에 대해 활성 개발 중인 코드가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-256">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="6c0c1-257">ASP.NET Core의 릴리스 버전에 대 한 샘플 앱 버전을 보려면 **분기** 드롭다운 목록을 사용 하 여 릴리스 분기 (예:)를 선택 `release/{X.Y}` 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c0c1-257">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
