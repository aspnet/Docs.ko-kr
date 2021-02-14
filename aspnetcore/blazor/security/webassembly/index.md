---
title: ASP.NET Core Blazor WebAssembly 보호
author: guardrex
description: Blazor WebAssemlby 앱을 SPA(단일 페이지 애플리케이션)로 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: 48a6227c30f025e118782b13e46fe0c7d6505851
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280835"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a><span data-ttu-id="0b116-103">ASP.NET Core Blazor WebAssembly 보호</span><span class="sxs-lookup"><span data-stu-id="0b116-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="0b116-104">Blazor WebAssembly 앱은 SPA(단일 페이지 애플리케이션)와 동일한 방식으로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-104">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="0b116-105">사용자를 SPA에 인증하는 여러 가지 방법이 있지만, [OIDC(OpenID Connect)](https://openid.net/connect/)와 같은 [OAuth 2.0 프로토콜](https://oauth.net/) 기반의 구현을 사용하는 것이 가장 일반적이고 포괄적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-105">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="0b116-106">인증 라이브러리</span><span class="sxs-lookup"><span data-stu-id="0b116-106">Authentication library</span></span>

<span data-ttu-id="0b116-107">Blazor WebAssembly는 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 라이브러리를 통해 OIDC를 사용한 앱 인증 및 권한 부여를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-107">Blazor WebAssembly supports authenticating and authorizing apps using OIDC via the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library.</span></span> <span data-ttu-id="0b116-108">이 라이브러리는 ASP.NET Core 백 엔드에 대해 원활하게 인증하기 위한 기본 형식 집합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-108">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="0b116-109">이 라이브러리는 [Identity 서버](https://identityserver.io/) 위에 빌드된 API 권한 부여 지원과 ASP.NET Core Identity를 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-109">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="0b116-110">이 라이브러리는 OP(OpenID 공급자)라고 하는 OIDC를 지원하는 타사 Identity 공급자(IP)에 대해 인증할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-110">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="0b116-111">Blazor WebAssembly의 인증 지원은 기본 인증 프로토콜 세부 정보를 처리하는 데 사용되는 `oidc-client.js` 라이브러리를 기반으로 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-111">The authentication support in Blazor WebAssembly is built on top of the `oidc-client.js` library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="0b116-112">SameSite cookie를 사용하는 것과 같이 SPA를 인증하기 위한 다른 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-112">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="0b116-113">그러나 Blazor WebAssembly의 엔지니어링 설계에서는 OAuth 및 OIDC가 Blazor WebAssembly 앱의 인증을 위한 최고의 옵션으로 자리 잡았습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-113">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="0b116-114">[JWT(JSON Web Token)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 기반의 [토큰 기반 인증](xref:security/anti-request-forgery#token-based-authentication)이 기능 및 보안상의 이유로 [cookie 기반 인증](xref:security/anti-request-forgery#cookie-based-authentication) 대신 선택되었습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-114">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="0b116-115">일부 요청에서는 토큰이 전송되지 않으므로 토큰 기반 프로토콜을 사용하면 공격 노출 영역이 적어집니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-115">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="0b116-116">토큰은 명시적으로 전송되므로 [CSRF(교차 사이트 요청 위조)](xref:security/anti-request-forgery)로부터 서버 엔드포인트를 보호할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-116">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="0b116-117">따라서 Blazor WebAssembly 앱을 MVC 또는 Razor Pages 앱과 함께 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-117">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="0b116-118">토큰은 cookie보다 사용 권한이 더 좁습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-118">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="0b116-119">예를 들어 토큰을 사용하여 사용자 계정을 관리하거나 사용자 암호를 변경할 수 없습니다. 이러한 작업은 해당 기능이 명시적으로 구현된 경우에만 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-119">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="0b116-120">토큰은 수명이 짧아서 기본적으로 1시간이므로 공격 기간이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-120">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="0b116-121">또한 토큰은 언제든지 해지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-121">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="0b116-122">자체 포함 JWT는 클라이언트와 서버에 인증 프로세스를 보증합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-122">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="0b116-123">예를 들어 클라이언트에게는 수신한 토큰이 적합하고 주어진 인증 프로세스의 일부로 내보내 졌는지 검색하고 확인할 수단이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-123">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="0b116-124">타사가 인증 프로세스 중에 토큰을 바꾸려고 하는 경우 클라이언트는 바뀐 토큰을 감지하고 사용하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-124">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="0b116-125">OAuth 및 OIDC를 사용하는 토큰은 사용자 에이전트가 제대로 동작하지 않아도 앱의 보안을 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-125">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="0b116-126">OAuth 및 OIDC와 같은 토큰 기반 프로토콜을 사용하면 호스트형 앱과 독립 실행형 앱을 동일한 보안 특성 집합을 사용하여 인증하고 권한 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-126">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="0b116-127">OIDC를 사용하는 인증 프로세스</span><span class="sxs-lookup"><span data-stu-id="0b116-127">Authentication process with OIDC</span></span>

<span data-ttu-id="0b116-128">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 라이브러리는 OIDC를 사용하여 인증 및 권한 부여를 구현하는 여러 가지 기본 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-128">The [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="0b116-129">대체로 인증은 다음과 같이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-129">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="0b116-130">익명 사용자가 로그인 단추를 선택하거나 [`[Authorize]` 특성](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)이 적용된 페이지를 요청하면 앱의 로그인 페이지(`/authentication/login`)로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-130">When an anonymous user selects the login button or requests a page with the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="0b116-131">로그인 페이지에서 인증 라이브러리는 권한 부여 엔드포인트로의 리디렉션을 준비합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-131">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="0b116-132">권한 부여 엔드포인트는 Blazor WebAssembly 앱 외부에 있으며 별도의 원본에 호스트될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-132">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="0b116-133">엔드포인트는 사용자가 인증되었는지 여부를 확인하고 응답으로 하나 이상의 토큰을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-133">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="0b116-134">인증 라이브러리는 인증 응답을 받기 위한 로그인 콜백을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-134">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="0b116-135">사용자가 인증되지 않은 경우 사용자는 기본 인증 시스템으로 리디렉션되며 이는 일반적으로 ASP.NET Core Identity입니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-135">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="0b116-136">사용자가 이미 인증된 경우에는 권한 부여 엔드포인트에서 적절한 토큰을 생성하고 브라우저를 다시 로그인 콜백 엔드포인트(`/authentication/login-callback`)로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-136">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="0b116-137">Blazor WebAssembly 앱이 로그인 콜백 엔드포인트(`/authentication/login-callback`)를 로드하면 인증 응답이 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-137">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="0b116-138">인증 프로세스가 완료되면 사용자는 인증되고 필요에 따라 사용자가 요청한 원래의 보호된 URL로 다시 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-138">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="0b116-139">어떤 이유로든 인증 프로세스가 실패하는 경우 사용자는 로그인 실패 페이지(`/authentication/login-failed`)로 보내지고 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-139">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="authentication-component"></a><span data-ttu-id="0b116-140">`Authentication` 구성 요소</span><span class="sxs-lookup"><span data-stu-id="0b116-140">`Authentication` component</span></span>

<span data-ttu-id="0b116-141">`Authentication` 구성 요소(`Pages/Authentication.razor`)는 원격 인증 작업을 처리하고 앱이 다음 작업을 수행하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-141">The `Authentication` component (`Pages/Authentication.razor`) handles remote authentication operations and permits the app to:</span></span>

* <span data-ttu-id="0b116-142">인증 상태에 대한 앱 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-142">Configure app routes for authentication states.</span></span>
* <span data-ttu-id="0b116-143">인증 상태에 대한 UI 콘텐츠를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-143">Set UI content for authentication states.</span></span>
* <span data-ttu-id="0b116-144">인증 상태를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-144">Manage authentication state.</span></span>

<span data-ttu-id="0b116-145">사용자 등록 또는 로그인 같은 인증 작업은 인증 작업 간에 상태를 유지하고 제어하는, Blazor 프레임워크의 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> 구성 요소에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-145">Authentication actions, such as registering or signing in a user, are passed to the Blazor framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> component, which persists and controls state across authentication operations.</span></span>

<span data-ttu-id="0b116-146">자세한 내용과 예제를 보려면 <xref:blazor/security/webassembly/additional-scenarios>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0b116-146">For more information and examples, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

## <a name="authorization"></a><span data-ttu-id="0b116-147">권한 부여</span><span class="sxs-lookup"><span data-stu-id="0b116-147">Authorization</span></span>

<span data-ttu-id="0b116-148">Blazor WebAssembly 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 권한 부여 확인을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-148">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="0b116-149">JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-149">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="0b116-150">**항상 클라이언트 쪽 앱을 통해 액세스한 API 엔드포인트 내에서 서버의 권한 부여 확인을 수행합니다.**</span><span class="sxs-lookup"><span data-stu-id="0b116-150">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="require-authorization-for-the-entire-app"></a><span data-ttu-id="0b116-151">전체 앱에 대한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="0b116-151">Require authorization for the entire app</span></span>

<span data-ttu-id="0b116-152">다음 방법 중 하나를 사용하여 앱의 각 Razor 구성 요소에 [`[Authorize]` 특성](xref:blazor/security/index#authorize-attribute)([API 설명서](xref:System.Web.Mvc.AuthorizeAttribute))을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-152">Apply the [`[Authorize]` attribute](xref:blazor/security/index#authorize-attribute) ([API documentation](xref:System.Web.Mvc.AuthorizeAttribute)) to each Razor component of the app using one of the following approaches:</span></span>

* <span data-ttu-id="0b116-153">`_Imports.razor` 파일에 [`@attribute`](xref:mvc/views/razor#attribute) 지시문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-153">Use the [`@attribute`](xref:mvc/views/razor#attribute) directive in the `_Imports.razor` file:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* <span data-ttu-id="0b116-154">`Pages` 폴더의 각 Razor 구성 요소에 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-154">Add the attribute to each Razor component in the `Pages` folder.</span></span>

> [!NOTE]
> <span data-ttu-id="0b116-155"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A>를 사용하는 정책에 대한 <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> 설정은 지원되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="0b116-155">Setting an <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> to a policy with <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> is **not** supported.</span></span>

## <a name="refresh-tokens"></a><span data-ttu-id="0b116-156">새로 고침 토큰</span><span class="sxs-lookup"><span data-stu-id="0b116-156">Refresh tokens</span></span>

<span data-ttu-id="0b116-157">새로 고침 토큰은 Blazor WebAssembly 앱에서 클라이언트 쪽으로 보호할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-157">Refresh tokens can't be secured client-side in Blazor WebAssembly apps.</span></span> <span data-ttu-id="0b116-158">따라서 직접 사용하기 위해 새로 고침 토큰이 앱에 전송되어서는 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-158">Therefore, refresh tokens shouldn't be sent to the app for direct use.</span></span>

<span data-ttu-id="0b116-159">새로 고침 토큰은 타사 API에 액세스하는 호스트된 Blazor WebAssembly 솔루션의 서버 쪽 앱에서 유지 관리하고 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-159">Refresh tokens can be maintained and used by the server-side app in a Hosted Blazor WebAssembly solution to access third-party APIs.</span></span> <span data-ttu-id="0b116-160">자세한 내용은 <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0b116-160">For more information, see <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span></span>

## <a name="establish-claims-for-users"></a><span data-ttu-id="0b116-161">사용자 클레임 설정</span><span class="sxs-lookup"><span data-stu-id="0b116-161">Establish claims for users</span></span>

<span data-ttu-id="0b116-162">앱에서는 종종 서버에 대한 웹 API 호출을 기반으로 사용자 클레임을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-162">Apps often require claims for users based on a web API call to a server.</span></span> <span data-ttu-id="0b116-163">예를 들어 클레임은 앱에서 [권한 부여를 설정](xref:blazor/security/index#authorization)하는 데 자주 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-163">For example, claims are frequently used to [establish authorization](xref:blazor/security/index#authorization) in an app.</span></span> <span data-ttu-id="0b116-164">이러한 시나리오에서 앱은 서비스에 액세스하기 위해 액세스 토큰을 요청하고 이 토큰을 사용하여 클레임을 위한 사용자 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-164">In these scenarios, the app requests an access token to access the service and uses the token to obtain the user data for the claims.</span></span> <span data-ttu-id="0b116-165">예제를 보려면 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0b116-165">For examples, see the following resources:</span></span>

* [<span data-ttu-id="0b116-166">추가 시나리오: 사용자의 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="0b116-166">Additional scenarios: Customize the user</span></span>](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="0b116-167">Identity Server를 사용한 Azure App Service on Linux</span><span class="sxs-lookup"><span data-stu-id="0b116-167">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="0b116-168">Identity Server를 사용하여 Azure App Service on Linux에 배포할 때 발급자를 명시적으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-168">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="0b116-169">자세한 내용은 <xref:security/authentication/identity/spa#azure-app-service-on-linux>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0b116-169">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="windows-authentication"></a><span data-ttu-id="0b116-170">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="0b116-170">Windows Authentication</span></span>

<span data-ttu-id="0b116-171">Blazor Webassembly 또는 다른 모든 SPA 프레임워크에서는 Windows 인증을 사용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-171">We don't recommend using Windows Authentication with Blazor Webassembly or with any other SPA framework.</span></span> <span data-ttu-id="0b116-172">Windows 인증 대신 ADFS(Active Directory Federation Services)를 사용하는 OIDC 같은 토큰 기반 프로토콜을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-172">We recommend using token-based protocols instead of Windows Authentication, such as OIDC with Active Directory Federation Services (ADFS).</span></span>

<span data-ttu-id="0b116-173">Blazor Webassembly 또는 다른 모든 SPA 프레임워크에서 Windows 인증을 사용하는 경우 CSRF(교차 사이트 요청 위조) 토큰으로부터 앱을 보호하려면 추가적인 방법이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-173">If Windows Authentication is used with Blazor Webassembly or with any other SPA framework, additional measures are required to protect the app from cross-site request forgery (CSRF) tokens.</span></span> <span data-ttu-id="0b116-174">Windows 인증에서 원본 간에 인증 컨텍스트를 공유하지 못하도록 하는 메커니즘을 제공하지 않을 뿐만 아니라 cookie에 적용되는 것과 동일한 문제가 Windows 인증에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-174">The same concerns that apply to cookies apply to Windows Authentication with the addition that Windows Authentication doesn't offer any mechanism to prevent sharing of the authentication context across origins.</span></span> <span data-ttu-id="0b116-175">CSRF로부터 보호하는 추가 방법 없이 Windows 인증을 사용하는 앱은 최소한 조직의 인트라넷으로 제한되어야 하며 인터넷에서는 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-175">Apps using Windows Authentication without additional protection from CSRF should at least be restricted to an organization's intranet and not be used on the Internet.</span></span>

<span data-ttu-id="0b116-176">자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0b116-176">For for information, see <xref:security/anti-request-forgery>.</span></span>

## <a name="implementation-guidance"></a><span data-ttu-id="0b116-177">구현 지침</span><span class="sxs-lookup"><span data-stu-id="0b116-177">Implementation guidance</span></span>

<span data-ttu-id="0b116-178">이 개요의 문서에서는 Blazor WebAssembly 앱에서 특정 공급자에 대해 사용자를 인증하는 방법을 안내합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-178">Articles under this *Overview* provide information on authenticating users in Blazor WebAssembly apps against specific providers.</span></span>

<span data-ttu-id="0b116-179">독립 실행형 Blazor WebAssembly 앱:</span><span class="sxs-lookup"><span data-stu-id="0b116-179">Standalone Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="0b116-180">OIDC 공급자 및 WebAssembly 인증 라이브러리에 대한 일반 지침</span><span class="sxs-lookup"><span data-stu-id="0b116-180">General guidance for OIDC providers and the WebAssembly Authentication Library</span></span>](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [<span data-ttu-id="0b116-181">Microsoft 계정</span><span class="sxs-lookup"><span data-stu-id="0b116-181">Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="0b116-182">AAD(Azure Active Directory)</span><span class="sxs-lookup"><span data-stu-id="0b116-182">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="0b116-183">AAD(Azure Active Directory) B2C</span><span class="sxs-lookup"><span data-stu-id="0b116-183">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

<span data-ttu-id="0b116-184">호스트된 Blazor WebAssembly 앱:</span><span class="sxs-lookup"><span data-stu-id="0b116-184">Hosted Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="0b116-185">AAD(Azure Active Directory)</span><span class="sxs-lookup"><span data-stu-id="0b116-185">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [<span data-ttu-id="0b116-186">AAD(Azure Active Directory) B2C</span><span class="sxs-lookup"><span data-stu-id="0b116-186">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [<span data-ttu-id="0b116-187">Identity서버</span><span class="sxs-lookup"><span data-stu-id="0b116-187">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0b116-188">추가 구성 지침은 다음 문서에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-188">Further configuration guidance is found in the following articles:</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="0b116-189">추가 구성 지침은 <xref:blazor/security/webassembly/additional-scenarios>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0b116-189">For further configuration guidance, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0b116-190">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="0b116-190">Additional resources</span></span>

* <span data-ttu-id="0b116-191"><xref:host-and-deploy/proxy-load-balancer>: 다음 사항에 대한 지침을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0b116-191"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="0b116-192">전달된 헤더 미들웨어를 사용하여 프록시 서버와 내부 네트워크에서 HTTPS 체계 정보 유지.</span><span class="sxs-lookup"><span data-stu-id="0b116-192">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="0b116-193">수동 체계 구성, 올바른 요청 라우팅에 대한 요청 경로 변경, Linux 및 비 IIS 역방향 프록시에 대한 요청 체계 전달을 포함한 추가 시나리오 및 사용 사례.</span><span class="sxs-lookup"><span data-stu-id="0b116-193">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
