---
title: '인증과 Identity ASP.NET Core 2.0으로 마이그레이션'
author: scottaddie
description: '이 문서에서는 ASP.NET Core 1.x 인증 및 ASP.NET Core 2.0를 마이그레이션하기 위한 가장 일반적인 단계를 간략하게 설명 합니다 Identity .'
ms.author: scaddie
ms.date: 06/21/2019
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
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: cad7582670013661f5fcbfbebad923f0f092462e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057182"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a><span data-ttu-id="43317-103">인증과 Identity ASP.NET Core 2.0으로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="43317-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="43317-104">[Scott Addie](https://github.com/scottaddie) 및 [jia-hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="43317-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="43317-105">ASP.NET Core 2.0에는 인증을 위한 새 모델이 있으며 [Identity](xref:security/authentication/identity) 서비스를 사용 하 여 구성을 간소화 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="43317-106">인증을 사용 하거나 Identity 아래에 설명 된 대로 새 모델을 사용 하도록 업데이트 될 수 있는 ASP.NET Core 1.x 응용 프로그램입니다.</span><span class="sxs-lookup"><span data-stu-id="43317-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="43317-107">네임 스페이스 업데이트</span><span class="sxs-lookup"><span data-stu-id="43317-107">Update namespaces</span></span>

<span data-ttu-id="43317-108">1.x에서 `IdentityRole` 및와 같은 클래스 `IdentityUser` 는 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 네임 스페이스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="43317-109">2.0에서 <xref:Microsoft.AspNetCore.Identity> 네임 스페이스는 이러한 클래스 중 일부에 대 한 새 홈이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43317-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="43317-110">기본 코드를 사용 하는 Identity 경우 영향을 받는 클래스에는 및가 포함 됩니다 `ApplicationUser` `Startup` .</span><span class="sxs-lookup"><span data-stu-id="43317-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="43317-111">영향을 `using` 받는 참조를 확인 하도록 문을 조정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="43317-112">인증 미들웨어 및 서비스</span><span class="sxs-lookup"><span data-stu-id="43317-112">Authentication Middleware and services</span></span>

<span data-ttu-id="43317-113">1.x 프로젝트에서 인증은 미들웨어를 통해 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43317-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="43317-114">지원 하려는 각 인증 체계에 대해 미들웨어 메서드가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43317-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="43317-115">다음 1.x 예에서는 Startup.cs에서를 사용 하 여 Facebook 인증을 구성 합니다 Identity . *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="43317-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

<span data-ttu-id="43317-116">2.0 프로젝트에서 인증은 서비스를 통해 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43317-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="43317-117">각 인증 체계가 `ConfigureServices` *Startup.cs* 의 메서드에서 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43317-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs* .</span></span> <span data-ttu-id="43317-118">`UseIdentity`메서드는로 대체 됩니다 `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="43317-119">다음 2.0 예제에서는 Startup.cs에서를 사용 하 여 Facebook 인증을 구성 합니다 Identity . *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="43317-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="43317-120">`UseAuthentication`메서드는 자동 인증 및 원격 인증 요청 처리를 담당 하는 단일 인증 미들웨어 구성 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="43317-121">모든 개별 미들웨어 구성 요소를 하나의 공통 미들웨어 구성 요소로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="43317-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="43317-122">각 주요 인증 체계에 대 한 2.0 마이그레이션 지침은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="43317-123">Cookie기반 인증</span><span class="sxs-lookup"><span data-stu-id="43317-123">Cookie-based authentication</span></span>

<span data-ttu-id="43317-124">아래 두 옵션 중 하나를 선택 하 고 *Startup.cs* 에서 필요한 변경을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-124">Select one of the two options below, and make the necessary changes in *Startup.cs* :</span></span>

1. <span data-ttu-id="43317-125">cookie에서 사용Identity</span><span class="sxs-lookup"><span data-stu-id="43317-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="43317-126">`UseIdentity`메서드에서를로 바꿉니다 `UseAuthentication` `Configure` .</span><span class="sxs-lookup"><span data-stu-id="43317-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="43317-127">`AddIdentity`메서드에서 메서드를 호출 `ConfigureServices` 하 여 인증 서비스를 추가 cookie 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="43317-128">필요에 따라 `ConfigureApplicationCookie` 메서드에서 또는 `ConfigureExternalCookie` 메서드를 호출 `ConfigureServices` 하 여 설정을 조정 Identity cookie 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="43317-129">을 사용 cookie 하지 않고 s 사용 Identity</span><span class="sxs-lookup"><span data-stu-id="43317-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="43317-130">`UseCookieAuthentication`메서드의 메서드 호출을 `Configure` 다음으로 바꿉니다 `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="43317-131">`AddAuthentication`메서드에서 및 메서드를 호출 합니다 `AddCookie` `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="43317-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="43317-132">JWT 전달자 인증</span><span class="sxs-lookup"><span data-stu-id="43317-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="43317-133">*Startup.cs* 에서 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-133">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="43317-134">`UseJwtBearerAuthentication`메서드의 메서드 호출을 `Configure` 다음으로 바꿉니다 `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="43317-135">`AddJwtBearer`메서드에서 메서드를 호출 합니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="43317-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="43317-136">이 코드 조각에서는를 사용 하지 Identity 않으므로 메서드에 전달 하 여 기본 체계를 설정 해야 합니다 `JwtBearerDefaults.AuthenticationScheme` `AddAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="43317-137">OIDC (Openid connect Connect) 인증</span><span class="sxs-lookup"><span data-stu-id="43317-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="43317-138">*Startup.cs* 에서 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-138">Make the following changes in *Startup.cs* :</span></span>

- <span data-ttu-id="43317-139">`UseOpenIdConnectAuthentication`메서드의 메서드 호출을 `Configure` 다음으로 바꿉니다 `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="43317-140">`AddOpenIdConnect`메서드에서 메서드를 호출 합니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="43317-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- <span data-ttu-id="43317-141">`PostLogoutRedirectUri`작업의 속성을 `OpenIdConnectOptions` 다음으로 바꿉니다 `SignedOutRedirectUri` .</span><span class="sxs-lookup"><span data-stu-id="43317-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="43317-142">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="43317-142">Facebook authentication</span></span>

<span data-ttu-id="43317-143">*Startup.cs* 에서 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-143">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="43317-144">`UseFacebookAuthentication`메서드의 메서드 호출을 `Configure` 다음으로 바꿉니다 `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="43317-145">`AddFacebook`메서드에서 메서드를 호출 합니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="43317-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="43317-146">Google 인증</span><span class="sxs-lookup"><span data-stu-id="43317-146">Google authentication</span></span>

<span data-ttu-id="43317-147">*Startup.cs* 에서 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-147">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="43317-148">`UseGoogleAuthentication`메서드의 메서드 호출을 `Configure` 다음으로 바꿉니다 `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="43317-149">`AddGoogle`메서드에서 메서드를 호출 합니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="43317-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="43317-150">Microsoft 계정 인증</span><span class="sxs-lookup"><span data-stu-id="43317-150">Microsoft Account authentication</span></span>

<span data-ttu-id="43317-151">Microsoft 계정 인증에 대 한 자세한 내용은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/14455)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43317-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="43317-152">*Startup.cs* 에서 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-152">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="43317-153">`UseMicrosoftAccountAuthentication`메서드의 메서드 호출을 `Configure` 다음으로 바꿉니다 `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="43317-154">`AddMicrosoftAccount`메서드에서 메서드를 호출 합니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="43317-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="43317-155">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="43317-155">Twitter authentication</span></span>

