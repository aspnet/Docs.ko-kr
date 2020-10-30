---
title: ASP.NET Core에서 특정 체계를 사용 하 여 권한 부여
author: rick-anderson
description: 이 문서에서는 여러 인증 방법으로 작업할 때 id를 특정 체계로 제한 하는 방법을 설명 합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 4dc86480d40d8ee40b3c03aa7fd2994e6c15b105
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053126"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="c80b1-103">ASP.NET Core에서 특정 체계를 사용 하 여 권한 부여</span><span class="sxs-lookup"><span data-stu-id="c80b1-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="c80b1-104">SPAs (단일 페이지 응용 프로그램)와 같은 일부 시나리오에서는 여러 인증 방법을 사용 하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="c80b1-105">예를 들어 앱은 :::no-loc(cookie)::: 기반 인증을 사용 하 여 JavaScript 요청에 대 한 JWT 전달자 인증 및 로그인을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-105">For example, the app may use :::no-loc(cookie):::-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="c80b1-106">경우에 따라 앱에 인증 처리기의 인스턴스가 여러 개 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="c80b1-107">예를 들어, :::no-loc(cookie)::: 하나는 기본 id를 포함 하 고 다른 하나는 MFA (multi-factor authentication)가 트리거될 때 생성 되는 두 개의 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-107">For example, two :::no-loc(cookie)::: handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="c80b1-108">사용자가 추가 보안이 필요한 작업을 요청 하 여 MFA를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="c80b1-109">사용자가 MFA를 요구 하는 리소스를 요청할 때 MFA를 적용 하는 방법에 대 한 자세한 내용은 MFA를 사용 하 여 GitHub 문제 [보호 섹션](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c80b1-109">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="c80b1-110">인증 체계는 인증 중에 인증 서비스가 구성 될 때 이름이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="c80b1-111">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-111">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .Add:::no-loc(Cookie):::(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="c80b1-112">위의 코드에서 두 개의 인증 처리기가 추가 되었습니다. 하나는 하나이 :::no-loc(cookie)::: 고 다른 하나는 전달자 용입니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-112">In the preceding code, two authentication handlers have been added: one for :::no-loc(cookie):::s and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="c80b1-113">기본 스키마를 지정 하면 `HttpContext.User` 속성이 해당 id로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="c80b1-114">해당 동작이 필요 하지 않은 경우에는 매개 변수가 없는 형식을 호출 하 여 사용 하지 않도록 설정 `AddAuthentication` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="c80b1-115">권한 부여 특성이 있는 체계 선택</span><span class="sxs-lookup"><span data-stu-id="c80b1-115">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="c80b1-116">권한 부여 시점에서 앱은 사용할 처리기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-116">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="c80b1-117">쉼표로 구분 된 인증 체계 목록을에 전달 하 여 앱에 권한을 부여 하는 처리기를 선택 `[Authorize]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-117">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="c80b1-118">`[Authorize]`특성은 기본값이 구성 되어 있는지 여부에 관계 없이 사용할 인증 체계 또는 체계를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-118">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="c80b1-119">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-119">For example:</span></span>

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

<span data-ttu-id="c80b1-120">앞의 예제에서는 :::no-loc(cookie)::: 및 전달자 처리기를 실행 하 고 현재 사용자에 대 한 id를 만들고 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-120">In the preceding example, both the :::no-loc(cookie)::: and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="c80b1-121">단일 스키마만 지정 하면 해당 처리기가 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-121">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="c80b1-122">위의 코드에서는 "전달자" 체계가 있는 처리기만 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-122">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="c80b1-123">모든 :::no-loc(cookie)::: 기반 id는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-123">Any :::no-loc(cookie):::-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="c80b1-124">정책을 사용 하 여 체계 선택</span><span class="sxs-lookup"><span data-stu-id="c80b1-124">Selecting the scheme with policies</span></span>

<span data-ttu-id="c80b1-125">[정책](xref:security/authorization/policies)에서 원하는 스키마를 지정 하려는 경우 `AuthenticationSchemes` 정책을 추가할 때 컬렉션을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-125">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="c80b1-126">위의 예제에서 "Over18" 정책은 "전달자" 처리기에서 만든 id에 대해서만 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-126">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="c80b1-127">특성의 속성을 설정 하 여 정책을 사용 합니다 `[Authorize]` `Policy` .</span><span class="sxs-lookup"><span data-stu-id="c80b1-127">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="c80b1-128">여러 인증 체계 사용</span><span class="sxs-lookup"><span data-stu-id="c80b1-128">Use multiple authentication schemes</span></span>

<span data-ttu-id="c80b1-129">일부 앱은 여러 유형의 인증을 지원 해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-129">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="c80b1-130">예를 들어 앱이 사용자 데이터베이스에서 Azure Active Directory 사용자를 인증할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-130">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="c80b1-131">또 다른 예로 Active Directory Federation Services 및 Azure Active Directory B2C에서 사용자를 인증 하는 앱이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-131">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="c80b1-132">이 경우 앱은 여러 발급자의 JWT 전달자 토큰을 수락 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-132">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="c80b1-133">수락 하려는 모든 인증 스키마를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-133">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="c80b1-134">예를 들어의 다음 코드는 `Startup.ConfigureServices` 발급자가 서로 다른 두 개의 JWT 전달자 인증 체계를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-134">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> <span data-ttu-id="c80b1-135">기본 인증 체계에는 하나의 JWT 전달자 인증만 등록 됩니다 `JwtBearerDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="c80b1-135">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="c80b1-136">추가 인증은 고유한 인증 체계를 사용 하 여 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-136">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="c80b1-137">다음 단계는 두 인증 체계를 모두 허용 하도록 기본 권한 부여 정책을 업데이트 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-137">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="c80b1-138">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-138">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

<span data-ttu-id="c80b1-139">기본 권한 부여 정책이 재정의 되 면 `[Authorize]` 컨트롤러에서 특성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-139">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="c80b1-140">그러면 컨트롤러는 첫 번째 또는 두 번째 발급자가 발급 한 JWT를 사용 하 여 요청을 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="c80b1-140">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end
