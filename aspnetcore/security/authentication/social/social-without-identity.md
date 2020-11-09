---
title: 'Facebook, Google 및 외부 공급자 인증 없이 ASP.NET Core Identity'
author: rick-anderson
description: '를 사용 하지 않고 Facebook, Google, Twitter 등의 계정 사용자 인증을 사용 하는 방법에 대 한 ASP.NET Core Identity 설명입니다.'
ms.author: riande
ms.date: 12/10/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cd7545a3ddaccedfa64ef5e9d5458c21c651257a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060289"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="83ade-103">다음을 사용 하지 않고 소셜 로그인 공급자 인증 사용 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="83ade-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="83ade-104">[Kirk Larkin](https://twitter.com/serpent5) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83ade-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83ade-105"><xref:security/authentication/social/index> 사용자가 외부 인증 공급자의 자격 증명으로 OAuth 2.0를 사용 하 여 로그인 할 수 있게 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="83ade-106">이 항목에서 설명 하는 접근 방식에는 인증 공급자로가 포함 되어 있습니다 ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="83ade-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="83ade-107">이 샘플에서는 **없이** 외부 인증 공급자를 사용 하는 방법을 보여 줍니다 ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="83ade-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="83ade-108">이 기능은의 모든 기능이 필요 하지는 ASP.NET Core Identity 않지만 여전히 신뢰할 수 있는 외부 인증 공급자와의 통합이 필요한 앱에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="83ade-109">이 샘플에서는 사용자를 인증 하는 데 [Google 인증](xref:security/authentication/google-logins) 을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="83ade-110">Google 인증을 사용 하면 로그인 프로세스를 관리 하는 복잡 한 여러 가지 복잡성이 Google으로 이동 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="83ade-111">다른 외부 인증 공급자와 통합 하려면 다음 항목을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="83ade-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="83ade-112">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="83ade-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="83ade-113">Microsoft 인증</span><span class="sxs-lookup"><span data-stu-id="83ade-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="83ade-114">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="83ade-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="83ade-115">기타 공급자</span><span class="sxs-lookup"><span data-stu-id="83ade-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="83ade-116">Configuration</span><span class="sxs-lookup"><span data-stu-id="83ade-116">Configuration</span></span>

<span data-ttu-id="83ade-117">`ConfigureServices`메서드에서 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> , 및 메서드를 사용 하 여 앱의 인증 체계를 구성 합니다 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> .</span><span class="sxs-lookup"><span data-stu-id="83ade-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="83ade-118">에 대 한 호출은 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 앱의를 설정 합니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> .</span><span class="sxs-lookup"><span data-stu-id="83ade-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="83ade-119">는 `DefaultScheme` 다음 `HttpContext` 인증 확장 프로그램 메서드에서 사용 하는 기본 체계입니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="83ade-120">앱의 `DefaultScheme` 를 [ Cookie authenticationdefaults로 설정 합니다. authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s")는 Cookie 이러한 확장 메서드에 대 한 기본 체계로 s를 사용 하도록 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="83ade-121">앱을 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google")로 설정 하면에 대 한 호출에 대 한 기본 체계로 Google을 사용 하도록 앱을 구성 합니다 `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="83ade-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="83ade-122">`DefaultChallengeScheme`는 `DefaultScheme`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="83ade-123"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>설정 시를 재정의 하는 추가 속성은를 참조 하세요 `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="83ade-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="83ade-124">에서 및 호출 `Startup.Configure` 사이에 및를 호출 `UseAuthentication` `UseAuthorization` `UseRouting` `UseEndpoints` 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="83ade-125">이렇게 하면 속성을 설정 `HttpContext.User` 하 고 요청에 대 한 권한 부여 미들웨어를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="83ade-126">인증 체계에 대 한 자세한 내용은 [인증 개념](xref:security/authentication/index#authentication-concepts)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="83ade-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="83ade-127">인증에 대 한 자세한 cookie 내용은을 참조 하십시오 <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="83ade-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="83ade-128">권한 부여 적용</span><span class="sxs-lookup"><span data-stu-id="83ade-128">Apply authorization</span></span>

<span data-ttu-id="83ade-129">`AuthorizeAttribute`컨트롤러, 작업 또는 페이지에 특성을 적용 하 여 앱의 인증 구성을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="83ade-130">다음 코드는 인증 된 사용자에 대 한 *개인 정보* 페이지에 대 한 액세스를 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="83ade-131">로그아웃</span><span class="sxs-lookup"><span data-stu-id="83ade-131">Sign out</span></span>

<span data-ttu-id="83ade-132">현재 사용자를 로그 아웃 하 고 삭제 하려면 cookie [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="83ade-133">다음 코드에서는 `Logout` 페이지 처리기를 *인덱스* 페이지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="83ade-134">에 대 한 호출은 `SignOutAsync` 인증 체계를 지정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="83ade-135">의 응용 프로그램 `DefaultScheme` 은 `CookieAuthenticationDefaults.AuthenticationScheme` 대체로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83ade-136">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="83ade-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83ade-137"><xref:security/authentication/social/index> 사용자가 외부 인증 공급자의 자격 증명으로 OAuth 2.0를 사용 하 여 로그인 할 수 있게 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="83ade-138">이 항목에서 설명 하는 접근 방식에는 인증 공급자로가 포함 되어 있습니다 ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="83ade-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="83ade-139">이 샘플에서는 **없이** 외부 인증 공급자를 사용 하는 방법을 보여 줍니다 ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="83ade-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="83ade-140">이 기능은의 모든 기능이 필요 하지는 ASP.NET Core Identity 않지만 여전히 신뢰할 수 있는 외부 인증 공급자와의 통합이 필요한 앱에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="83ade-141">이 샘플에서는 사용자를 인증 하는 데 [Google 인증](xref:security/authentication/google-logins) 을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="83ade-142">Google 인증을 사용 하면 로그인 프로세스를 관리 하는 복잡 한 여러 가지 복잡성이 Google으로 이동 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="83ade-143">다른 외부 인증 공급자와 통합 하려면 다음 항목을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="83ade-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="83ade-144">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="83ade-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="83ade-145">Microsoft 인증</span><span class="sxs-lookup"><span data-stu-id="83ade-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="83ade-146">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="83ade-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="83ade-147">기타 공급자</span><span class="sxs-lookup"><span data-stu-id="83ade-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="83ade-148">Configuration</span><span class="sxs-lookup"><span data-stu-id="83ade-148">Configuration</span></span>

<span data-ttu-id="83ade-149">`ConfigureServices`메서드에서 `AddAuthentication` , 및 메서드를 사용 하 여 앱의 인증 체계를 구성 합니다 `AddCookie` `AddGoogle` .</span><span class="sxs-lookup"><span data-stu-id="83ade-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="83ade-150">[Addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) 에 대 한 호출은 앱의 [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="83ade-151">는 `DefaultScheme` 다음 `HttpContext` 인증 확장 프로그램 메서드에서 사용 하는 기본 체계입니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="83ade-152">앱의 `DefaultScheme` 를 [ Cookie authenticationdefaults로 설정 합니다. authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s")는 Cookie 이러한 확장 메서드에 대 한 기본 체계로 s를 사용 하도록 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="83ade-153">앱을 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google")로 설정 하면에 대 한 호출에 대 한 기본 체계로 Google을 사용 하도록 앱을 구성 합니다 `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="83ade-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="83ade-154">`DefaultChallengeScheme`는 `DefaultScheme`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="83ade-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>설정 시를 재정의 하는 추가 속성은를 참조 하세요 `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="83ade-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="83ade-156">`Configure`메서드에서 메서드를 호출 하 여 `UseAuthentication` 속성을 설정 하는 인증 미들웨어를 호출 합니다 `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="83ade-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="83ade-157">또는를 `UseAuthentication` 호출 하기 전에 메서드를 호출 합니다 `UseMvcWithDefaultRoute` `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="83ade-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="83ade-158">인증 체계에 대 한 자세한 내용은 [인증 개념](xref:security/authentication/index#authentication-concepts)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="83ade-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="83ade-159">인증에 대 한 자세한 cookie 내용은을 참조 하십시오 <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="83ade-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="83ade-160">권한 부여 적용</span><span class="sxs-lookup"><span data-stu-id="83ade-160">Apply authorization</span></span>

<span data-ttu-id="83ade-161">`AuthorizeAttribute`컨트롤러, 작업 또는 페이지에 특성을 적용 하 여 앱의 인증 구성을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="83ade-162">다음 코드는 인증 된 사용자에 대 한 *개인 정보* 페이지에 대 한 액세스를 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="83ade-163">로그아웃</span><span class="sxs-lookup"><span data-stu-id="83ade-163">Sign out</span></span>

<span data-ttu-id="83ade-164">현재 사용자를 로그 아웃 하 고 삭제 하려면 cookie [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="83ade-165">다음 코드에서는 `Logout` 페이지 처리기를 *인덱스* 페이지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="83ade-166">에 대 한 호출은 `SignOutAsync` 인증 체계를 지정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="83ade-167">의 응용 프로그램 `DefaultScheme` 은 `CookieAuthenticationDefaults.AuthenticationScheme` 대체로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83ade-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83ade-168">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="83ade-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