<span data-ttu-id="43317-156">*Startup.cs* 에서 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-156">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="43317-157">`UseTwitterAuthentication`메서드의 메서드 호출을 `Configure` 다음으로 바꿉니다 `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="43317-158">`AddTwitter`메서드에서 메서드를 호출 합니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="43317-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="43317-159">기본 인증 체계 설정</span><span class="sxs-lookup"><span data-stu-id="43317-159">Setting default authentication schemes</span></span>

<span data-ttu-id="43317-160">1.x에서 `AutomaticAuthenticate` `AutomaticChallenge` [authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) 기본 클래스의 및 속성은 단일 인증 체계에 설정 하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="43317-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="43317-161">이를 적용 하는 좋은 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="43317-162">2.0에서는 이러한 두 속성이 개별 인스턴스에서 속성으로 제거 되었습니다 `AuthenticationOptions` .</span><span class="sxs-lookup"><span data-stu-id="43317-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="43317-163">`AddAuthentication` `ConfigureServices` *Startup.cs* 의 메서드 내에서 메서드 호출을 통해 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs* :</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="43317-164">위의 코드 조각에서 기본 체계는 `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s")로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43317-164">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="43317-165">또는 오버 로드 된 버전의 메서드를 사용 하 여 둘 이상의 `AddAuthentication` 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="43317-166">다음의 오버 로드 된 메서드 예제에서 기본 체계는로 설정 됩니다 `CookieAuthenticationDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="43317-166">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="43317-167">개별 `[Authorize]` 특성 또는 권한 부여 정책 내에서 인증 체계를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="43317-168">다음 조건 중 하나에 해당 하는 경우 2.0에서 기본 스키마를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="43317-169">사용자에 게 자동으로 로그인 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="43317-170">`[Authorize]`스키마를 지정 하지 않고 특성 또는 권한 부여 정책을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="43317-171">이 규칙의 예외는 `AddIdentity` 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="43317-171">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="43317-172">이 메서드는를 추가 하 cookie 고 응용 프로그램에 기본 인증 및 챌린지 체계를 설정 합니다 cookie `IdentityConstants.ApplicationScheme` .</span><span class="sxs-lookup"><span data-stu-id="43317-172">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="43317-173">또한 기본 로그인 체계를 외부로 설정 합니다 cookie `IdentityConstants.ExternalScheme` .</span><span class="sxs-lookup"><span data-stu-id="43317-173">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="43317-174">HttpContext 인증 확장 프로그램 사용</span><span class="sxs-lookup"><span data-stu-id="43317-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="43317-175">인터페이스는 1.x `IAuthenticationManager` 인증 시스템의 기본 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="43317-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="43317-176">네임 스페이스의 새로운 확장 메서드 집합으로 대체 되었습니다 `HttpContext` `Microsoft.AspNetCore.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="43317-177">예를 들어 1. x 프로젝트는 속성을 참조 합니다 `Authentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="43317-178">2.0 프로젝트에서 `Microsoft.AspNetCore.Authentication` 네임 스페이스를 가져오고 속성 참조를 삭제 합니다 `Authentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="43317-179">Windows 인증 (HTTP.sys/IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="43317-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="43317-180">Windows 인증에는 두 가지 변형이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="43317-181">호스트는 인증 된 사용자만 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-181">The host only allows authenticated users.</span></span> <span data-ttu-id="43317-182">이러한 변형은 2.0 변경의 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="43317-183">호스트는 익명 및 인증 된 사용자를 모두 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="43317-184">이러한 변형은 2.0 변경의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="43317-185">예를 들어 앱은 [IIS](xref:host-and-deploy/iis/index) 또는 [HTTP.sys](xref:fundamentals/servers/httpsys) 계층에서 익명 사용자를 허용 하지만 컨트롤러 수준에서 사용자에 게 권한을 부여 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="43317-186">이 시나리오에서는 메서드의 기본 스키마를 설정 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="43317-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="43317-187">[AspNetCore 통합](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)의 경우 기본 체계를로 설정 합니다 `IISDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="43317-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="43317-188">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)의 경우 기본 체계를로 설정 합니다 `HttpSysDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="43317-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="43317-189">기본 체계를 설정 하지 않으면 권한 부여 (챌린지) 요청이 다음 예외와 함께 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="43317-190">`System.InvalidOperationException`: AuthenticationScheme이 지정 되지 않았으므로 DefaultChallengeScheme을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="43317-191">자세한 내용은 <xref:security/authentication/windowsauth>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="43317-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-cookie-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a><span data-ttu-id="43317-192">IdentityCookie옵션 인스턴스</span><span class="sxs-lookup"><span data-stu-id="43317-192">IdentityCookieOptions instances</span></span>

