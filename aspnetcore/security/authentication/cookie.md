---
title: '인증을 사용 :::no-loc(cookie)::: 하지 않고 사용 :::no-loc(ASP.NET Core Identity):::'
author: rick-anderson
description: '를 사용 하지 않고 인증을 사용 하는 방법을 알아봅니다 :::no-loc(cookie)::: :::no-loc(ASP.NET Core Identity)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: 'security/authentication/:::no-loc(cookie):::'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061355"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="38b7a-103">인증을 사용 :::no-loc(cookie)::: 하지 않고 사용 :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="38b7a-103">Use :::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="38b7a-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="38b7a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38b7a-105">:::no-loc(ASP.NET Core Identity)::: 는 로그인을 만들고 유지 관리 하기 위한 완전 한 기능을 갖춘 완전 한 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-105">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="38b7a-106">그러나를 :::no-loc(cookie)::: 사용 하지 않는 기반 인증 공급자를 :::no-loc(ASP.NET Core Identity)::: 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-106">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="38b7a-107">자세한 내용은 <xref:security/authentication/identity>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="38b7a-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="38b7a-108">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="38b7a-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="38b7a-109">샘플 앱의 데모용으로, 가상 사용자 (민 Rodriguez)의 사용자 계정이 앱에 하드 코딩 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="38b7a-110">**전자 메일** 주소 `maria.rodriguez@contoso.com` 및 암호를 사용 하 여 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="38b7a-111">사용자는 `AuthenticateUser` *Pages/Account/Login. cshtml* 파일의 메서드에서 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="38b7a-112">실제 예제에서는 사용자가 데이터베이스에 대해 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="38b7a-113">Configuration</span><span class="sxs-lookup"><span data-stu-id="38b7a-113">Configuration</span></span>

