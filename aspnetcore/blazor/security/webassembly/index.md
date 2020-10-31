---
title: 'ASP.NET Core :::no-loc(Blazor WebAssembly)::: 보호'
author: guardrex
description: ':::no-loc(Blazor)::: WebAssemlby 앱을 SPA(단일 페이지 애플리케이션)로 보호하는 방법을 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: fc6fa075faa4fed1c2bf938d82c4dbfe631c31d3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055069"
---
# <a name="secure-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="48e89-103">ASP.NET Core :::no-loc(Blazor WebAssembly)::: 보호</span><span class="sxs-lookup"><span data-stu-id="48e89-103">Secure ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="48e89-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="48e89-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="48e89-105">:::no-loc(Blazor WebAssembly)::: 앱은 SPA(단일 페이지 애플리케이션)와 동일한 방식으로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-105">:::no-loc(Blazor WebAssembly)::: apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="48e89-106">사용자를 SPA에 인증하는 여러 가지 방법이 있지만, [OIDC(OpenID Connect)](https://openid.net/connect/)와 같은 [OAuth 2.0 프로토콜](https://oauth.net/) 기반의 구현을 사용하는 것이 가장 일반적이고 포괄적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="48e89-107">인증 라이브러리</span><span class="sxs-lookup"><span data-stu-id="48e89-107">Authentication library</span></span>