<span data-ttu-id="43317-193">2.0 변경의 부작용은 옵션 인스턴스 대신 명명 된 옵션을 사용 하는 것으로 전환 되는 것입니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="43317-193">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="43317-194">스키마 이름을 사용자 지정 하는 기능이 Identity cookie 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43317-194">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="43317-195">예를 들어 1. x 프로젝트는 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection) 을 사용 하 여 `IdentityCookieOptions` 매개 변수를 *AccountController.cs* 및 *ManageController.cs* 에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs* .</span></span> <span data-ttu-id="43317-196">외부 cookie 인증 체계는 제공 된 인스턴스에서 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-196">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="43317-197">앞에서 언급 한 생성자 삽입은 2.0 프로젝트에서 필요 `_externalCookieScheme` 하지 않으며 필드를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="43317-198">1.x 프로젝트는 다음과 같이 필드를 사용 `_externalCookieScheme` 했습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-198">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="43317-199">2.0 프로젝트에서 위의 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="43317-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="43317-200">`IdentityConstants.ExternalScheme`상수는 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-200">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="43317-201">`SignOutAsync`다음 네임 스페이스를 가져와서 새로 추가 된 호출을 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a><span data-ttu-id="43317-202">Identity사용자 POCO 탐색 속성 추가</span><span class="sxs-lookup"><span data-stu-id="43317-202">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="43317-203">기본 `IdentityUser` POCO (일반 이전 CLR 개체)의 Entity Framework (EF) 핵심 탐색 속성이 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-203">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="43317-204">1.x 프로젝트에서 이러한 속성을 사용 하는 경우 수동으로 2.0 프로젝트에 다시 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