<span data-ttu-id="38b7a-114">`Startup.ConfigureServices`메서드에서 및 메서드를 사용 하 여 인증 미들웨어 서비스를 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 만듭니다 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> .</span><span class="sxs-lookup"><span data-stu-id="38b7a-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="38b7a-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> 에 전달 된는 `AddAuthentication` 앱에 대 한 기본 인증 체계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="38b7a-116">`AuthenticationScheme` 는 인증 인스턴스가 여러 개 있고 :::no-loc(cookie)::: [특정 스키마를 사용 하 여 권한을 부여](xref:security/authorization/limitingidentitybyscheme)하려는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-116">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="38b7a-117">`AuthenticationScheme`를 [ :::no-loc(Cookie)::: authenticationdefaults로 설정 합니다. authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) 은 :::no-loc(Cookie)::: 스키마에 대해 "s" 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-117">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="38b7a-118">체계를 구별 하는 모든 문자열 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="38b7a-119">앱의 인증 체계가 앱의 인증 체계와 다릅니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="38b7a-119">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="38b7a-120">:::no-loc(cookie):::인증 체계가에 제공 되지 않는 경우 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s")를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-120">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="38b7a-121">인증 :::no-loc(cookie)::: 의 <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> 속성은 기본적으로로 설정 됩니다 `true` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-121">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="38b7a-122">:::no-loc(cookie):::사이트 방문자가 데이터 수집에 동의한 하지 않은 경우에는 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-122">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="38b7a-123">자세한 내용은 <xref:security/gdpr#essential-:::no-loc(cookie):::s>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="38b7a-123">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="38b7a-124">에서 `Startup.Configure` 및를 `UseAuthentication` 호출 `UseAuthorization` 하 여 속성을 설정 하 `HttpContext.User` 고 요청에 대 한 권한 부여 미들웨어를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="38b7a-125">`UseAuthentication`를 `UseAuthorization` 호출 하기 전에 및 메서드를 호출 합니다 `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="38b7a-126"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>클래스는 인증 공급자 옵션을 구성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-126">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="38b7a-127">`:::no-loc(Cookie):::AuthenticationOptions`메서드의 인증에 대 한 서비스 구성에서 설정 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-127">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="38b7a-128">:::no-loc(Cookie)::: 정책 미들웨어</span><span class="sxs-lookup"><span data-stu-id="38b7a-128">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="38b7a-129">[ :::no-loc(Cookie)::: 정책 미들웨어](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) :::no-loc(cookie)::: 를 통해 정책 기능을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-129">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="38b7a-130">응용 프로그램 처리 파이프라인에 미들웨어를 추가 하는 것은 순서를 구분 하기 때문에 &mdash; 파이프라인에 등록 된 다운스트림 구성 요소에만 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="38b7a-131"><xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions>정책 미들웨어에 제공 된를 사용 :::no-loc(Cookie)::: 하 여 :::no-loc(cookie)::: :::no-loc(cookie)::: :::no-loc(cookie)::: 가 추가 되거나 삭제 될 때 처리 및 처리 처리기에 대 한 전역 특성을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-131">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="38b7a-132">기본값은 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> `SameSiteMode.Lax` OAuth2 인증을 허용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-132">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="38b7a-133">의 동일한 사이트 정책을 엄격 하 게 적용 하려면를 `SameSiteMode.Strict` 설정 `MinimumSameSitePolicy` 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="38b7a-134">이 설정은 OAuth2 및 다른 크로스-원본 인증 스키마를 중단 하지만 :::no-loc(cookie)::: 원본 간 요청 처리에 의존 하지 않는 다른 유형의 앱에 대 한 보안 수준을 강화 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="38b7a-135">:::no-loc(Cookie):::의 정책 미들웨어 설정은 `MinimumSameSitePolicy` `:::no-loc(Cookie):::.SameSite` `:::no-loc(Cookie):::AuthenticationOptions` 아래 행렬에 따라 설정의 설정에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-135">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="38b7a-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="38b7a-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="38b7a-137">:::no-loc(Cookie):::. SameSite</span><span class="sxs-lookup"><span data-stu-id="38b7a-137">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="38b7a-138">결과 :::no-loc(Cookie)::: . SameSite 설정</span><span class="sxs-lookup"><span data-stu-id="38b7a-138">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="38b7a-139">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="38b7a-139">SameSiteMode.None</span></span>     | <span data-ttu-id="38b7a-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="38b7a-140">SameSiteMode.None</span></span><br><span data-ttu-id="38b7a-141">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="38b7a-142">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="38b7a-143">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="38b7a-143">SameSiteMode.None</span></span><br><span data-ttu-id="38b7a-144">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="38b7a-145">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="38b7a-146">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="38b7a-147">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="38b7a-147">SameSiteMode.None</span></span><br><span data-ttu-id="38b7a-148">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="38b7a-149">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="38b7a-150">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="38b7a-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="38b7a-152">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="38b7a-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="38b7a-154">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="38b7a-154">SameSiteMode.None</span></span><br><span data-ttu-id="38b7a-155">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="38b7a-156">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="38b7a-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="38b7a-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="38b7a-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="38b7a-160">인증 만들기 :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="38b7a-160">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="38b7a-161">:::no-loc(cookie):::보유 사용자 정보를 만들려면를 구성 <xref:System.Security.Claims.ClaimsPrincipal> 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-161">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="38b7a-162">사용자 정보는 serialize 되 고에 저장 됩니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="38b7a-162">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="38b7a-163">필요한를 사용 하 여를 만들고를 <xref:System.Security.Claims.Claims:::no-loc(Identity):::> <xref:System.Security.Claims.Claim> 호출 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 하 여 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-163">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="38b7a-164">`SignInAsync` 암호화 된를 만들어 :::no-loc(cookie)::: 현재 응답에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-164">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="38b7a-165">`AuthenticationScheme`을 지정 하지 않으면 기본 체계가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="38b7a-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> 는 기본적으로 몇 가지 특정 경로 (예: 로그인 경로 및 로그 아웃 경로) 에서만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="38b7a-167">자세한 내용은 [ :::no-loc(Cookie)::: authenticationhandler 원본](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="38b7a-167">For more information see the [:::no-loc(Cookie):::AuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="38b7a-168">ASP.NET Core의 [데이터 보호](xref:security/data-protection/using-data-protection) 시스템이 암호화에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="38b7a-169">여러 컴퓨터에서 호스트 되는 앱의 경우, 앱 간에 부하를 분산 하거나 웹 팜을 사용 하 여 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 하 여 동일한 키 링 및 앱 식별자를 사용 하도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="38b7a-170">로그아웃</span><span class="sxs-lookup"><span data-stu-id="38b7a-170">Sign out</span></span>

