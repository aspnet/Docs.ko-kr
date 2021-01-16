---
title: cookieASP.NET apps 간에 인증 공유
author: rick-anderson
description: cookieASP.NET 4.x 및 ASP.NET Core apps 간의 인증을 공유 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 0d43bbbc44015aff040b12dfacb260fe50492e54
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252996"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="05fc6-103">cookieASP.NET apps 간에 인증 공유</span><span class="sxs-lookup"><span data-stu-id="05fc6-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="05fc6-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="05fc6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="05fc6-105">Websites는 일반적으로 함께 작동 하는 개별 웹 앱으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="05fc6-106">SSO (Single Sign-On) 환경을 제공 하려면 사이트 내의 웹 앱이 인증을 공유 해야 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="05fc6-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="05fc6-107">이 시나리오를 지원 하기 위해 데이터 보호 스택에서 Katana cookie 인증 및 ASP.NET Core 인증 티켓을 공유할 수 있습니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="05fc6-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="05fc6-108">다음 예제에서:</span><span class="sxs-lookup"><span data-stu-id="05fc6-108">In the examples that follow:</span></span>

* <span data-ttu-id="05fc6-109">인증 cookie 이름이의 일반 값으로 설정 됩니다 `.AspNet.SharedCookie` .</span><span class="sxs-lookup"><span data-stu-id="05fc6-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="05fc6-110">는 `AuthenticationType` 명시적으로 또는 기본적으로로 설정 됩니다 `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="05fc6-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="05fc6-111">공통 앱 이름은 데이터 보호 시스템에서 데이터 보호 키 ()를 공유 하도록 설정 하는 데 사용 됩니다 `SharedCookieApp` .</span><span class="sxs-lookup"><span data-stu-id="05fc6-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="05fc6-112">`Identity.Application` 는 인증 체계로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="05fc6-113">사용 되는 스키마는 모두 공유 된 앱 *내에서* 일관 되 게 사용 되어야 하며, cookie 기본 체계로 사용 하거나 명시적으로 설정 하 여 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="05fc6-114">체계는를 암호화 하 고 암호를 해독 하는 데 사용 cookie 되므로 앱 간에 일관 된 스키마를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="05fc6-115">일반적인 [데이터 보호 키](xref:security/data-protection/implementation/key-management) 저장소 위치가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="05fc6-116">ASP.NET Core 앱에서 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 는 키 저장소 위치를 설정 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="05fc6-117">.NET Framework 앱에서 Cookie 인증 미들웨어는의 구현을 사용 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="05fc6-118">`DataProtectionProvider` 는 인증 페이로드 데이터의 암호화 및 암호 해독을 위한 데이터 보호 서비스를 제공 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="05fc6-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="05fc6-119">`DataProtectionProvider`인스턴스는 응용 프로그램의 다른 부분에서 사용 되는 데이터 보호 시스템에서 격리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="05fc6-120">[DataProtectionProvider (system.io.directoryinfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) 은를 허용 <xref:System.IO.DirectoryInfo> 하 여 데이터 보호 키 저장소의 위치를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="05fc6-121">`DataProtectionProvider`[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 패키지가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="05fc6-122">ASP.NET Core 2.x 앱에서 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="05fc6-123">.NET Framework apps에서 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="05fc6-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 공통 앱 이름을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="05fc6-125">cookie인증 공유ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="05fc6-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="05fc6-126">ASP.NET Core Identity를 사용하는 경우:</span><span class="sxs-lookup"><span data-stu-id="05fc6-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="05fc6-127">데이터 보호 키와 앱 이름은 앱 간에 공유 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="05fc6-128">다음 예에서는 일반 키 저장소 위치가 메서드에 제공 됩니다 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> .</span><span class="sxs-lookup"><span data-stu-id="05fc6-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="05fc6-129"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>다음 예에서는를 사용 하 여 공통 공유 앱 이름을 구성 `SharedCookieApp` 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="05fc6-130">자세한 내용은 <xref:security/data-protection/configuration/overview>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="05fc6-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="05fc6-131"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*>확장 메서드를 사용 하 여에 대 한 데이터 보호 서비스를 설정 cookie 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="05fc6-132">기본 인증 형식은 `Identity.Application` 입니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="05fc6-133">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="05fc6-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="05fc6-134">**참고:** 위의 지침은 ()에서 작동 하지 않습니다 `ITicketStore` `CookieAuthenticationOptions.SessionStore` .</span><span class="sxs-lookup"><span data-stu-id="05fc6-134">**Note:** The preceding instructions don't work with `ITicketStore` (`CookieAuthenticationOptions.SessionStore`).</span></span>  <span data-ttu-id="05fc6-135">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore.Docs/issues/21163)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="05fc6-135">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span></span>

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="05fc6-136">다음을 사용 cookie 하지 않고 인증 공유 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="05fc6-136">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="05fc6-137">을 (를 cookie ) 사용 하지 않고을 직접 사용 하 ASP.NET Core Identity 는 경우에서 데이터 보호 및 인증을 구성 `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-137">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="05fc6-138">다음 예제에서는 인증 형식이로 설정 됩니다 `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="05fc6-138">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="05fc6-139">cookie여러 기본 경로에서 s 공유</span><span class="sxs-lookup"><span data-stu-id="05fc6-139">Share cookies across different base paths</span></span>

<span data-ttu-id="05fc6-140">인증은 cookie [HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 을 기본값으로 사용 [ Cookie 합니다. 경로](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span><span class="sxs-lookup"><span data-stu-id="05fc6-140">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="05fc6-141">앱 cookie 을 다른 기본 경로에서 공유 해야 하는 경우에는를 `Path` 재정의 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-141">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="05fc6-142">하위 cookie 도메인 간 공유</span><span class="sxs-lookup"><span data-stu-id="05fc6-142">Share cookies across subdomains</span></span>

<span data-ttu-id="05fc6-143">하위 도메인에서를 공유 하는 앱을 호스트 하는 경우 cookie 에서 공통 도메인을 지정 [ Cookie 합니다. 도메인](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-143">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="05fc6-144">및와 cookie 같이에서 앱 간에를 공유 하려면를 `contoso.com` 다음과 같이 `first_subdomain.contoso.com` `second_subdomain.contoso.com` 지정 `Cookie.Domain` 합니다 `.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="05fc6-144">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="05fc6-145">휴지 상태의 데이터 보호 키 암호화</span><span class="sxs-lookup"><span data-stu-id="05fc6-145">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="05fc6-146">프로덕션 배포의 경우 `DataProtectionProvider` DPAPI 또는 X509Certificate를 사용 하 여 미사용 키를 암호화 하도록를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-146">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="05fc6-147">자세한 내용은 <xref:security/data-protection/implementation/key-encryption-at-rest>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="05fc6-147">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="05fc6-148">다음 예제에서는에 대 한 인증서 지문을 제공 합니다 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> .</span><span class="sxs-lookup"><span data-stu-id="05fc6-148">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="05fc6-149">ASP.NET 4.x cookie 와 ASP.NET Core apps 간에 인증 공유</span><span class="sxs-lookup"><span data-stu-id="05fc6-149">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="05fc6-150">Katana 인증 미들웨어를 사용 하는 ASP.NET 4. x 앱은 Cookie cookie ASP.NET Core 인증 미들웨어와 호환 되는 인증을 생성 하도록 구성할 수 있습니다 Cookie .</span><span class="sxs-lookup"><span data-stu-id="05fc6-150">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="05fc6-151">이를 통해 여러 단계로 많은 사이트의 개별 앱을 업그레이드할 수 있으며 사이트 전체에서 원활한 SSO 환경을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-151">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="05fc6-152">앱에서 Katana Cookie 인증 미들웨어를 사용 하는 경우 `UseCookieAuthentication` 프로젝트의 *Startup.Auth.cs* 파일에서를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-152">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="05fc6-153">Visual Studio 2013 이상에서 만든 ASP.NET 4.x 웹 앱 프로젝트는 Cookie 기본적으로 Katana Authentication 미들웨어를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-153">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="05fc6-154">`UseCookieAuthentication`는 ASP.NET Core apps에서 사용 되지 않으며 지원 되지 않지만 `UseCookieAuthentication` Katana 인증 미들웨어를 사용 하는 ASP.NET 4.x 앱에서 호출은 Cookie 유효 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-154">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="05fc6-155">ASP.NET 4.x 앱은 4.5.1 이상 .NET Framework 대상으로 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-155">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="05fc6-156">그렇지 않으면 필요한 NuGet 패키지를 설치 하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-156">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="05fc6-157">cookieASP.NET 4.x 앱과 ASP.NET Core 앱 간의 인증을 공유 하려면 [ cookie ASP.NET Core 앱 간 인증 공유](#share-authentication-cookies-with-aspnet-core-identity) 섹션에 설명 된 대로 ASP.NET Core 앱을 구성 하 고 다음과 같이 ASP.NET 4.x 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-157">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="05fc6-158">앱의 패키지가 최신 릴리스로 업데이트 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-158">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="05fc6-159">각 ASP.NET 4.x 앱에 [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-159">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="05fc6-160">호출을 찾아서 수정 합니다 `UseCookieAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="05fc6-160">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="05fc6-161">cookieASP.NET Core 인증 미들웨어에서 사용 하는 이름과 일치 하도록 이름을 변경 Cookie 합니다 ( `.AspNet.SharedCookie` 예제에서는).</span><span class="sxs-lookup"><span data-stu-id="05fc6-161">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="05fc6-162">다음 예제에서는 인증 형식이로 설정 됩니다 `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="05fc6-162">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="05fc6-163">`DataProtectionProvider`공통 데이터 보호 키 저장소 위치에 초기화 된의 인스턴스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-163">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="05fc6-164">앱 이름이 인증을 공유 하는 모든 앱에서 사용 하는 공용 앱 이름으로 설정 되어 있는지 확인 cookie 합니다 (이 `SharedCookieApp` 예제에서는).</span><span class="sxs-lookup"><span data-stu-id="05fc6-164">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="05fc6-165">및를 설정 하지 않으면 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 고유한 사용자를 구별 하는 클레임으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-165">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="05fc6-166">*App_Start/startup.auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="05fc6-166">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="05fc6-167">사용자 id를 생성 하는 경우 인증 유형 ( `Identity.Application` )이 `AuthenticationType` `UseCookieAuthentication` *App_Start/startup.auth.cs* 의로 설정 된에 정의 된 유형과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-167">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="05fc6-168">*모델/ Identity Models.cs*:</span><span class="sxs-lookup"><span data-stu-id="05fc6-168">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="05fc6-169">공용 사용자 데이터베이스 사용</span><span class="sxs-lookup"><span data-stu-id="05fc6-169">Use a common user database</span></span>

<span data-ttu-id="05fc6-170">앱에서 동일한 Identity 스키마 (동일한 버전의)를 사용 하는 경우 Identity Identity 각 앱의 시스템이 동일한 사용자 데이터베이스를 가리키고 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-170">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="05fc6-171">그렇지 않으면 id 시스템은 인증 정보를 데이터베이스의 정보에 대해 일치 시 키 려 고 할 때 런타임에 오류를 생성 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="05fc6-171">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="05fc6-172">앱에서 Identity 스키마가 서로 다를 때, 일반적으로 앱이 서로 다른 버전을 사용 하기 때문에 Identity 최신 버전을 기반으로 하는 공통 데이터베이스를 공유 하는 것은 Identity 다른 앱의 스키마에서 열을 다시 매핑 및 추가 하지 않고도 가능 Identity 합니다</span><span class="sxs-lookup"><span data-stu-id="05fc6-172">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="05fc6-173">Identity앱에서 공통 데이터베이스를 공유할 수 있도록 최신 버전을 사용 하도록 다른 앱을 업그레이드 하는 것이 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="05fc6-173">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="05fc6-174">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="05fc6-174">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
