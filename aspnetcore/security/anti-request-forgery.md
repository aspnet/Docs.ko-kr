---
title: ASP.NET Core에서 교차 사이트 요청 위조 (XSRF/CSRF) 공격 방지
author: steve-smith
description: 악의적인 웹 사이트가 클라이언트 브라우저와 앱 간의 상호 작용에 영향을 줄 수 있는 웹 앱에 대 한 공격을 방지 하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 12/05/2019
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
uid: security/anti-request-forgery
ms.openlocfilehash: 197954965ee57b2a44ad0217d79ba142114e7df6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060848"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="49b26-103">ASP.NET Core에서 교차 사이트 요청 위조 (XSRF/CSRF) 공격 방지</span><span class="sxs-lookup"><span data-stu-id="49b26-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="49b26-104">작성자, [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="49b26-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="49b26-105">사이트 간 요청 위조 (XSRF 또는 CSRF 라고도 함)는 악의적인 웹 앱이 클라이언트 브라우저와 해당 브라우저를 신뢰 하는 웹 앱 간의 상호 작용에 영향을 줄 수 있는 웹 호스팅 앱에 대 한 공격입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="49b26-106">웹 브라우저에서 웹 사이트에 대 한 모든 요청과 함께 일부 형식의 인증 토큰을 자동으로 보내기 때문에 이러한 공격이 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="49b26-107">이러한 형태의 악용은 공격이 사용자의 이전에 인증 된 세션을 활용 하기 때문에 *한 번 클릭 공격* 또는 *세션 riding* 라고도 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="49b26-108">CSRF 공격의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="49b26-109">사용자가 `www.good-banking-site.com` 폼 인증을 사용 하 여에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="49b26-110">서버가 사용자를 인증 하 고 인증을 포함 하는 응답을 발급 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="49b26-110">The server authenticates the user and issues a response that includes an authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="49b26-111">사이트는 수신 하는 모든 요청을 유효한 인증으로 신뢰 하기 때문에 공격에 취약 :::no-loc(cookie)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication :::no-loc(cookie):::.</span></span>
1. <span data-ttu-id="49b26-112">사용자가 악의적인 사이트인를 방문 합니다 `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="49b26-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="49b26-113">악의적인 사이트인는 `www.bad-crook-site.com` 다음과 유사한 HTML 양식을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="49b26-114">양식이 `action` 악의적인 사이트가 아닌 취약 한 사이트에 게시 되는 것을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="49b26-115">CSRF의 "교차 사이트" 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="49b26-116">사용자가 제출 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-116">The user selects the submit button.</span></span> <span data-ttu-id="49b26-117">브라우저가 요청을 만들고 요청 된 :::no-loc(cookie)::: 도메인에 대 한 인증을 자동으로 포함 `www.good-banking-site.com` 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-117">The browser makes the request and automatically includes the authentication :::no-loc(cookie)::: for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="49b26-118">요청은 `www.good-banking-site.com` 사용자의 인증 컨텍스트를 사용 하 여 서버에서 실행 되며 인증 된 사용자가 수행할 수 있는 모든 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="49b26-119">사용자가 폼을 제출 하는 단추를 선택 하는 시나리오 외에도 악의적인 사이트는 다음과 같은 일을 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="49b26-120">양식을 자동으로 전송 하는 스크립트를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="49b26-121">양식 제출을 AJAX 요청으로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="49b26-122">CSS를 사용 하 여 폼을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-122">Hide the form using CSS.</span></span>

<span data-ttu-id="49b26-123">이러한 대체 시나리오에는 처음에 악성 사이트를 방문 하는 것 외에 다른 작업 또는 사용자 입력이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="49b26-124">HTTPS를 사용 하는 경우 CSRF 공격을 방지 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="49b26-125">악의적인 사이트는 안전 하지 않은 요청을 보낼 수 있는 것과 같은 방법으로 요청을 쉽게 보낼 수 있습니다 `https://www.good-banking-site.com/` .</span><span class="sxs-lookup"><span data-stu-id="49b26-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="49b26-126">일부 공격은 GET 요청에 응답 하는 끝점을 대상으로 하며,이 경우 이미지 태그를 사용 하 여 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="49b26-127">이러한 형태의 공격은 이미지를 허용 하지만 JavaScript를 차단 하는 포럼 사이트에서 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="49b26-128">변수 또는 리소스가 변경 되는 GET 요청에 대 한 상태를 변경 하는 앱은 악의적인 공격에 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="49b26-129">**변경 상태가 안전 하지 않은 가져오기 요청입니다. 모범 사례는 GET 요청에 대 한 상태를 변경 하지 않는 것입니다.**</span><span class="sxs-lookup"><span data-stu-id="49b26-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="49b26-130">CSRF 공격은 인증에 s를 사용 하는 웹 앱에 대해 :::no-loc(cookie)::: 다음과 같은 이유로 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-130">CSRF attacks are possible against web apps that use :::no-loc(cookie):::s for authentication because:</span></span>

* <span data-ttu-id="49b26-131">브라우저 :::no-loc(cookie)::: 는 웹 앱에 의해 발급 된를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-131">Browsers store :::no-loc(cookie):::s issued by a web app.</span></span>
* <span data-ttu-id="49b26-132">저장 된 :::no-loc(cookie)::: 에는 :::no-loc(cookie)::: 인증 된 사용자에 대 한 세션 s가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-132">Stored :::no-loc(cookie):::s include session :::no-loc(cookie):::s for authenticated users.</span></span>
* <span data-ttu-id="49b26-133">브라우저 :::no-loc(cookie)::: 는 브라우저에서 앱에 대 한 요청이 생성 된 방법에 관계 없이 모든 요청에 대해 도메인에 연결 된 모든를 웹 앱에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-133">Browsers send all of the :::no-loc(cookie):::s associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="49b26-134">그러나 CSRF 공격은 s를 악용 하는 것으로 제한 되지 않습니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="49b26-134">However, CSRF attacks aren't limited to exploiting :::no-loc(cookie):::s.</span></span> <span data-ttu-id="49b26-135">예를 들어, 기본 및 다이제스트 인증에도 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="49b26-136">사용자가 기본 또는 다이제스트 인증을 사용 하 여 로그인 한 후에는 세션이 종료 될 때까지 브라우저에서 자동으로 자격 증명을 보냅니다 &dagger; .</span><span class="sxs-lookup"><span data-stu-id="49b26-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="49b26-137">&dagger;이 컨텍스트에서 *세션* 은 사용자를 인증 하는 데 사용 되는 클라이언트 쪽 세션을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="49b26-138">서버 쪽 세션 또는 [ASP.NET Core 세션 미들웨어](xref:fundamentals/app-state)와 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="49b26-139">사용자는 예방 조치를 취하여 CSRF 취약성 으로부터 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="49b26-140">웹 앱 사용을 마치면 웹 앱에서 로그 오프 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="49b26-141">:::no-loc(cookie):::정기적으로 브라우저를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-141">Clear browser :::no-loc(cookie):::s periodically.</span></span>

<span data-ttu-id="49b26-142">그러나 CSRF 취약점은 기본적으로 최종 사용자가 아닌 웹 앱에 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="49b26-143">인증 기본 사항</span><span class="sxs-lookup"><span data-stu-id="49b26-143">Authentication fundamentals</span></span>

<span data-ttu-id="49b26-144">:::no-loc(Cookie):::기반 인증은 널리 사용 되는 인증 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-144">:::no-loc(Cookie):::-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="49b26-145">토큰 기반 인증 시스템은 특히 SPAs (단일 페이지 응용 프로그램)에 널리 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="49b26-146">:::no-loc(Cookie):::기반 인증</span><span class="sxs-lookup"><span data-stu-id="49b26-146">:::no-loc(Cookie):::-based authentication</span></span>

<span data-ttu-id="49b26-147">사용자가 사용자 이름 및 암호를 사용 하 여 인증 하는 경우 인증 및 권한 부여에 사용할 수 있는 인증 티켓이 포함 된 토큰이 발급 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="49b26-148">토큰은 :::no-loc(cookie)::: 클라이언트에서 수행 하는 모든 요청과 함께 제공 되는으로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-148">The token is stored as a :::no-loc(cookie)::: that accompanies every request the client makes.</span></span> <span data-ttu-id="49b26-149">이를 생성 하 고 유효성을 검사 하 :::no-loc(cookie)::: 는 것은 인증 미들웨어에 의해 수행 됩니다 :::no-loc(Cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="49b26-149">Generating and validating this :::no-loc(cookie)::: is performed by the :::no-loc(Cookie)::: Authentication Middleware.</span></span> <span data-ttu-id="49b26-150">[미들웨어](xref:fundamentals/middleware/index) 는 사용자 보안 주체를 암호화 된로 serialize 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="49b26-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted :::no-loc(cookie):::.</span></span> <span data-ttu-id="49b26-151">후속 요청에서 미들웨어는의 유효성을 검사 :::no-loc(cookie)::: 하 고, 보안 주체를 다시 만들고, 사용자를 [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)의 [사용자](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) 속성에 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-151">On subsequent requests, the middleware validates the :::no-loc(cookie):::, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="49b26-152">토큰 기반 인증</span><span class="sxs-lookup"><span data-stu-id="49b26-152">Token-based authentication</span></span>

<span data-ttu-id="49b26-153">사용자가 인증 되 면 토큰 (위조 방지 토큰이 아님)이 발급 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="49b26-154">토큰에는 [클레임](/dotnet/framework/security/claims-based-identity-model) 형식의 사용자 정보 또는 앱이 앱에서 유지 관리 되는 사용자 상태를 가리키는 참조 토큰이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="49b26-155">사용자가 인증을 요구 하는 리소스에 액세스 하려고 하면 토큰은 전달자 토큰의 형태로 추가 권한 부여 헤더를 사용 하 여 앱으로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="49b26-156">그러면 앱 상태 비저장이 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-156">This makes the app stateless.</span></span> <span data-ttu-id="49b26-157">각 후속 요청에서 토큰은 서버 쪽 유효성 검사에 대 한 요청에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="49b26-158">이 토큰은 *암호화* 되지 않습니다. *인코딩됩니다* .</span><span class="sxs-lookup"><span data-stu-id="49b26-158">This token isn't *encrypted* ; it's *encoded* .</span></span> <span data-ttu-id="49b26-159">서버에서 토큰은 정보에 액세스 하기 위해 디코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="49b26-160">후속 요청에서 토큰을 보내려면 브라우저의 로컬 저장소에 토큰을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="49b26-161">토큰이 브라우저의 로컬 저장소에 저장 된 경우 CSRF 취약성에 대해 걱정 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="49b26-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="49b26-162">CSRF는 토큰을에 저장 하는 경우에 문제가 됩니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="49b26-162">CSRF is a concern when the token is stored in a :::no-loc(cookie):::.</span></span> <span data-ttu-id="49b26-163">자세한 내용은 GitHub 문제 [SPA 코드 샘플 2 개 추가 :::no-loc(cookie)::: ](https://github.com/dotnet/AspNetCore.Docs/issues/13369)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="49b26-163">For more information, see the GitHub issue [SPA code sample adds two :::no-loc(cookie):::s](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="49b26-164">한 도메인에서 호스트 되는 여러 앱</span><span class="sxs-lookup"><span data-stu-id="49b26-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="49b26-165">공유 호스팅 환경은 세션 하이재킹, 로그인 CSRF 및 기타 공격에 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="49b26-166">`example1.contoso.net`및 `example2.contoso.net` 는 서로 다른 호스트 이지만 도메인의 호스트 간에 암시적 트러스트 관계가 있습니다 `*.contoso.net` .</span><span class="sxs-lookup"><span data-stu-id="49b26-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="49b26-167">이 암시적 트러스트 관계를 사용 하면 신뢰할 수 없는 호스트가 서로에 게 영향을 줄 수 있습니다 :::no-loc(cookie)::: . AJAX 요청을 제어 하는 동일한 원본 정책은 반드시 HTTP s에 적용 되는 것은 아닙니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="49b26-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's :::no-loc(cookie):::s (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP :::no-loc(cookie):::s).</span></span>

<span data-ttu-id="49b26-168">동일한 도메인에서 호스트 되는 앱 간에 트러스트 된를 활용 하는 공격은 :::no-loc(cookie)::: 도메인을 공유 하지 않도록 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-168">Attacks that exploit trusted :::no-loc(cookie):::s between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="49b26-169">각 앱이 자체 도메인에서 호스트 되는 경우에는 악용할 암시적 :::no-loc(cookie)::: 트러스트 관계가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-169">When each app is hosted on its own domain, there is no implicit :::no-loc(cookie)::: trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="49b26-170">위조 방지 구성 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49b26-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="49b26-171">ASP.NET Core은 [ASP.NET Core 데이터 보호](xref:security/data-protection/introduction)를 사용 하 여 위조 방지를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="49b26-172">서버 팜에서 작동 하도록 데이터 보호 스택을 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="49b26-173">자세한 내용은 [데이터 보호 구성](xref:security/data-protection/configuration/overview) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="49b26-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="49b26-174">에서 다음 Api 중 하나가 호출 될 때 위조 방지 미들웨어는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가 됩니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="49b26-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.Map:::no-loc(Razor):::Pages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="49b26-175">에서가 호출 되 면 위조 방지 미들웨어가 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가 됩니다. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="49b26-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="49b26-176">ASP.NET Core 2.0 이상에서 [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) 는 위조 방지 토큰을 HTML 양식 요소에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="49b26-177">파일의 다음 태그는 :::no-loc(Razor)::: 위조 방지 토큰을 자동으로 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-177">The following markup in a :::no-loc(Razor)::: file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="49b26-178">마찬가지로, [IHtmlHelper html.beginform](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) 는 폼의 메서드가 GET이 아닌 경우 기본적으로 위조 방지 토큰을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="49b26-179">HTML 양식 요소에 대 한 위조 방지 토큰 자동 생성은 태그에 `<form>` 특성이 포함 되어 있고 다음 중 하나에 해당 하는 경우에 발생 합니다 `method="post"` .</span><span class="sxs-lookup"><span data-stu-id="49b26-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="49b26-180">Action 특성이 비어 있는 경우 ( `action=""` )</span><span class="sxs-lookup"><span data-stu-id="49b26-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="49b26-181">Action 특성을 제공 하지 않습니다 ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="49b26-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="49b26-182">HTML 양식 요소에 대 한 위조 방지 토큰 자동 생성을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="49b26-183">특성을 사용 하 여 위조 방지 토큰을 명시적으로 사용 하지 않도록 설정 합니다 `asp-antiforgery` .</span><span class="sxs-lookup"><span data-stu-id="49b26-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="49b26-184">Form 요소는 태그 도우미 [! 옵트아웃 기호](xref:mvc/views/tag-helpers/intro#opt-out)를 사용 하 여 태그 도우미에서 옵트아웃 (opt out) 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="49b26-185">뷰에서를 제거 합니다 `FormTagHelper` .</span><span class="sxs-lookup"><span data-stu-id="49b26-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="49b26-186">뷰에 `FormTagHelper` 다음 지시문을 추가 하 여 뷰에서를 제거할 수 있습니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="49b26-186">The `FormTagHelper` can be removed from a view by adding the following directive to the :::no-loc(Razor)::: view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="49b26-187">[ :::no-loc(Razor)::: 페이지](xref:razor-pages/index) 는 XSRF/csrf에서 자동으로 보호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-187">[:::no-loc(Razor)::: Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="49b26-188">자세한 내용은 [XSRF/CSRF 및 :::no-loc(Razor)::: Pages](xref:razor-pages/index#xsrf)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="49b26-188">For more information, see [XSRF/CSRF and :::no-loc(Razor)::: Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="49b26-189">CSRF 공격 으로부터 보호 하는 가장 일반적인 방법은 STP ( *동기화 장치 토큰 패턴* )를 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="49b26-190">사용자가 양식 데이터를 사용 하 여 페이지를 요청 하면 STP가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="49b26-191">서버는 현재 사용자의 id와 연결 된 토큰을 클라이언트에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="49b26-192">클라이언트에서 확인을 위해 토큰을 서버에 다시 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="49b26-193">서버가 인증 된 사용자의 id와 일치 하지 않는 토큰을 받으면 요청이 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="49b26-194">토큰은 고유 하 고 예측할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="49b26-195">토큰을 사용 하 여 일련의 요청을 적절 하 게 시퀀싱 할 수도 있습니다. 예를 들어 1 페이지 > 페이지 2 > 페이지 3)에 대 한 요청 시퀀스를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="49b26-196">ASP.NET Core MVC 및 :::no-loc(Razor)::: 페이지 템플릿의 모든 양식은 위조 방지 토큰을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-196">All of the forms in ASP.NET Core MVC and :::no-loc(Razor)::: Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="49b26-197">다음 뷰 예제에서는 위조 방지 토큰을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="49b26-198">`<form>`HTML 도우미에서 태그 도우미를 사용 하지 않고 요소에 위조 방지 토큰을 명시적으로 추가 합니다 [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) .</span><span class="sxs-lookup"><span data-stu-id="49b26-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="49b26-199">위의 각 경우에 ASP.NET Core는 다음과 유사한 숨겨진 양식 필드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="49b26-200">위조 방지 토큰을 사용 하기 위한 세 가지 [필터](xref:mvc/controllers/filters) 를 포함 하는 ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="49b26-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="49b26-201">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="49b26-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="49b26-202">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="49b26-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="49b26-203">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="49b26-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="49b26-204">위조 방지 옵션</span><span class="sxs-lookup"><span data-stu-id="49b26-204">Antiforgery options</span></span>

<span data-ttu-id="49b26-205">[위조 방지 옵션](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) 사용자 지정 `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="49b26-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set :::no-loc(Cookie)::: properties using :::no-loc(Cookie):::Builder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="49b26-206">&dagger;`:::no-loc(Cookie):::` [ :::no-loc(Cookie)::: 작성기](/dotnet/api/microsoft.aspnetcore.http.:::no-loc(cookie):::builder) 클래스의 속성을 사용 하 여 위조 방지 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-206">&dagger;Set the antiforgery `:::no-loc(Cookie):::` properties using the properties of the [:::no-loc(Cookie):::Builder](/dotnet/api/microsoft.aspnetcore.http.:::no-loc(cookie):::builder) class.</span></span>

| <span data-ttu-id="49b26-207">옵션</span><span class="sxs-lookup"><span data-stu-id="49b26-207">Option</span></span> | <span data-ttu-id="49b26-208">설명</span><span class="sxs-lookup"><span data-stu-id="49b26-208">Description</span></span> |
| ------ | ----------- |
| [:::no-loc(Cookie):::](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::) | <span data-ttu-id="49b26-209">위조 방지를 만드는 데 사용 되는 설정을 결정 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="49b26-209">Determines the settings used to create the antiforgery :::no-loc(cookie):::s.</span></span> |
| [<span data-ttu-id="49b26-210">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="49b26-210">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="49b26-211">위조 방지 시스템이 뷰에서 위조 방지 토큰을 렌더링 하는 데 사용 하는 숨겨진 양식 필드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-211">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="49b26-212">HeaderName</span><span class="sxs-lookup"><span data-stu-id="49b26-212">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="49b26-213">위조 방지 시스템에서 사용 하는 헤더의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-213">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="49b26-214">이면 `null` 시스템은 폼 데이터만 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-214">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="49b26-215">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="49b26-215">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="49b26-216">헤더 생성을 표시 하지 않을 지 여부를 지정 합니다 `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="49b26-216">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="49b26-217">기본적으로 헤더는 "SAMEORIGIN" 값을 사용 하 여 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-217">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="49b26-218">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-218">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.:::no-loc(Cookie):::Domain = "contoso.com";
    options.:::no-loc(Cookie):::Name = "X-CSRF-TOKEN-COOKIENAME";
    options.:::no-loc(Cookie):::Path = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="49b26-219">옵션</span><span class="sxs-lookup"><span data-stu-id="49b26-219">Option</span></span> | <span data-ttu-id="49b26-220">설명</span><span class="sxs-lookup"><span data-stu-id="49b26-220">Description</span></span> |
| ------ | ----------- |
| [:::no-loc(Cookie):::](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::) | <span data-ttu-id="49b26-221">위조 방지를 만드는 데 사용 되는 설정을 결정 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="49b26-221">Determines the settings used to create the antiforgery :::no-loc(cookie):::s.</span></span> |
| <span data-ttu-id="49b26-222">[:::no-loc(Cookie):::도메인](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::domain)</span><span class="sxs-lookup"><span data-stu-id="49b26-222">[:::no-loc(Cookie):::Domain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::domain)</span></span> | <span data-ttu-id="49b26-223">의 도메인 :::no-loc(cookie)::: 입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-223">The domain of the :::no-loc(cookie):::.</span></span> <span data-ttu-id="49b26-224">기본값은 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-224">Defaults to `null`.</span></span> <span data-ttu-id="49b26-225">이 속성은 사용 되지 않으며 이후 버전에서 제거 될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-225">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="49b26-226">대신를 사용할 것을 권장 합니다 :::no-loc(Cookie)::: . 도메인.</span><span class="sxs-lookup"><span data-stu-id="49b26-226">The recommended alternative is :::no-loc(Cookie):::.Domain.</span></span> |
| <span data-ttu-id="49b26-227">[:::no-loc(Cookie):::Name](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::name)</span><span class="sxs-lookup"><span data-stu-id="49b26-227">[:::no-loc(Cookie):::Name](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::name)</span></span> | <span data-ttu-id="49b26-228">:::no-loc(cookie):::의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-228">The name of the :::no-loc(cookie):::.</span></span> <span data-ttu-id="49b26-229">설정 하지 않으면 시스템은 [기본 :::no-loc(Cookie)::: 접두사](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.default:::no-loc(cookie):::prefix) (")로 시작 하는 고유한 이름을 생성 합니다. AspNetCore. ").</span><span class="sxs-lookup"><span data-stu-id="49b26-229">If not set, the system generates a unique name beginning with the [Default:::no-loc(Cookie):::Prefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.default:::no-loc(cookie):::prefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="49b26-230">이 속성은 사용 되지 않으며 이후 버전에서 제거 될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-230">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="49b26-231">대신를 사용할 것을 권장 합니다 :::no-loc(Cookie)::: . 이름의.</span><span class="sxs-lookup"><span data-stu-id="49b26-231">The recommended alternative is :::no-loc(Cookie):::.Name.</span></span> |
| <span data-ttu-id="49b26-232">[:::no-loc(Cookie):::Path](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::path)</span><span class="sxs-lookup"><span data-stu-id="49b26-232">[:::no-loc(Cookie):::Path](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::path)</span></span> | <span data-ttu-id="49b26-233">에 설정 된 경로 :::no-loc(cookie)::: 입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-233">The path set on the :::no-loc(cookie):::.</span></span> <span data-ttu-id="49b26-234">이 속성은 사용 되지 않으며 이후 버전에서 제거 될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-234">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="49b26-235">대신를 사용할 것을 권장 합니다 :::no-loc(Cookie)::: . Path.</span><span class="sxs-lookup"><span data-stu-id="49b26-235">The recommended alternative is :::no-loc(Cookie):::.Path.</span></span> |
| [<span data-ttu-id="49b26-236">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="49b26-236">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="49b26-237">위조 방지 시스템이 뷰에서 위조 방지 토큰을 렌더링 하는 데 사용 하는 숨겨진 양식 필드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-237">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="49b26-238">HeaderName</span><span class="sxs-lookup"><span data-stu-id="49b26-238">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="49b26-239">위조 방지 시스템에서 사용 하는 헤더의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-239">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="49b26-240">이면 `null` 시스템은 폼 데이터만 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-240">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="49b26-241">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="49b26-241">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="49b26-242">위조 방지 시스템에 HTTPS가 필요한 지 여부를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-242">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="49b26-243">이면 `true` HTTPS가 아닌 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-243">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="49b26-244">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-244">Defaults to `false`.</span></span> <span data-ttu-id="49b26-245">이 속성은 사용 되지 않으며 이후 버전에서 제거 될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-245">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="49b26-246">대신를 설정 하는 것이 좋습니다 :::no-loc(Cookie)::: . SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="49b26-246">The recommended alternative is to set :::no-loc(Cookie):::.SecurePolicy.</span></span> |
| [<span data-ttu-id="49b26-247">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="49b26-247">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="49b26-248">헤더 생성을 표시 하지 않을 지 여부를 지정 합니다 `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="49b26-248">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="49b26-249">기본적으로 헤더는 "SAMEORIGIN" 값을 사용 하 여 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-249">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="49b26-250">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-250">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="49b26-251">자세한 내용은 [ :::no-loc(Cookie)::: authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="49b26-251">For more information, see [:::no-loc(Cookie):::AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="49b26-252">I위조 방지를 사용 하 여 위조 방지 기능 구성</span><span class="sxs-lookup"><span data-stu-id="49b26-252">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="49b26-253">[I방지 위조](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 는 위조 방지 기능을 구성 하는 API를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-253">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="49b26-254">`IAntiforgery` 클래스의 메서드에서 요청 될 수 있습니다 `Configure` `Startup` .</span><span class="sxs-lookup"><span data-stu-id="49b26-254">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="49b26-255">다음 예제에서는 앱의 홈페이지에서 미들웨어를 사용 하 여 위조 방지 토큰을 생성 하 고 응답에서로 보냅니다 :::no-loc(cookie)::: (이 항목의 뒷부분에서 설명 하는 기본 각도 명명 규칙 사용).</span><span class="sxs-lookup"><span data-stu-id="49b26-255">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a :::no-loc(cookie)::: (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable :::no-loc(cookie):::, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.:::no-loc(Cookie):::s.Append("XSRF-TOKEN", tokens.RequestToken, 
                new :::no-loc(Cookie):::Options() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="49b26-256">위조 방지 유효성 검사 필요</span><span class="sxs-lookup"><span data-stu-id="49b26-256">Require antiforgery validation</span></span>

<span data-ttu-id="49b26-257">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) 은 개별 작업, 컨트롤러 또는 전역에 적용 될 수 있는 작업 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-257">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="49b26-258">이 필터가 적용 된 작업에 대 한 요청은 유효한 위조 방지 토큰을 포함 하지 않는 한 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-258">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="49b26-259">특성에는 `ValidateAntiForgeryToken` HTTP GET 요청을 포함 하 여 표시 되는 작업 메서드에 대 한 요청 토큰이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-259">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="49b26-260">특성을 `ValidateAntiForgeryToken` 앱의 컨트롤러에서 적용 하는 경우 특성을 사용 하 여 재정의할 수 있습니다 `IgnoreAntiforgeryToken` .</span><span class="sxs-lookup"><span data-stu-id="49b26-260">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="49b26-261">ASP.NET Core는 위조 방지 토큰 추가를 지원 하지 않으므로 요청을 자동으로 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-261">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="49b26-262">안전 하지 않은 HTTP 메서드에 대 한 위조 방지 토큰의 유효성을 자동으로 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-262">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="49b26-263">ASP.NET Core 앱은 safe HTTP 메서드 (GET, HEAD, OPTIONS 및 TRACE)에 대해 위조 방지 토큰을 생성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-263">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="49b26-264">특성을 광범위 하 게 적용 한 `ValidateAntiForgeryToken` 다음 특성을 사용 하 여 재정의 하는 대신 `IgnoreAntiforgeryToken` [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) 특성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-264">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="49b26-265">이 특성은 특성에서 동일 하 게 작동 `ValidateAntiForgeryToken` 합니다. 단, 다음 HTTP 메서드를 사용 하 여 생성 된 요청에 대 한 토큰은 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-265">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="49b26-266">GET</span><span class="sxs-lookup"><span data-stu-id="49b26-266">GET</span></span>
* <span data-ttu-id="49b26-267">HEAD</span><span class="sxs-lookup"><span data-stu-id="49b26-267">HEAD</span></span>
* <span data-ttu-id="49b26-268">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="49b26-268">OPTIONS</span></span>
* <span data-ttu-id="49b26-269">TRACE</span><span class="sxs-lookup"><span data-stu-id="49b26-269">TRACE</span></span>

<span data-ttu-id="49b26-270">비 API 시나리오에는을 광범위 하 게 사용 하는 것이 좋습니다 `AutoValidateAntiforgeryToken` .</span><span class="sxs-lookup"><span data-stu-id="49b26-270">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="49b26-271">이렇게 하면 게시 작업은 기본적으로 보호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-271">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="49b26-272">대신, `ValidateAntiForgeryToken` 개별 작업 메서드에가 적용 되지 않는 한, 기본적으로 위조 방지 토큰을 무시 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-272">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="49b26-273">앱이 CSRF 공격에 취약 한 상태로 유지 하 여 POST 작업 메서드를 실수로 보호 되지 않은 상태로 남겨둘 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-273">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="49b26-274">모든 게시물은 위조 방지 토큰을 전송 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-274">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="49b26-275">Api에는 토큰의 일부가 아닌를 보내기 위한 자동 메커니즘이 없습니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="49b26-275">APIs don't have an automatic mechanism for sending the non-:::no-loc(cookie)::: part of the token.</span></span> <span data-ttu-id="49b26-276">구현은 클라이언트 코드 구현에 따라 달라질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-276">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="49b26-277">몇 가지 예가 아래에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-277">Some examples are shown below:</span></span>

<span data-ttu-id="49b26-278">클래스 수준 예제:</span><span class="sxs-lookup"><span data-stu-id="49b26-278">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="49b26-279">전역 예:</span><span class="sxs-lookup"><span data-stu-id="49b26-279">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="49b26-280">서비스. AddMvc (options => 옵션입니다. Filters. Add (new AutoValidateAntiforgeryTokenAttribute ()));</span><span class="sxs-lookup"><span data-stu-id="49b26-280">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="49b26-281">전역 또는 컨트롤러 위조 방지 특성 재정의</span><span class="sxs-lookup"><span data-stu-id="49b26-281">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="49b26-282">[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) 필터는 지정 된 작업 (또는 컨트롤러)에 위조 방지 토큰이 필요 하지 않도록 방지 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-282">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="49b26-283">적용 되는 경우이 필터 `ValidateAntiForgeryToken` 는 `AutoValidateAntiforgeryToken` 더 높은 수준 (전역 또는 컨트롤러)으로 지정 된 필터를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-283">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="49b26-284">인증 후 토큰 새로 고침</span><span class="sxs-lookup"><span data-stu-id="49b26-284">Refresh tokens after authentication</span></span>

<span data-ttu-id="49b26-285">사용자를 보기 또는 페이지 페이지로 리디렉션하여 사용자를 인증 한 후에 토큰을 새로 고쳐야 합니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="49b26-285">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or :::no-loc(Razor)::: Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="49b26-286">JavaScript, AJAX 및 SPAs</span><span class="sxs-lookup"><span data-stu-id="49b26-286">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="49b26-287">기존의 HTML 기반 앱에서 위조 방지 토큰은 숨겨진 양식 필드를 사용 하 여 서버로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-287">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="49b26-288">최신 JavaScript 기반 앱과 SPAs에서 많은 요청은 프로그래밍 방식으로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-288">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="49b26-289">이러한 AJAX 요청은 다른 기술 (예: 요청 헤더 또는 :::no-loc(cookie)::: s)을 사용 하 여 토큰을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-289">These AJAX requests may use other techniques (such as request headers or :::no-loc(cookie):::s) to send the token.</span></span>

<span data-ttu-id="49b26-290">:::no-loc(cookie):::S를 사용 하 여 인증 토큰을 저장 하 고 서버에서 API 요청을 인증 하는 경우 CSRF는 잠재적인 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-290">If :::no-loc(cookie):::s are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="49b26-291">로컬 저장소를 사용 하 여 토큰을 저장 하는 경우 로컬 저장소의 값이 모든 요청을 통해 서버에 자동으로 전송 되지 않으므로 CSRF 취약성이 완화 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-291">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="49b26-292">따라서 로컬 저장소를 사용 하 여 클라이언트에 위조 방지 토큰을 저장 하 고 요청 헤더로 토큰을 전송 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-292">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="49b26-293">JavaScript</span><span class="sxs-lookup"><span data-stu-id="49b26-293">JavaScript</span></span>

<span data-ttu-id="49b26-294">보기에서 JavaScript를 사용 하 여 뷰 내에서 서비스를 사용 하 여 토큰을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-294">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="49b26-295">[AspNetCore](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 를 뷰에 삽입 하 고 [Getandstoretokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-295">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="49b26-296">이 접근 방식을 사용 하면 :::no-loc(cookie)::: 서버에서를 설정 하거나 클라이언트에서 해당 항목을 읽는 것을 직접 처리 하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-296">This approach eliminates the need to deal directly with setting :::no-loc(cookie):::s from the server or reading them from the client.</span></span>

<span data-ttu-id="49b26-297">앞의 예제에서는 JavaScript를 사용 하 여 AJAX POST 헤더에 대 한 숨겨진 필드 값을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-297">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="49b26-298">JavaScript는 s에서 토큰에 액세스 하 :::no-loc(cookie)::: 고 :::no-loc(cookie)::: 의 내용을 사용 하 여 토큰 값을 사용 하 여 헤더를 만들 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-298">JavaScript can also access tokens in :::no-loc(cookie):::s and use the :::no-loc(cookie):::'s contents to create a header with the token's value.</span></span>

```csharp
context.Response.:::no-loc(Cookie):::s.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Options { HttpOnly = false });
```

<span data-ttu-id="49b26-299">스크립트가 라는 헤더에서 토큰을 보내도록 요청 하는 경우 `X-CSRF-TOKEN` 위조 방지 서비스를 구성 하 여 헤더를 찾습니다 `X-CSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="49b26-299">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="49b26-300">다음 예제에서는 JavaScript를 사용 하 여 적절 한 헤더를 사용 하는 AJAX 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-300">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function get:::no-loc(Cookie):::(cname) {
    var name = cname + "=";
    var decoded:::no-loc(Cookie)::: = decodeURIComponent(document.:::no-loc(cookie):::);
    var ca = decoded:::no-loc(Cookie):::.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = get:::no-loc(Cookie):::("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="49b26-301">AngularJS</span><span class="sxs-lookup"><span data-stu-id="49b26-301">AngularJS</span></span>

<span data-ttu-id="49b26-302">AngularJS는 규칙을 사용 하 여 CSRF를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-302">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="49b26-303">서버에서 :::no-loc(cookie)::: 이름이 인을 보내면 `XSRF-TOKEN` AngularJS `$http` 서비스는 :::no-loc(cookie)::: 서버에 요청을 보낼 때 헤더에 값을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-303">If the server sends a :::no-loc(cookie)::: with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the :::no-loc(cookie)::: value to a header when it sends a request to the server.</span></span> <span data-ttu-id="49b26-304">이 프로세스는 자동입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-304">This process is automatic.</span></span> <span data-ttu-id="49b26-305">헤더는 클라이언트에서 명시적으로 설정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-305">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="49b26-306">헤더 이름은 `X-XSRF-TOKEN` 입니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-306">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="49b26-307">서버에서이 헤더를 검색 하 고 내용의 유효성을 검사 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-307">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="49b26-308">ASP.NET Core API가 응용 프로그램 시작 시이 규칙을 사용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-308">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="49b26-309">호출 된에서 토큰을 제공 하도록 앱을 :::no-loc(cookie)::: 구성 `XSRF-TOKEN` 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-309">Configure your app to provide a token in a :::no-loc(cookie)::: called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="49b26-310">라는 헤더를 찾도록 위조 방지 서비스를 구성 `X-XSRF-TOKEN` 합니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-310">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable :::no-loc(cookie):::, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.:::no-loc(Cookie):::s.Append("XSRF-TOKEN", tokens.RequestToken, 
                new :::no-loc(Cookie):::Options() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="49b26-311">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49b26-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="49b26-312">위조 방지 확장</span><span class="sxs-lookup"><span data-stu-id="49b26-312">Extend antiforgery</span></span>

<span data-ttu-id="49b26-313">[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) 형식을 사용 하면 개발자가 각 토큰에서 추가 데이터를 라운드트립 하 여 csrf 시스템의 동작을 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-313">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="49b26-314">[Getadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) 메서드는 필드 토큰이 생성 될 때마다 호출 되며 반환 값은 생성 된 토큰에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-314">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="49b26-315">구현자는 타임 스탬프, nonce 또는 다른 값을 반환 하 고, 토큰의 유효성을 검사할 때 [Validateadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) 를 호출 하 여이 데이터의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-315">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="49b26-316">클라이언트의 사용자 이름이 이미 생성 된 토큰에 포함 되어 있으므로이 정보를 포함할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-316">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="49b26-317">토큰에 추가 데이터가 포함 되어 있지만가 구성 되지 않은 경우 `IAntiForgeryAdditionalDataProvider` 보충 데이터의 유효성이 검사 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49b26-317">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49b26-318">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="49b26-318">Additional resources</span></span>

* <span data-ttu-id="49b26-319">OWASP ( [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) )의 [csrf](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))</span><span class="sxs-lookup"><span data-stu-id="49b26-319">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