<span data-ttu-id="38b7a-171">현재 사용자를 로그 아웃 하 고 삭제 하려면 :::no-loc(cookie)::: 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> .</span><span class="sxs-lookup"><span data-stu-id="38b7a-171">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="38b7a-172">`:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme`(또는 " :::no-loc(Cookie)::: s")가 구성표 (예: "Contoso")로 사용 되지 않는 경우 :::no-loc(Cookie)::: 인증 공급자를 구성할 때 사용 되는 체계를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-172">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="38b7a-173">그렇지 않으면 기본 체계가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="38b7a-174">브라우저가 닫히면 세션 기반 :::no-loc(cookie)::: s (비영구 s)가 자동으로 삭제 :::no-loc(cookie)::: 되지만 :::no-loc(cookie)::: 개별 탭이 닫히면 s는 제거 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-174">When the browser closes it automatically deletes session based :::no-loc(cookie):::s (non-persistent :::no-loc(cookie):::s), but no :::no-loc(cookie):::s are cleared when an individual tab is closed.</span></span> <span data-ttu-id="38b7a-175">서버에 탭 또는 브라우저 닫기 이벤트에 대 한 알림이 표시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="38b7a-176">백 엔드 변경 내용에 대응</span><span class="sxs-lookup"><span data-stu-id="38b7a-176">React to back-end changes</span></span>

