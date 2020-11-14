---
title: ASP.NET Core 인증 개요
author: mjrousos
description: ASP.NET Core의 인증에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
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
uid: security/authentication/index
ms.openlocfilehash: e9e4ca11d20557666c75b84e56af825d002df0f1
ms.sourcegitcommit: fbd5427293d9ecccc388bd5fd305c2eb8ada7281
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94464005"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="f4868-103">ASP.NET Core 인증 개요</span><span class="sxs-lookup"><span data-stu-id="f4868-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="f4868-104">[Mike Rousos](https://github.com/mjrousos) 작성</span><span class="sxs-lookup"><span data-stu-id="f4868-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="f4868-105">인증은 사용자 ID를 확인하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="f4868-106">[권한 부여](xref:security/authorization/introduction)는 사용자에게 리소스에 대한 액세스 권한이 있는지를 확인하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="f4868-107">ASP.NET Core에서는 인증이 인증 [미들웨어](xref:fundamentals/middleware/index)에서 사용되는 `IAuthenticationService`을(를) 사용하여 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="f4868-108">인증 서비스는 등록된 인증 처리기를 사용하여 인증 관련 작업을 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="f4868-109">인증 관련 작업의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="f4868-110">사용자 인증.</span><span class="sxs-lookup"><span data-stu-id="f4868-110">Authenticating a user.</span></span>
* <span data-ttu-id="f4868-111">인증되지 않은 사용자가 제한된 리소스에 액세스하려고 할 때 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="f4868-112">등록된 인증 처리기와 해당 구성 옵션을 "체계"라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="f4868-113">인증 체계는 `Startup.ConfigureServices`에 인증 서비스를 등록하여 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="f4868-114">`services.AddAuthentication`(예: `AddJwtBearer` 또는 `AddCookie`)를 호출한 후에 스키마별 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="f4868-115">이 확장 메서드는 [AuthenticationBuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*)을 사용하여 적절한 설정으로 체계를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="f4868-116">드물게 [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*)을 직접 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="f4868-117">예를 들어, 다음 코드는 cookie 및 JWT 전달자 인증 체계의 인증 서비스와 처리기를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="f4868-118">`AddAuthentication` 매개 변수 `JwtBearerDefaults.AuthenticationScheme`는 특정 체계가 요청되지 않은 경우 기본적으로 사용할 체계의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="f4868-119">여러 스키마를 사용하는 경우, 권한 부여 정책(또는 권한 부여 특성)은 사용자를 인증하기 위해 사용해야 하는 [인증 체계(또는 체계)를 지정](xref:security/authorization/limitingidentitybyscheme)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="f4868-120">위의 예제에서, cookie 인증 체계는 이름을 지정하여 사용할 수 있습니다(`AddCookie`를 호출할 때 다른 이름을 제공할 수 있지만 기본적으로 `CookieAuthenticationDefaults.AuthenticationScheme`).</span><span class="sxs-lookup"><span data-stu-id="f4868-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="f4868-121">경우에 따라, `AddAuthentication`에 대한 호출은 다른 확장 메서드에서 자동으로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="f4868-122">예를 들어, [ASP.NET Core Identity](xref:security/authentication/identity)를 사용하는 경우 `AddAuthentication`이 내부적으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="f4868-123">인증 미들웨어는 앱의 `IApplicationBuilder`에서 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 확장 메서드를 호출하여 `Startup.Configure`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="f4868-124">`UseAuthentication`을(를) 호출하면 이전에 등록된 인증 체계를 사용하는 미들웨어가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="f4868-125">인증되는 사용자에 따라 달라지는 미들웨어 이전에 `UseAuthentication`을(를) 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="f4868-126">엔드포인트 라우팅을 사용하는 경우 `UseAuthentication`에 대한 호출이 다음과 같이 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="f4868-127">`UseRouting` 후에 이동하여 인증 결정에 경로 정보를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="f4868-128">`UseEndpoints` 전에 이동하여 사용자가 엔드포인트에 액세스하기 전에 인증됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="f4868-129">인증 개념</span><span class="sxs-lookup"><span data-stu-id="f4868-129">Authentication Concepts</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="f4868-130">인증 체계</span><span class="sxs-lookup"><span data-stu-id="f4868-130">Authentication scheme</span></span>

<span data-ttu-id="f4868-131">인증 체계는 다음에 해당하는 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-131">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="f4868-132">인증 처리기.</span><span class="sxs-lookup"><span data-stu-id="f4868-132">An authentication handler.</span></span>
* <span data-ttu-id="f4868-133">처리기의 특정 인스턴스를 구성하기 위한 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-133">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="f4868-134">체계는 연결된 처리기의 인증, 챌린지 및 금지 동작을 참조하기 위한 메커니즘으로 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-134">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="f4868-135">예를 들어, 권한 부여 정책은 체계 이름을 사용하여 사용자를 인증하는 데 사용해야 하는 권한 부여 체계(또는 체계)를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-135">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="f4868-136">인증을 구성할 때 기본 인증 체계를 지정하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-136">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="f4868-137">리소스에서 특정 체계를 요청하지 않는 한, 기본 체계가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-137">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="f4868-138">또한 다음과 같은 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-138">It's also possible to:</span></span>

* <span data-ttu-id="f4868-139">인증, 챌린지 및 금지 작업에 사용할 다른 기본 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-139">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="f4868-140">[정책 체계](xref:security/authentication/policyschemes)를 사용하여 여러 스키마를 하나로 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-140">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="f4868-141">인증 처리기</span><span class="sxs-lookup"><span data-stu-id="f4868-141">Authentication handler</span></span>

<span data-ttu-id="f4868-142">인증 처리기:</span><span class="sxs-lookup"><span data-stu-id="f4868-142">An authentication handler:</span></span>

* <span data-ttu-id="f4868-143">체계의 동작을 구현하는 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-143">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="f4868-144"><xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> 또는 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-144">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="f4868-145">사용자를 인증해야 하는 기본 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-145">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="f4868-146">인증 체계의 구성 및 들어오는 요청 컨텍스트를 기반으로 하는 인증 처리기:</span><span class="sxs-lookup"><span data-stu-id="f4868-146">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="f4868-147">인증에 성공하면 사용자 ID를 나타내는 <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> 개체를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-147">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="f4868-148">인증에 실패하면 '결과 없음' 또는 '실패'를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-148">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="f4868-149">사용자가 리소스에 액세스하려는 경우에 챌린지 및 금지 작업에 대한 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-149">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="f4868-150">액세스 권한이 없습니다(금지).</span><span class="sxs-lookup"><span data-stu-id="f4868-150">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="f4868-151">인증되지 않았습니다(챌린지).</span><span class="sxs-lookup"><span data-stu-id="f4868-151">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="f4868-152">Authenticate</span><span class="sxs-lookup"><span data-stu-id="f4868-152">Authenticate</span></span>

<span data-ttu-id="f4868-153">인증 체계의 인증 작업은 요청 컨텍스트를 기반으로 사용자의 id를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-153">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="f4868-154">인증에 성공했는지와 그런 경우 인증 티켓의 사용자 ID를 나타내는 <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult>을(를) 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-154">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="f4868-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f4868-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="f4868-156">인증 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-156">Authenticate examples include:</span></span>

* <span data-ttu-id="f4868-157">cookie에서 사용자 ID를 생성하는 cookie 인증 체계입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-157">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="f4868-158">JWT 전달자 체계는 JWT 전달자 토큰을 역직렬화하고 유효성을 검사하여 사용자 ID를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-158">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="f4868-159">과제</span><span class="sxs-lookup"><span data-stu-id="f4868-159">Challenge</span></span>

<span data-ttu-id="f4868-160">인증 챌린지는 인증되지 않은 사용자가 인증을 요구하는 엔드포인트를 요청하는 경우 권한 부여를 수행하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-160">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="f4868-161">인증 챌린지를 발행합니다(예를 들어, 익명 사용자가 제한된 리소스를 요청하거나 로그인 링크를 클릭하는 경우).</span><span class="sxs-lookup"><span data-stu-id="f4868-161">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="f4868-162">권한 부여는 지정된 인증 체계를 사용하여 챌린지를 호출하거나, 기본값(지정되지 않은 경우)을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-162">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="f4868-163"><xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f4868-163">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="f4868-164">인증 챌린지 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-164">Authentication challenge examples include:</span></span>

* <span data-ttu-id="f4868-165">사용자를 로그인 페이지로 리디렉션하는 cookie 인증 체계입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-165">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="f4868-166">`www-authenticate: bearer` 헤더를 사용하여 401 결과를 반환하는 JWT 전달자 체계입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-166">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="f4868-167">챌린지 작업을 통해 요청된 리소스에 액세스하는 데 사용할 인증 메커니즘을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-167">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="f4868-168">금지</span><span class="sxs-lookup"><span data-stu-id="f4868-168">Forbid</span></span>

<span data-ttu-id="f4868-169">인증된 사용자가 액세스를 허용하지 않는 리소스에 액세스를 시도할 때 인증 체계의 금지 작업이 권한 부여에 의해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-169">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="f4868-170"><xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f4868-170">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="f4868-171">인증 금지 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-171">Authentication forbid examples include:</span></span>
* <span data-ttu-id="f4868-172">액세스를 사용할 수 없음을 나타내는 페이지로 리디렉션하는 cookie 인증 체계입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-172">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="f4868-173">403 결과를 반환하는 JWT 전달자 체계입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-173">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="f4868-174">사용자가 리소스에 대한 액세스 권한을 요청할 수 있는 페이지로 리디렉션하는 사용자 지정 인증 체계입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-174">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="f4868-175">금지 작업을 통해 다음을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-175">A forbid action can let the user know:</span></span>

* <span data-ttu-id="f4868-176">인증되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-176">They are authenticated.</span></span>
* <span data-ttu-id="f4868-177">요청된 리소스에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-177">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="f4868-178">챌린지와 금지 간의 차이점에 대해서는 다음 링크를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f4868-178">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="f4868-179">[작업 리소스 처리기를 사용하는 챌린지 및 금지](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler)</span><span class="sxs-lookup"><span data-stu-id="f4868-179">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="f4868-180">[챌린지와 금지 간의 차이](xref:security/authorization/secure-data#challenge).</span><span class="sxs-lookup"><span data-stu-id="f4868-180">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="f4868-181">테넌트당 인증 공급자</span><span class="sxs-lookup"><span data-stu-id="f4868-181">Authentication providers per tenant</span></span>

<span data-ttu-id="f4868-182">ASP.NET Core 프레임워크에는 다중 테넌트 인증을 위한 기본 제공 솔루션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-182">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="f4868-183">고객이 기본 제공 기능을 사용하여 쓸 수 있는 것은 분명하지만, 이러한 목적을 위해 [Orchard Core](https://www.orchardcore.net/)를 조사하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-183">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="f4868-184">Orchard Core는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-184">Orchard Core is:</span></span>

* <span data-ttu-id="f4868-185">ASP.NET Core로 빌드된 오픈 소스 모듈형 및 다중 테넌트 앱 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-185">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="f4868-186">해당 앱 프레임워크 위에 빌드된 콘텐츠 관리 시스템(CMS)입니다.</span><span class="sxs-lookup"><span data-stu-id="f4868-186">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="f4868-187">테넌트당 인증 공급자의 예는 [Orchard Core](https://github.com/OrchardCMS/OrchardCore) 소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f4868-187">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f4868-188">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f4868-188">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [<span data-ttu-id="f4868-189">인증된 사용자가 전역적으로 필요함</span><span class="sxs-lookup"><span data-stu-id="f4868-189">Globally require authenticated users</span></span>](xref:security/authorization/secure-data#rau)
* [<span data-ttu-id="f4868-190">여러 인증 스키마 사용에 대한 GitHub 문제</span><span class="sxs-lookup"><span data-stu-id="f4868-190">GitHub issue on using multiple authentication schemes</span></span>](https://github.com/dotnet/aspnetcore/issues/26002)