<span data-ttu-id="43317-205">EF Core 마이그레이션을 실행 하는 경우 중복 된 외래 키를 방지 하려면 `IdentityDbContext` 호출 후 클래스의 메서드에 다음을 추가 합니다 `OnModelCreating` `base.OnModelCreating();` .</span><span class="sxs-lookup"><span data-stu-id="43317-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="43317-206">GetExternalAuthenticationSchemes 바꾸기</span><span class="sxs-lookup"><span data-stu-id="43317-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="43317-207">`GetExternalAuthenticationSchemes`비동기 버전을 위해 동기 메서드가 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="43317-208">1.x 프로젝트에는 *controller/ManageController* 에 다음 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43317-208">1.x projects have the following code in *Controllers/ManageController.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="43317-209">이 메서드는 *보기/계정/로그인* 에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43317-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="43317-210">2.0 프로젝트에서 메서드를 사용 <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="43317-211">*ManageController.cs* 의 변경 내용은 다음 코드와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="43317-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="43317-212">*Login. cshtml* 에서 `AuthenticationScheme` 루프에 액세스 된 속성이 `foreach` 로 변경 됩니다 `Name` .</span><span class="sxs-lookup"><span data-stu-id="43317-212">In *Login.cshtml* , the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="43317-213">ManageLoginsViewModel 속성 변경</span><span class="sxs-lookup"><span data-stu-id="43317-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="43317-214">`ManageLoginsViewModel` `ManageLogins` *ManageController.cs* 의 작업에 개체가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43317-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs* .</span></span> <span data-ttu-id="43317-215">1.x 프로젝트에서 개체의 `OtherLogins` 속성 반환 형식은 `IList<AuthenticationDescription>` 입니다.</span><span class="sxs-lookup"><span data-stu-id="43317-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="43317-216">이 반환 형식에는 다음과 같은 가져오기가 필요 합니다 `Microsoft.AspNetCore.Http.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="43317-217">2.0 프로젝트에서 반환 형식은로 변경 `IList<AuthenticationScheme>` 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43317-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="43317-218">이 새 반환 형식을 사용 하려면 가져오기를 `Microsoft.AspNetCore.Http.Authentication` 가져오기로 바꾸어야 합니다 `Microsoft.AspNetCore.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="43317-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="43317-219">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="43317-219">Additional resources</span></span>

<span data-ttu-id="43317-220">자세한 내용은 GitHub의 [Auth 2.0 문제에 대 한 설명을](https://github.com/aspnet/Security/issues/1338) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43317-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