<span data-ttu-id="38b7a-177">가 :::no-loc(cookie)::: 만들어지면는 :::no-loc(cookie)::: id의 단일 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-177">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="38b7a-178">백 엔드 시스템에서 사용자 계정을 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="38b7a-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="38b7a-179">앱의 :::no-loc(cookie)::: 인증 시스템은 인증에 따라 요청을 계속 처리 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="38b7a-179">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="38b7a-180">인증이 유효한 경우 사용자는 앱에 로그인 상태를 유지 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="38b7a-180">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="38b7a-181"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*>이벤트를 사용 하 여 id의 유효성 검사를 가로채 고 재정의할 수 있습니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="38b7a-181">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="38b7a-182">모든 요청에서의 유효성을 검사 하면 :::no-loc(cookie)::: 해지 된 사용자가 앱에 액세스 하는 위험을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-182">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="38b7a-183">유효성 검사에 대 한 한 가지 방법은 :::no-loc(cookie)::: 사용자 데이터베이스가 변경 되는 시기를 추적 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-183">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="38b7a-184">사용자가 발급 된 이후에 데이터베이스가 변경 되지 않은 경우에 :::no-loc(cookie)::: 도 해당 사용자를 다시 인증할 필요가 없습니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="38b7a-184">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="38b7a-185">샘플 앱에서 데이터베이스는에서 구현 되 `IUserRepository` 고 값을 저장 합니다 `LastChanged` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="38b7a-186">데이터베이스에서 사용자를 업데이트 하는 경우 값은 `LastChanged` 현재 시간으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="38b7a-187">:::no-loc(cookie):::값에 따라 데이터베이스가 변경 될 때를 무효화 하려면 데이터베이스의 `LastChanged` 현재 값이 포함 된 클레임을 사용 하 여를 만듭니다 :::no-loc(cookie)::: `LastChanged` `LastChanged` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-187">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="38b7a-188">이벤트에 대 한 재정의를 구현 하려면 `ValidatePrincipal` 에서 파생 되는 클래스에 다음 시그니처를 사용 하 여 메서드를 작성 합니다 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> .</span><span class="sxs-lookup"><span data-stu-id="38b7a-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="38b7a-189">다음은의 구현 예제입니다 `:::no-loc(Cookie):::AuthenticationEvents` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-189">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
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
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="38b7a-190">메서드에서 서비스를 등록 하는 동안 events 인스턴스를 등록 :::no-loc(cookie)::: `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-190">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="38b7a-191">클래스에 대해 범위가 지정 된 [서비스 등록](xref:fundamentals/dependency-injection#service-lifetimes) 을 제공 합니다 `Custom:::no-loc(Cookie):::AuthenticationEvents` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="38b7a-192">사용자의 이름이 &mdash; 보안에 영향을 주지 않는 결정을 업데이트 하는 상황을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="38b7a-193">사용자 보안 주체를 destructively 업데이트 하려면를 호출 하 `context.ReplacePrincipal` 고 `context.ShouldRenew` 속성을로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="38b7a-194">여기에 설명 된 방법은 모든 요청에서 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="38b7a-195">모든 :::no-loc(cookie)::: 요청에서 모든 사용자에 대 한 인증의 유효성을 검사 하면 앱에 대 한 성능 저하가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-195">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="38b7a-196">영구 :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="38b7a-196">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="38b7a-197">가 :::no-loc(cookie)::: 브라우저 세션에서 지속 되도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-197">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="38b7a-198">이 지 속성은 로그인 또는 유사한 메커니즘에서 "사용자 이름" 확인란을 사용 하 여 명시적인 사용자 동의로만 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="38b7a-199">다음 코드 조각에서는 id를 만들고 :::no-loc(cookie)::: 브라우저 클로저를 통해 해당 하는 해당 하는를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-199">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="38b7a-200">이전에 구성 된 슬라이딩 만료 설정은 모두 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="38b7a-201">브라우저를 :::no-loc(cookie)::: 닫을 때가 만료 되 면 브라우저가 :::no-loc(cookie)::: 다시 시작 되 면를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-201">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="38b7a-202"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>에서로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="38b7a-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="38b7a-203">절대 :::no-loc(cookie)::: 만료</span><span class="sxs-lookup"><span data-stu-id="38b7a-203">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="38b7a-204">절대 만료 시간은를 사용 하 여 설정할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="38b7a-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="38b7a-205">영구을 만들려면 :::no-loc(cookie)::: `IsPersistent` 도 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-205">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="38b7a-206">그렇지 않은 경우는 :::no-loc(cookie)::: 세션 기반 수명으로 생성 되며 보유 한 인증 티켓 전이나 후에 만료 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-206">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="38b7a-207">`ExpiresUtc`가 설정 되 면 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> 설정 된 경우의 옵션 값을 재정의 합니다 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> .</span><span class="sxs-lookup"><span data-stu-id="38b7a-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="38b7a-208">다음 코드 조각에서는 id를 만들고 해당 id를 :::no-loc(cookie)::: 20 분 동안 지속 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-208">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="38b7a-209">이렇게 하면 이전에 구성 된 슬라이딩 만료 설정이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="38b7a-210">:::no-loc(ASP.NET Core Identity)::: 는 로그인을 만들고 유지 관리 하기 위한 완전 한 기능을 갖춘 완전 한 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-210">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="38b7a-211">그러나를 :::no-loc(cookie)::: 사용 하지 않는 기반 인증 공급자를 :::no-loc(ASP.NET Core Identity)::: 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-211">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="38b7a-212">자세한 내용은 <xref:security/authentication/identity>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="38b7a-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="38b7a-213">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="38b7a-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="38b7a-214">샘플 앱의 데모용으로, 가상 사용자 (민 Rodriguez)의 사용자 계정이 앱에 하드 코딩 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="38b7a-215">**전자 메일** 주소 `maria.rodriguez@contoso.com` 및 암호를 사용 하 여 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="38b7a-216">사용자는 `AuthenticateUser` *Pages/Account/Login. cshtml* 파일의 메서드에서 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="38b7a-217">실제 예제에서는 사용자가 데이터베이스에 대해 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="38b7a-218">Configuration</span><span class="sxs-lookup"><span data-stu-id="38b7a-218">Configuration</span></span>

<span data-ttu-id="38b7a-219">앱이 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 사용 하지 않는 경우 프로젝트 파일에서 [AspNetCore :::no-loc(Cookie)::: 에 대 한 패키지 참조를 만듭니다. s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) 패키지.</span><span class="sxs-lookup"><span data-stu-id="38b7a-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) package.</span></span>

<span data-ttu-id="38b7a-220">`Startup.ConfigureServices`메서드에서 및 메서드를 사용 하 여 인증 미들웨어 서비스를 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 만듭니다 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> .</span><span class="sxs-lookup"><span data-stu-id="38b7a-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="38b7a-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> 에 전달 된는 `AddAuthentication` 앱에 대 한 기본 인증 체계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="38b7a-222">`AuthenticationScheme` 는 인증 인스턴스가 여러 개 있고 :::no-loc(cookie)::: [특정 스키마를 사용 하 여 권한을 부여](xref:security/authorization/limitingidentitybyscheme)하려는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-222">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="38b7a-223">`AuthenticationScheme`를 [ :::no-loc(Cookie)::: authenticationdefaults로 설정 합니다. authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) 은 :::no-loc(Cookie)::: 스키마에 대해 "s" 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-223">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="38b7a-224">체계를 구별 하는 모든 문자열 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="38b7a-225">앱의 인증 체계가 앱의 인증 체계와 다릅니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="38b7a-225">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="38b7a-226">:::no-loc(cookie):::인증 체계가에 제공 되지 않는 경우 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s")를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-226">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="38b7a-227">인증 :::no-loc(cookie)::: 의 <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> 속성은 기본적으로로 설정 됩니다 `true` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-227">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="38b7a-228">:::no-loc(cookie):::사이트 방문자가 데이터 수집에 동의한 하지 않은 경우에는 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-228">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="38b7a-229">자세한 내용은 <xref:security/gdpr#essential-:::no-loc(cookie):::s>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="38b7a-229">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="38b7a-230">`Startup.Configure`메서드에서 메서드를 호출 하 여 `UseAuthentication` 속성을 설정 하는 인증 미들웨어를 호출 합니다 `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="38b7a-231">또는를 `UseAuthentication` 호출 하기 전에 메서드를 호출 합니다 `UseMvcWithDefaultRoute` `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="38b7a-232"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>클래스는 인증 공급자 옵션을 구성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-232">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="38b7a-233">`:::no-loc(Cookie):::AuthenticationOptions`메서드의 인증에 대 한 서비스 구성에서 설정 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-233">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="38b7a-234">:::no-loc(Cookie)::: 정책 미들웨어</span><span class="sxs-lookup"><span data-stu-id="38b7a-234">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="38b7a-235">[ :::no-loc(Cookie)::: 정책 미들웨어](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) :::no-loc(cookie)::: 를 통해 정책 기능을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-235">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="38b7a-236">응용 프로그램 처리 파이프라인에 미들웨어를 추가 하는 것은 순서를 구분 하기 때문에 &mdash; 파이프라인에 등록 된 다운스트림 구성 요소에만 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="38b7a-237"><xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions>정책 미들웨어에 제공 된를 사용 :::no-loc(Cookie)::: 하 여 :::no-loc(cookie)::: :::no-loc(cookie)::: :::no-loc(cookie)::: 가 추가 되거나 삭제 될 때 처리 및 처리 처리기에 대 한 전역 특성을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-237">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="38b7a-238">기본값은 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> `SameSiteMode.Lax` OAuth2 인증을 허용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-238">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="38b7a-239">의 동일한 사이트 정책을 엄격 하 게 적용 하려면를 `SameSiteMode.Strict` 설정 `MinimumSameSitePolicy` 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="38b7a-240">이 설정은 OAuth2 및 다른 크로스-원본 인증 스키마를 중단 하지만 :::no-loc(cookie)::: 원본 간 요청 처리에 의존 하지 않는 다른 유형의 앱에 대 한 보안 수준을 강화 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="38b7a-241">:::no-loc(Cookie):::의 정책 미들웨어 설정은 `MinimumSameSitePolicy` `:::no-loc(Cookie):::.SameSite` `:::no-loc(Cookie):::AuthenticationOptions` 아래 행렬에 따라 설정의 설정에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-241">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="38b7a-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="38b7a-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="38b7a-243">:::no-loc(Cookie):::. SameSite</span><span class="sxs-lookup"><span data-stu-id="38b7a-243">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="38b7a-244">결과 :::no-loc(Cookie)::: . SameSite 설정</span><span class="sxs-lookup"><span data-stu-id="38b7a-244">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="38b7a-245">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="38b7a-245">SameSiteMode.None</span></span>     | <span data-ttu-id="38b7a-246">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="38b7a-246">SameSiteMode.None</span></span><br><span data-ttu-id="38b7a-247">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="38b7a-248">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="38b7a-249">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="38b7a-249">SameSiteMode.None</span></span><br><span data-ttu-id="38b7a-250">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="38b7a-251">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="38b7a-252">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="38b7a-253">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="38b7a-253">SameSiteMode.None</span></span><br><span data-ttu-id="38b7a-254">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="38b7a-255">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="38b7a-256">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="38b7a-257">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="38b7a-258">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="38b7a-259">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="38b7a-260">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="38b7a-260">SameSiteMode.None</span></span><br><span data-ttu-id="38b7a-261">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="38b7a-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="38b7a-262">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="38b7a-263">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="38b7a-264">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="38b7a-265">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="38b7a-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="38b7a-266">인증 만들기 :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="38b7a-266">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="38b7a-267">:::no-loc(cookie):::보유 사용자 정보를 만들려면를 구성 <xref:System.Security.Claims.ClaimsPrincipal> 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-267">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="38b7a-268">사용자 정보는 serialize 되 고에 저장 됩니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="38b7a-268">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="38b7a-269">필요한를 사용 하 여를 만들고를 <xref:System.Security.Claims.Claims:::no-loc(Identity):::> <xref:System.Security.Claims.Claim> 호출 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 하 여 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-269">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="38b7a-270">`SignInAsync` 암호화 된를 만들어 :::no-loc(cookie)::: 현재 응답에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-270">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="38b7a-271">`AuthenticationScheme`을 지정 하지 않으면 기본 체계가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="38b7a-272">ASP.NET Core의 [데이터 보호](xref:security/data-protection/using-data-protection) 시스템이 암호화에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="38b7a-273">여러 컴퓨터에서 호스트 되는 앱의 경우, 앱 간에 부하를 분산 하거나 웹 팜을 사용 하 여 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 하 여 동일한 키 링 및 앱 식별자를 사용 하도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="38b7a-274">로그아웃</span><span class="sxs-lookup"><span data-stu-id="38b7a-274">Sign out</span></span>

<span data-ttu-id="38b7a-275">현재 사용자를 로그 아웃 하 고 삭제 하려면 :::no-loc(cookie)::: 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> .</span><span class="sxs-lookup"><span data-stu-id="38b7a-275">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="38b7a-276">`:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme`(또는 " :::no-loc(Cookie)::: s")가 구성표 (예: "Contoso")로 사용 되지 않는 경우 :::no-loc(Cookie)::: 인증 공급자를 구성할 때 사용 되는 체계를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-276">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="38b7a-277">그렇지 않으면 기본 체계가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="38b7a-278">백 엔드 변경 내용에 대응</span><span class="sxs-lookup"><span data-stu-id="38b7a-278">React to back-end changes</span></span>

<span data-ttu-id="38b7a-279">가 :::no-loc(cookie)::: 만들어지면는 :::no-loc(cookie)::: id의 단일 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-279">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="38b7a-280">백 엔드 시스템에서 사용자 계정을 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="38b7a-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="38b7a-281">앱의 :::no-loc(cookie)::: 인증 시스템은 인증에 따라 요청을 계속 처리 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="38b7a-281">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="38b7a-282">인증이 유효한 경우 사용자는 앱에 로그인 상태를 유지 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="38b7a-282">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="38b7a-283"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*>이벤트를 사용 하 여 id의 유효성 검사를 가로채 고 재정의할 수 있습니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="38b7a-283">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="38b7a-284">모든 요청에서의 유효성을 검사 하면 :::no-loc(cookie)::: 해지 된 사용자가 앱에 액세스 하는 위험을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-284">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="38b7a-285">유효성 검사에 대 한 한 가지 방법은 :::no-loc(cookie)::: 사용자 데이터베이스가 변경 되는 시기를 추적 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-285">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="38b7a-286">사용자가 발급 된 이후에 데이터베이스가 변경 되지 않은 경우에 :::no-loc(cookie)::: 도 해당 사용자를 다시 인증할 필요가 없습니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="38b7a-286">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="38b7a-287">샘플 앱에서 데이터베이스는에서 구현 되 `IUserRepository` 고 값을 저장 합니다 `LastChanged` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="38b7a-288">데이터베이스에서 사용자를 업데이트 하는 경우 값은 `LastChanged` 현재 시간으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="38b7a-289">:::no-loc(cookie):::값에 따라 데이터베이스가 변경 될 때를 무효화 하려면 데이터베이스의 `LastChanged` 현재 값이 포함 된 클레임을 사용 하 여를 만듭니다 :::no-loc(cookie)::: `LastChanged` `LastChanged` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-289">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="38b7a-290">이벤트에 대 한 재정의를 구현 하려면 `ValidatePrincipal` 에서 파생 되는 클래스에 다음 시그니처를 사용 하 여 메서드를 작성 합니다 <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> .</span><span class="sxs-lookup"><span data-stu-id="38b7a-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="38b7a-291">다음은의 구현 예제입니다 `:::no-loc(Cookie):::AuthenticationEvents` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-291">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
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
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="38b7a-292">메서드에서 서비스를 등록 하는 동안 events 인스턴스를 등록 :::no-loc(cookie)::: `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-292">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="38b7a-293">클래스에 대해 범위가 지정 된 [서비스 등록](xref:fundamentals/dependency-injection#service-lifetimes) 을 제공 합니다 `Custom:::no-loc(Cookie):::AuthenticationEvents` .</span><span class="sxs-lookup"><span data-stu-id="38b7a-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="38b7a-294">사용자의 이름이 &mdash; 보안에 영향을 주지 않는 결정을 업데이트 하는 상황을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="38b7a-295">사용자 보안 주체를 destructively 업데이트 하려면를 호출 하 `context.ReplacePrincipal` 고 `context.ShouldRenew` 속성을로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="38b7a-296">여기에 설명 된 방법은 모든 요청에서 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="38b7a-297">모든 :::no-loc(cookie)::: 요청에서 모든 사용자에 대 한 인증의 유효성을 검사 하면 앱에 대 한 성능 저하가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-297">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="38b7a-298">영구 :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="38b7a-298">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="38b7a-299">가 :::no-loc(cookie)::: 브라우저 세션에서 지속 되도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-299">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="38b7a-300">이 지 속성은 로그인 또는 유사한 메커니즘에서 "사용자 이름" 확인란을 사용 하 여 명시적인 사용자 동의로만 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="38b7a-301">다음 코드 조각에서는 id를 만들고 :::no-loc(cookie)::: 브라우저 클로저를 통해 해당 하는 해당 하는를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-301">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="38b7a-302">이전에 구성 된 슬라이딩 만료 설정은 모두 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="38b7a-303">브라우저를 :::no-loc(cookie)::: 닫을 때가 만료 되 면 브라우저가 :::no-loc(cookie)::: 다시 시작 되 면를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-303">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="38b7a-304"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>에서로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="38b7a-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="38b7a-305">절대 :::no-loc(cookie)::: 만료</span><span class="sxs-lookup"><span data-stu-id="38b7a-305">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="38b7a-306">절대 만료 시간은를 사용 하 여 설정할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="38b7a-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="38b7a-307">영구을 만들려면 :::no-loc(cookie)::: `IsPersistent` 도 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-307">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="38b7a-308">그렇지 않은 경우는 :::no-loc(cookie)::: 세션 기반 수명으로 생성 되며 보유 한 인증 티켓 전이나 후에 만료 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-308">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="38b7a-309">`ExpiresUtc`가 설정 되 면 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> 설정 된 경우의 옵션 값을 재정의 합니다 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions> .</span><span class="sxs-lookup"><span data-stu-id="38b7a-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="38b7a-310">다음 코드 조각에서는 id를 만들고 해당 id를 :::no-loc(cookie)::: 20 분 동안 지속 합니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-310">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="38b7a-311">이렇게 하면 이전에 구성 된 슬라이딩 만료 설정이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="38b7a-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="38b7a-312">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="38b7a-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="38b7a-313">정책 기반 역할 검사</span><span class="sxs-lookup"><span data-stu-id="38b7a-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