<span data-ttu-id="48e89-108">:::no-loc(Blazor WebAssembly):::는 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 라이브러리를 통해 OIDC를 사용한 앱 인증 및 권한 부여를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-108">:::no-loc(Blazor WebAssembly)::: supports authenticating and authorizing apps using OIDC via the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library.</span></span> <span data-ttu-id="48e89-109">이 라이브러리는 ASP.NET Core 백 엔드에 대해 원활하게 인증하기 위한 기본 형식 집합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="48e89-110">이 라이브러리는 [:::no-loc(Identity)::: 서버](https://identityserver.io/) 위에 빌드된 API 권한 부여 지원과 :::no-loc(ASP.NET Core Identity):::를 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-110">The library integrates :::no-loc(ASP.NET Core Identity)::: with API authorization support built on top of [:::no-loc(Identity)::: Server](https://identityserver.io/).</span></span> <span data-ttu-id="48e89-111">이 라이브러리는 OP(OpenID 공급자)라고 하는 OIDC를 지원하는 타사 :::no-loc(Identity)::: 공급자(IP)에 대해 인증할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-111">The library can authenticate against any third-party :::no-loc(Identity)::: Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="48e89-112">:::no-loc(Blazor WebAssembly):::의 인증 지원은 기본 인증 프로토콜 세부 정보를 처리하는 데 사용되는 `oidc-client.js` 라이브러리를 기반으로 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-112">The authentication support in :::no-loc(Blazor WebAssembly)::: is built on top of the `oidc-client.js` library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="48e89-113">SameSite :::no-loc(cookie):::를 사용하는 것과 같이 SPA를 인증하기 위한 다른 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-113">Other options for authenticating SPAs exist, such as the use of SameSite :::no-loc(cookie):::s.</span></span> <span data-ttu-id="48e89-114">그러나 :::no-loc(Blazor WebAssembly):::의 엔지니어링 설계에서는 OAuth 및 OIDC가 :::no-loc(Blazor WebAssembly)::: 앱의 인증을 위한 최고의 옵션으로 자리 잡았습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-114">However, the engineering design of :::no-loc(Blazor WebAssembly)::: is settled on OAuth and OIDC as the best option for authentication in :::no-loc(Blazor WebAssembly)::: apps.</span></span> <span data-ttu-id="48e89-115">[JWT(JSON Web Token)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 기반의 [토큰 기반 인증](xref:security/anti-request-forgery#token-based-authentication)이 기능 및 보안상의 이유로 [:::no-loc(cookie)::: 기반 인증](xref:security/anti-request-forgery#:::no-loc(cookie):::-based-authentication) 대신 선택되었습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [:::no-loc(cookie):::-based authentication](xref:security/anti-request-forgery#:::no-loc(cookie):::-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="48e89-116">일부 요청에서는 토큰이 전송되지 않으므로 토큰 기반 프로토콜을 사용하면 공격 노출 영역이 적어집니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="48e89-117">토큰은 명시적으로 전송되므로 [CSRF(교차 사이트 요청 위조)](xref:security/anti-request-forgery)로부터 서버 엔드포인트를 보호할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="48e89-118">따라서 :::no-loc(Blazor WebAssembly)::: 앱을 MVC 또는 :::no-loc(Razor)::: Pages 앱과 함께 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-118">This allows you to host :::no-loc(Blazor WebAssembly)::: apps alongside MVC or :::no-loc(Razor)::: pages apps.</span></span>
* <span data-ttu-id="48e89-119">토큰은 :::no-loc(cookie):::보다 사용 권한이 더 좁습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-119">Tokens have narrower permissions than :::no-loc(cookie):::s.</span></span> <span data-ttu-id="48e89-120">예를 들어 토큰을 사용하여 사용자 계정을 관리하거나 사용자 암호를 변경할 수 없습니다. 이러한 작업은 해당 기능이 명시적으로 구현된 경우에만 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="48e89-121">토큰은 수명이 짧아서 기본적으로 1시간이므로 공격 기간이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="48e89-122">또한 토큰은 언제든지 해지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="48e89-123">자체 포함 JWT는 클라이언트와 서버에 인증 프로세스를 보증합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="48e89-124">예를 들어 클라이언트에게는 수신한 토큰이 적합하고 주어진 인증 프로세스의 일부로 내보내 졌는지 검색하고 확인할 수단이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="48e89-125">타사가 인증 프로세스 중에 토큰을 바꾸려고 하는 경우 클라이언트는 바뀐 토큰을 감지하고 사용하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="48e89-126">OAuth 및 OIDC를 사용하는 토큰은 사용자 에이전트가 제대로 동작하지 않아도 앱의 보안을 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="48e89-127">OAuth 및 OIDC와 같은 토큰 기반 프로토콜을 사용하면 호스트형 앱과 독립 실행형 앱을 동일한 보안 특성 집합을 사용하여 인증하고 권한 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="48e89-128">OIDC를 사용하는 인증 프로세스</span><span class="sxs-lookup"><span data-stu-id="48e89-128">Authentication process with OIDC</span></span>

<span data-ttu-id="48e89-129">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 라이브러리는 OIDC를 사용하여 인증 및 권한 부여를 구현하는 여러 가지 기본 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-129">The [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="48e89-130">대체로 인증은 다음과 같이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="48e89-131">익명 사용자가 로그인 단추를 선택하거나 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성이 적용된 페이지를 요청하면 앱의 로그인 페이지(`/authentication/login`)로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="48e89-132">로그인 페이지에서 인증 라이브러리는 권한 부여 엔드포인트로의 리디렉션을 준비합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="48e89-133">권한 부여 엔드포인트는 :::no-loc(Blazor WebAssembly)::: 앱 외부에 있으며 별도의 원본에 호스트될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-133">The authorization endpoint is outside of the :::no-loc(Blazor WebAssembly)::: app and can be hosted at a separate origin.</span></span> <span data-ttu-id="48e89-134">엔드포인트는 사용자가 인증되었는지 여부를 확인하고 응답으로 하나 이상의 토큰을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="48e89-135">인증 라이브러리는 인증 응답을 받기 위한 로그인 콜백을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="48e89-136">사용자가 인증되지 않은 경우 사용자는 기본 인증 시스템으로 리디렉션되며 이는 일반적으로 :::no-loc(ASP.NET Core Identity):::입니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually :::no-loc(ASP.NET Core Identity):::.</span></span>
  * <span data-ttu-id="48e89-137">사용자가 이미 인증된 경우에는 권한 부여 엔드포인트에서 적절한 토큰을 생성하고 브라우저를 다시 로그인 콜백 엔드포인트(`/authentication/login-callback`)로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="48e89-138">:::no-loc(Blazor WebAssembly)::: 앱이 로그인 콜백 엔드포인트(`/authentication/login-callback`)를 로드하면 인증 응답이 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-138">When the :::no-loc(Blazor WebAssembly)::: app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="48e89-139">인증 프로세스가 완료되면 사용자는 인증되고 필요에 따라 사용자가 요청한 원래의 보호된 URL로 다시 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="48e89-140">어떤 이유로든 인증 프로세스가 실패하는 경우 사용자는 로그인 실패 페이지(`/authentication/login-failed`)로 보내지고 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="authentication-component"></a><span data-ttu-id="48e89-141">`Authentication` 구성 요소</span><span class="sxs-lookup"><span data-stu-id="48e89-141">`Authentication` component</span></span>

<span data-ttu-id="48e89-142">`Authentication` 구성 요소(`Pages/Authentication.razor`)는 원격 인증 작업을 처리하고 앱이 다음 작업을 수행하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-142">The `Authentication` component (`Pages/Authentication.razor`) handles remote authentication operations and permits the app to:</span></span>

* <span data-ttu-id="48e89-143">인증 상태에 대한 앱 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-143">Configure app routes for authentication states.</span></span>
* <span data-ttu-id="48e89-144">인증 상태에 대한 UI 콘텐츠를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-144">Set UI content for authentication states.</span></span>
* <span data-ttu-id="48e89-145">인증 상태를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-145">Manage authentication state.</span></span>

<span data-ttu-id="48e89-146">사용자 등록 또는 로그인 같은 인증 작업은 인증 작업 간에 상태를 유지하고 제어하는, :::no-loc(Blazor)::: 프레임워크의 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> 구성 요소에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-146">Authentication actions, such as registering or signing in a user, are passed to the :::no-loc(Blazor)::: framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> component, which persists and controls state across authentication operations.</span></span>

<span data-ttu-id="48e89-147">자세한 내용과 예제를 보려면 <xref:blazor/security/webassembly/additional-scenarios>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="48e89-147">For more information and examples, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

## <a name="authorization"></a><span data-ttu-id="48e89-148">권한 부여</span><span class="sxs-lookup"><span data-stu-id="48e89-148">Authorization</span></span>

<span data-ttu-id="48e89-149">:::no-loc(Blazor WebAssembly)::: 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 권한 부여 확인을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-149">In :::no-loc(Blazor WebAssembly)::: apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="48e89-150">JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="48e89-151">**항상 클라이언트 쪽 앱을 통해 액세스한 API 엔드포인트 내에서 서버의 권한 부여 확인을 수행합니다.**</span><span class="sxs-lookup"><span data-stu-id="48e89-151">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="require-authorization-for-the-entire-app"></a><span data-ttu-id="48e89-152">전체 앱에 대한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="48e89-152">Require authorization for the entire app</span></span>

<span data-ttu-id="48e89-153">다음 방법 중 하나를 사용하여 앱의 각 :::no-loc(Razor)::: 구성 요소에 [`[Authorize]` 특성](xref:blazor/security/index#authorize-attribute)([API 설명서](xref:System.Web.Mvc.AuthorizeAttribute))을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-153">Apply the [`[Authorize]` attribute](xref:blazor/security/index#authorize-attribute) ([API documentation](xref:System.Web.Mvc.AuthorizeAttribute)) to each :::no-loc(Razor)::: component of the app using one of the following approaches:</span></span>

* <span data-ttu-id="48e89-154">`_Imports.razor` 파일에 [`@attribute`](xref:mvc/views/razor#attribute) 지시문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-154">Use the [`@attribute`](xref:mvc/views/razor#attribute) directive in the `_Imports.razor` file:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* <span data-ttu-id="48e89-155">`Pages` 폴더의 각 :::no-loc(Razor)::: 구성 요소에 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-155">Add the attribute to each :::no-loc(Razor)::: component in the `Pages` folder.</span></span>

> [!NOTE]
> <span data-ttu-id="48e89-156"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A>를 사용하는 정책에 대한 <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> 설정은 지원되지 **않습니다** .</span><span class="sxs-lookup"><span data-stu-id="48e89-156">Setting an <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> to a policy with <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> is **not** supported.</span></span>

## <a name="refresh-tokens"></a><span data-ttu-id="48e89-157">새로 고침 토큰</span><span class="sxs-lookup"><span data-stu-id="48e89-157">Refresh tokens</span></span>

<span data-ttu-id="48e89-158">새로 고침 토큰은 :::no-loc(Blazor WebAssembly)::: 앱에서 클라이언트 쪽으로 보호할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-158">Refresh tokens can't be secured client-side in :::no-loc(Blazor WebAssembly)::: apps.</span></span> <span data-ttu-id="48e89-159">따라서 직접 사용하기 위해 새로 고침 토큰이 앱에 전송되어서는 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-159">Therefore, refresh tokens shouldn't be sent to the app for direct use.</span></span>

<span data-ttu-id="48e89-160">새로 고침 토큰은 타사 API에 액세스하는 호스트된 :::no-loc(Blazor WebAssembly)::: 솔루션의 서버 쪽 앱에서 유지 관리하고 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-160">Refresh tokens can be maintained and used by the server-side app in a Hosted :::no-loc(Blazor WebAssembly)::: solution to access third-party APIs.</span></span> <span data-ttu-id="48e89-161">자세한 내용은 <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48e89-161">For more information, see <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span></span>

## <a name="establish-claims-for-users"></a><span data-ttu-id="48e89-162">사용자 클레임 설정</span><span class="sxs-lookup"><span data-stu-id="48e89-162">Establish claims for users</span></span>

<span data-ttu-id="48e89-163">앱에서는 종종 서버에 대한 웹 API 호출을 기반으로 사용자 클레임을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-163">Apps often require claims for users based on a web API call to a server.</span></span> <span data-ttu-id="48e89-164">예를 들어 클레임은 앱에서 [권한 부여를 설정](xref:blazor/security/index#authorization)하는 데 자주 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-164">For example, claims are frequently used to [establish authorization](xref:blazor/security/index#authorization) in an app.</span></span> <span data-ttu-id="48e89-165">이러한 시나리오에서 앱은 서비스에 액세스하기 위해 액세스 토큰을 요청하고 이 토큰을 사용하여 클레임을 위한 사용자 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-165">In these scenarios, the app requests an access token to access the service and uses the token to obtain the user data for the claims.</span></span> <span data-ttu-id="48e89-166">예제를 보려면 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48e89-166">For examples, see the following resources:</span></span>

* [<span data-ttu-id="48e89-167">추가 시나리오: 사용자의 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="48e89-167">Additional scenarios: Customize the user</span></span>](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="implementation-guidance"></a><span data-ttu-id="48e89-168">구현 지침</span><span class="sxs-lookup"><span data-stu-id="48e89-168">Implementation guidance</span></span>

<span data-ttu-id="48e89-169">이 개요의 문서에서는 :::no-loc(Blazor WebAssembly)::: 앱에서 특정 공급자에 대해 사용자를 인증하는 방법을 안내합니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-169">Articles under this *Overview* provide information on authenticating users in :::no-loc(Blazor WebAssembly)::: apps against specific providers.</span></span>

<span data-ttu-id="48e89-170">독립 실행형 :::no-loc(Blazor WebAssembly)::: 앱:</span><span class="sxs-lookup"><span data-stu-id="48e89-170">Standalone :::no-loc(Blazor WebAssembly)::: apps:</span></span>

* [<span data-ttu-id="48e89-171">OIDC 공급자 및 WebAssembly 인증 라이브러리에 대한 일반 지침</span><span class="sxs-lookup"><span data-stu-id="48e89-171">General guidance for OIDC providers and the WebAssembly Authentication Library</span></span>](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [<span data-ttu-id="48e89-172">Microsoft 계정</span><span class="sxs-lookup"><span data-stu-id="48e89-172">Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="48e89-173">AAD(Azure Active Directory)</span><span class="sxs-lookup"><span data-stu-id="48e89-173">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="48e89-174">AAD(Azure Active Directory) B2C</span><span class="sxs-lookup"><span data-stu-id="48e89-174">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

<span data-ttu-id="48e89-175">호스트된 :::no-loc(Blazor WebAssembly)::: 앱:</span><span class="sxs-lookup"><span data-stu-id="48e89-175">Hosted :::no-loc(Blazor WebAssembly)::: apps:</span></span>

* [<span data-ttu-id="48e89-176">AAD(Azure Active Directory)</span><span class="sxs-lookup"><span data-stu-id="48e89-176">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [<span data-ttu-id="48e89-177">AAD(Azure Active Directory) B2C</span><span class="sxs-lookup"><span data-stu-id="48e89-177">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [<span data-ttu-id="48e89-178">:::no-loc(Identity):::서버</span><span class="sxs-lookup"><span data-stu-id="48e89-178">:::no-loc(Identity)::: Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="48e89-179">추가 구성 지침은 다음 문서에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48e89-179">Further configuration guidance is found in the following articles:</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="48e89-180">추가 구성 지침은 <xref:blazor/security/webassembly/additional-scenarios>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48e89-180">For further configuration guidance, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

::: moniker-end
