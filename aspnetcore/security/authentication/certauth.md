---
title: ASP.NET Core에서 인증서 인증 구성
author: blowdart
description: IIS 및 HTTP.sys에 대 한 ASP.NET Core에서 인증서 인증을 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
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
uid: security/authentication/certauth
ms.openlocfilehash: c862bc8bff6c4cc80696d92067e814889d6e7782
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217533"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="2ab13-103">ASP.NET Core에서 인증서 인증 구성</span><span class="sxs-lookup"><span data-stu-id="2ab13-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="2ab13-104">`Microsoft.AspNetCore.Authentication.Certificate` ASP.NET Core에 대 한 [인증서 인증과](https://tools.ietf.org/html/rfc5246#section-7.4.4) 유사한 구현을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="2ab13-105">인증서 인증은 ASP.NET Core에 도달하기 훨씬 전에 TLS 수준에서 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="2ab13-106">보다 정확 하 게,이는 인증서의 유효성을 검사 한 다음 해당 인증서를에 대해 확인할 수 있는 이벤트를 제공 하는 인증 처리기입니다 `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="2ab13-107">인증서 인증을 위해 [서버를 구성](#configure-your-server-to-require-certificates) 하 고, IIS, Kestrel, Azure Web Apps 또는 사용 중인 다른 모든 항목을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-107">[Configure your server](#configure-your-server-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="2ab13-108">프록시 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="2ab13-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="2ab13-109">인증서 인증은 프록시 또는 부하 분산 장치가 클라이언트와 서버 간의 트래픽을 처리 하지 않는 경우 주로 사용 되는 상태 저장 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="2ab13-110">프록시 또는 부하 분산 장치를 사용 하는 경우 인증서 인증은 프록시 또는 부하 분산 장치에 대해서만 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="2ab13-111">인증을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-111">Handles the authentication.</span></span>
* <span data-ttu-id="2ab13-112">인증 정보에 대 한 역할을 하는 응용 프로그램 (예: 요청 헤더)에 사용자 인증 정보를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="2ab13-113">프록시 및 부하 분산 장치를 사용 하는 환경에서 인증서 인증에 대 한 대안은 OIDC (Openid connect Connect)를 사용 하는 페더레이션 서비스 (ADFS) Active Directory입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="2ab13-114">시작하기</span><span class="sxs-lookup"><span data-stu-id="2ab13-114">Get started</span></span>

<span data-ttu-id="2ab13-115">HTTPS 인증서를 획득 하 고 적용 한 다음 인증서를 요구 하도록 [서버를 구성](#configure-your-server-to-require-certificates) 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-115">Acquire an HTTPS certificate, apply it, and [configure your server](#configure-your-server-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="2ab13-116">웹 앱에서 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) 패키지에 대 한 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-116">In your web app, add a reference to the [Microsoft.AspNetCore.Authentication.Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) package.</span></span> <span data-ttu-id="2ab13-117">그런 다음 `Startup.ConfigureServices` 메서드에서와 함께를 호출 하 여 `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` 요청을 `OnCertificateValidated` 통해 보낸 클라이언트 인증서에 대 한 모든 보충 유효성 검사를 수행할 수 있는 대리자를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="2ab13-118">해당 정보를로 변환 하 `ClaimsPrincipal` 고 속성에 설정 `context.Principal` 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="2ab13-119">인증이 실패 하는 경우이 처리기는 `403 (Forbidden)` 정상적으로 응답을 반환 `401 (Unauthorized)` 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="2ab13-120">초기 TLS 연결 중에 인증이 수행 되어야 한다는 것을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="2ab13-121">처리기에 도달할 때까지 너무 늦습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="2ab13-122">익명 연결에서 인증서를 사용 하는 연결로의 연결을 업그레이드할 수 있는 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="2ab13-123">또한 `app.UseAuthentication();` 메서드에를 추가 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="2ab13-124">그렇지 않으면 `HttpContext.User` 인증서에서 생성 된로 설정 되지 않습니다 `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="2ab13-125">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-125">For example:</span></span>

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

<span data-ttu-id="2ab13-126">앞의 예제에서는 인증서 인증을 추가 하는 기본 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="2ab13-127">처리기는 일반적인 인증서 속성을 사용 하 여 사용자 보안 주체를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="2ab13-128">인증서 유효성 검사 구성</span><span class="sxs-lookup"><span data-stu-id="2ab13-128">Configure certificate validation</span></span>

<span data-ttu-id="2ab13-129">처리기에는 `CertificateAuthenticationOptions` 인증서에 대해 수행 해야 하는 최소 유효성 검사 인 몇 가지 기본 제공 유효성 검사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="2ab13-130">이러한 각 설정은 기본적으로 사용 하도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="2ab13-131">AllowedCertificateTypes = 체인, SelfSigned 또는 모두 (연결 된 | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="2ab13-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="2ab13-132">기본값: `CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="2ab13-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="2ab13-133">이 검사는 적절 한 인증서 유형만 허용 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="2ab13-134">앱에서 자체 서명 된 인증서를 사용 하는 경우이 옵션을 또는로 설정 `CertificateTypes.All` 해야 `CertificateTypes.SelfSigned` 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="2ab13-135">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="2ab13-135">ValidateCertificateUse</span></span>

<span data-ttu-id="2ab13-136">기본값: `true`</span><span class="sxs-lookup"><span data-stu-id="2ab13-136">Default value: `true`</span></span>

<span data-ttu-id="2ab13-137">이 검사는 클라이언트에서 제공 하는 인증서에 클라이언트 인증 EKU (확장 키 사용)가 있거나 Eku가 없는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="2ab13-138">지정 된 것 처럼 EKU가 지정 되지 않은 경우 모든 Eku가 유효한 것으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="2ab13-139">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="2ab13-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="2ab13-140">기본값: `true`</span><span class="sxs-lookup"><span data-stu-id="2ab13-140">Default value: `true`</span></span>

<span data-ttu-id="2ab13-141">이 검사는 인증서가 유효 기간 내에 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="2ab13-142">각 요청에서 처리기는 제공 된 유효한 인증서가 현재 세션 중에 만료 되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="2ab13-143">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="2ab13-143">RevocationFlag</span></span>

<span data-ttu-id="2ab13-144">기본값: `X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="2ab13-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="2ab13-145">체인에서 해지를 확인할 인증서를 지정 하는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="2ab13-146">해지 검사는 인증서가 루트 인증서에 연결 된 경우에만 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="2ab13-147">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="2ab13-147">RevocationMode</span></span>

<span data-ttu-id="2ab13-148">기본값: `X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="2ab13-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="2ab13-149">해지 검사를 수행 하는 방법을 지정 하는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="2ab13-150">온라인 검사를 지정 하면 인증 기관에 연결 하는 동안 지연 시간이 길어질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="2ab13-151">해지 검사는 인증서가 루트 인증서에 연결 된 경우에만 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="2ab13-152">특정 경로에 대해서만 인증서를 요구 하도록 앱을 구성할 수 있나요?</span><span class="sxs-lookup"><span data-stu-id="2ab13-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="2ab13-153">이는 불가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-153">This isn't possible.</span></span> <span data-ttu-id="2ab13-154">인증서 교환은 HTTPS 대화가 시작 되 면 서버에서 첫 번째 요청을 수신 하기 전에 수행 하는 것 이므로 요청 필드를 기준으로 범위를 지정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="2ab13-155">처리기 이벤트</span><span class="sxs-lookup"><span data-stu-id="2ab13-155">Handler events</span></span>

<span data-ttu-id="2ab13-156">처리기에는 두 가지 이벤트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-156">The handler has two events:</span></span>

* <span data-ttu-id="2ab13-157">`OnAuthenticationFailed`: 인증 하는 동안 예외가 발생 하 여 반응할 수 있는 경우 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-157">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="2ab13-158">`OnCertificateValidated`: 인증서의 유효성을 검사 하 고 유효성 검사를 통과 했으며 기본 보안 주체가 생성 된 후에 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-158">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="2ab13-159">이 이벤트를 사용 하 여 사용자 고유의 유효성 검사를 수행 하 고 보안 주체를 확대 하거나 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="2ab13-160">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-160">For examples include:</span></span>
  * <span data-ttu-id="2ab13-161">인증서가 서비스에 알려져 있는지 확인 하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="2ab13-162">사용자 고유의 보안 주체를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-162">Constructing your own principal.</span></span> <span data-ttu-id="2ab13-163">`Startup.ConfigureServices`에서 다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2ab13-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

    ```csharp
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate(options =>
        {
            options.Events = new CertificateAuthenticationEvents
            {
                OnCertificateValidated = context =>
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject,
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(ClaimTypes.Name,
                            context.ClientCertificate.Subject,
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };
    
                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
    
                    return Task.CompletedTask;
                }
            };
        });
    ```

<span data-ttu-id="2ab13-164">인바운드 인증서가 추가 유효성 검사를 충족 하지 않는 경우 `context.Fail("failure reason")` 오류 원인으로를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="2ab13-165">실제 기능을 사용 하려면 데이터베이스 또는 다른 유형의 사용자 저장소에 연결 하는 종속성 주입에 등록 된 서비스를 호출 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="2ab13-166">대리자에 전달 된 컨텍스트를 사용 하 여 서비스에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="2ab13-167">`Startup.ConfigureServices`에서 다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2ab13-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetRequiredService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="2ab13-168">개념적으로 인증서의 유효성 검사는 권한 부여에 대 한 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="2ab13-169">예를 들어, 권한 부여 정책에서 발급자 또는 지문을 포함 하는 것이 아니라 확인을 추가 하는 `OnCertificateValidated` 것은 완벽 하 게 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-server-to-require-certificates"></a><span data-ttu-id="2ab13-170">인증서를 요구 하도록 서버 구성</span><span class="sxs-lookup"><span data-stu-id="2ab13-170">Configure your server to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="2ab13-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="2ab13-171">Kestrel</span></span>

<span data-ttu-id="2ab13-172">*Program.cs* 에서 다음과 같이 Kestrel을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-172">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="2ab13-173"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="2ab13-174">IIS</span><span class="sxs-lookup"><span data-stu-id="2ab13-174">IIS</span></span>

<span data-ttu-id="2ab13-175">IIS 관리자에서 다음 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="2ab13-176">**연결** 탭에서 사이트를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="2ab13-177">**기능 보기** 창에서 **SSL 설정** 옵션을 두 번 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="2ab13-178">**SSL 필요** 확인란을 선택 하 고 **클라이언트 인증서** 섹션 **에서 라디오 단추를 선택** 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS의 클라이언트 인증서 설정](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="2ab13-180">Azure 및 사용자 지정 웹 프록시</span><span class="sxs-lookup"><span data-stu-id="2ab13-180">Azure and custom web proxies</span></span>

<span data-ttu-id="2ab13-181">인증서 전달 미들웨어를 구성 하는 방법은 [호스트 및 배포 설명서](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2ab13-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="2ab13-182">Azure Web Apps에서 인증서 인증 사용</span><span class="sxs-lookup"><span data-stu-id="2ab13-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="2ab13-183">Azure에는 전달 구성이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-183">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="2ab13-184">인증서 전달 미들웨어에 이미 설치 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-184">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="2ab13-185">이렇게 하려면 CertificateForwardingMiddleware이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-185">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="2ab13-186">사용자 지정 웹 프록시에서 인증서 인증 사용</span><span class="sxs-lookup"><span data-stu-id="2ab13-186">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="2ab13-187">`AddCertificateForwarding`메서드는 다음을 지정 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-187">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="2ab13-188">클라이언트 헤더 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-188">The client header name.</span></span>
* <span data-ttu-id="2ab13-189">속성을 사용 하 여 인증서를 로드 하는 방법 `HeaderConverter` 입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-189">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="2ab13-190">사용자 지정 웹 프록시에서 인증서는 사용자 지정 요청 헤더로 전달 됩니다 (예:) `X-SSL-CERT` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-190">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="2ab13-191">이를 사용 하려면에서 인증서 전달을 구성 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-191">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;

            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="2ab13-192">`Startup.Configure`그런 다음 메서드는 미들웨어를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-192">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="2ab13-193">`UseCertificateForwarding` 는 및를 호출 하기 전에 호출 됩니다 `UseAuthentication` `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-193">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="2ab13-194">별도의 클래스를 사용 하 여 유효성 검사 논리를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-194">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="2ab13-195">이 예제에서는 동일한 자체 서명 된 인증서를 사용 하기 때문에 인증서만 사용할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-195">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="2ab13-196">클라이언트 인증서와 서버 인증서의 지문이 일치 하는지 확인 합니다. 그렇지 않으면 인증서를 사용할 수 있으며 인증 하는 데 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-196">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="2ab13-197">이는 메서드 내에서 사용 됩니다 `AddCertificate` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-197">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="2ab13-198">중간 또는 자식 인증서를 사용 하는 경우 여기에서 주체 또는 발급자의 유효성을 검사할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-198">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="2ab13-199">Certificate 및 HttpClientHandler를 사용 하 여 HttpClient 구현</span><span class="sxs-lookup"><span data-stu-id="2ab13-199">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="2ab13-200">는 `HttpClientHandler` 클래스의 생성자에 직접 추가할 수 있습니다 `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-200">The `HttpClientHandler` could be added directly in the constructor of the `HttpClient` class.</span></span> <span data-ttu-id="2ab13-201">인스턴스를 만들 때 주의 해야 합니다 `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-201">Care should be taken when creating instances of the `HttpClient`.</span></span> <span data-ttu-id="2ab13-202">`HttpClient`그러면가 각 요청과 함께 인증서를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-202">The `HttpClient` will then send the certificate with each request.</span></span>

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="2ab13-203">IHttpClientFactory에서 인증서 및 명명 된 HttpClient를 사용 하 여 HttpClient 구현</span><span class="sxs-lookup"><span data-stu-id="2ab13-203">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="2ab13-204">다음 예제에서는 처리기의 속성을 사용 하 여에 클라이언트 인증서를 추가 합니다 `HttpClientHandler` `ClientCertificates` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-204">In the following example, a client certificate is added to a `HttpClientHandler` using the `ClientCertificates` property from the handler.</span></span> <span data-ttu-id="2ab13-205">그런 다음 `HttpClient` 메서드를 사용 하 여의 명명 된 인스턴스에서이 처리기를 사용할 수 있습니다 `ConfigurePrimaryHttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-205">This handler can then be used in a named instance of an `HttpClient` using the `ConfigurePrimaryHttpMessageHandler` method.</span></span> <span data-ttu-id="2ab13-206">다음 위치에 설치 됩니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-206">This is setup in `Startup.ConfigureServices`:</span></span>

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

<span data-ttu-id="2ab13-207">`IHttpClientFactory`그런 다음를 사용 하 여 처리기와 인증서를 사용 하 여 명명 된 인스턴스를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-207">The `IHttpClientFactory` can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="2ab13-208">`CreateClient`클래스에 정의 된 클라이언트 이름을 가진 메서드는 인스턴스를 `Startup` 가져오는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-208">The `CreateClient` method with the name of the client defined in the `Startup` class is used to get the instance.</span></span> <span data-ttu-id="2ab13-209">필요에 따라 클라이언트를 사용 하 여 HTTP 요청을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-209">The HTTP request can be sent using the client as required.</span></span>

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

<span data-ttu-id="2ab13-210">서버에 올바른 인증서를 보내면 데이터가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-210">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="2ab13-211">인증서 나 잘못 된 인증서를 보낸 경우 HTTP 403 상태 코드가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-211">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="2ab13-212">PowerShell에서 인증서 만들기</span><span class="sxs-lookup"><span data-stu-id="2ab13-212">Create certificates in PowerShell</span></span>

<span data-ttu-id="2ab13-213">인증서 만들기는이 흐름을 설정 하는 가장 어려운 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-213">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="2ab13-214">PowerShell cmdlet을 사용 하 여 루트 인증서를 만들 수 있습니다 `New-SelfSignedCertificate` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-214">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="2ab13-215">인증서를 만들 때 강력한 암호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-215">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="2ab13-216">`KeyUsageProperty`표시 된 대로 매개 변수 및 매개 변수를 추가 하는 것이 중요 합니다 `KeyUsage` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-216">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="2ab13-217">루트 CA 만들기</span><span class="sxs-lookup"><span data-stu-id="2ab13-217">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="2ab13-218">`-DnsName`매개 변수 값은 앱의 배포 대상과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-218">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="2ab13-219">예를 들어 개발용 "localhost"입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-219">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="2ab13-220">신뢰할 수 있는 루트에 설치</span><span class="sxs-lookup"><span data-stu-id="2ab13-220">Install in the trusted root</span></span>

<span data-ttu-id="2ab13-221">호스트 시스템에서 루트 인증서를 신뢰할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-221">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="2ab13-222">인증 기관에서 만들지 않은 루트 인증서는 기본적으로 신뢰할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-222">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="2ab13-223">다음 링크는 Windows에서이 작업을 수행 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-223">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="2ab13-224">중간 인증서</span><span class="sxs-lookup"><span data-stu-id="2ab13-224">Intermediate certificate</span></span>

<span data-ttu-id="2ab13-225">이제 루트 인증서에서 중간 인증서를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-225">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="2ab13-226">이는 모든 사용 사례에는 필요 하지 않지만 많은 인증서를 만들거나 인증서 그룹을 활성화 하거나 비활성화 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-226">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="2ab13-227">`TextExtension`매개 변수는 인증서의 기본 제약 조건에서 경로 길이를 설정 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-227">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="2ab13-228">그런 다음 Windows 호스트 시스템의 신뢰할 수 있는 중간 인증서에 중간 인증서를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-228">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="2ab13-229">중간 인증서에서 자식 인증서 만들기</span><span class="sxs-lookup"><span data-stu-id="2ab13-229">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="2ab13-230">중간 인증서에서 자식 인증서를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-230">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="2ab13-231">이 엔터티는 최종 엔터티 이며 더 이상 자식 인증서를 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-231">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="2ab13-232">루트 인증서에서 자식 인증서 만들기</span><span class="sxs-lookup"><span data-stu-id="2ab13-232">Create child certificate from root certificate</span></span>

<span data-ttu-id="2ab13-233">루트 인증서에서 직접 자식 인증서를 만들 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-233">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="2ab13-234">예제 루트-중간 인증서-인증서</span><span class="sxs-lookup"><span data-stu-id="2ab13-234">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="2ab13-235">루트, 중간 또는 자식 인증서를 사용 하는 경우 필요에 따라 지문 또는 PublicKey를 사용 하 여 인증서의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-235">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a><span data-ttu-id="2ab13-236">인증서 유효성 검사 캐싱</span><span class="sxs-lookup"><span data-stu-id="2ab13-236">Certificate validation caching</span></span>

<span data-ttu-id="2ab13-237">ASP.NET Core 5.0 이상 버전에서는 유효성 검사 결과의 캐싱을 사용 하도록 설정 하는 기능을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-237">ASP.NET Core 5.0 and later versions support the ability to enable caching of validation results.</span></span> <span data-ttu-id="2ab13-238">유효성 검사는 비용이 많이 드는 작업 이므로 캐싱은 인증서 인증의 성능을 크게 향상 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-238">The caching dramatically improves performance of certificate authentication, as validation is an expensive operation.</span></span>

<span data-ttu-id="2ab13-239">기본적으로 인증서 인증은 캐싱을 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-239">By default, certificate authentication disables caching.</span></span> <span data-ttu-id="2ab13-240">캐싱을 사용 하도록 설정 하려면에서을 호출 합니다 `AddCertificateCache` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-240">To enable caching, call `AddCertificateCache` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

<span data-ttu-id="2ab13-241">기본 캐싱 구현에서는 결과를 메모리에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-241">The default caching implementation stores results in memory.</span></span> <span data-ttu-id="2ab13-242">`ICertificateValidationCache`종속성 주입을 사용 하 여 구현 하 고 등록 하 여 자체 캐시를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-242">You can provide your own cache by implementing `ICertificateValidationCache` and registering it with dependency injection.</span></span> <span data-ttu-id="2ab13-243">예들 들어 `services.AddSingleton<ICertificateValidationCache, YourCache>()`입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-243">For example, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.</span></span>

::: moniker-end

## <a name="optional-client-certificates"></a><span data-ttu-id="2ab13-244">클라이언트 인증서 (옵션)</span><span class="sxs-lookup"><span data-stu-id="2ab13-244">Optional client certificates</span></span>

<span data-ttu-id="2ab13-245">이 섹션에서는 인증서를 사용 하 여 앱의 하위 집합을 보호 해야 하는 앱에 대 한 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-245">This section provides information for apps that must protect a subset of the app with a certificate.</span></span> <span data-ttu-id="2ab13-246">예를 들어 Razor 앱의 페이지 또는 컨트롤러에 클라이언트 인증서가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-246">For example, a Razor Page or controller in the app might require client certificates.</span></span> <span data-ttu-id="2ab13-247">클라이언트 인증서로 챌린지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-247">This presents challenges as client certificates:</span></span>
  
* <span data-ttu-id="2ab13-248">는 HTTP 기능이 아닌 TLS 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-248">Are a TLS feature, not an HTTP feature.</span></span>
* <span data-ttu-id="2ab13-249">는 연결당 협상 되며, HTTP 데이터를 사용 하려면 먼저 연결을 시작할 때 협상 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-249">Are negotiated per-connection and must be be negotiated at the start of the connection before any HTTP data is available.</span></span> <span data-ttu-id="2ab13-250">연결이 시작 될 때 SNI (서버 이름 표시)만 &dagger; 알려집니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-250">At the start of the connection, only the Server Name Indication (SNI)&dagger; is known.</span></span> <span data-ttu-id="2ab13-251">클라이언트 및 서버 인증서는 연결에 대 한 첫 번째 요청 이전에 협상 되며 요청은 일반적으로 재협상을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-251">The client and server certificates are negotiated prior to the first request on a connection and requests generally aren't able to renegotiate.</span></span>

<span data-ttu-id="2ab13-252">TLS 재협상은 선택적 클라이언트 인증서를 구현 하는 기존 방법 이었습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-252">TLS renegotiation was an old way to implement optional client certificates.</span></span> <span data-ttu-id="2ab13-253">이는 다음과 같은 이유로 더 이상 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-253">This is no longer recommended because:</span></span>
- <span data-ttu-id="2ab13-254">HTTP/1.1에서 POST 요청을 수행 하는 동안 renegotiating는 요청 본문이 TCP 창에서 채워지고 재협상 패킷을 받을 수 없는 교착 상태를 일으킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-254">In HTTP/1.1, renegotiating during a POST request could cause a deadlock where the request body filled up the TCP window and the renegotiation packets can't be received.</span></span>
- <span data-ttu-id="2ab13-255">HTTP/2는 재협상을 [명시적으로 금지](https://tools.ietf.org/html/rfc7540#section-9.2.1) 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-255">HTTP/2 [explicitly prohibits](https://tools.ietf.org/html/rfc7540#section-9.2.1) renegotiation.</span></span>
- <span data-ttu-id="2ab13-256">TLS 1.3에서 재협상에 대 한 지원을 [제거](https://tools.ietf.org/html/rfc8740#section-1) 했습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-256">TLS 1.3 has [removed](https://tools.ietf.org/html/rfc8740#section-1) support for renegotiation.</span></span>

<span data-ttu-id="2ab13-257">ASP.NET Core 5 preview 7 이상에서는 선택적 클라이언트 인증서에 대 한 편리한 지원을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-257">ASP.NET Core 5 preview 7 and later adds more convenient support for optional client certificates.</span></span> <span data-ttu-id="2ab13-258">자세한 내용은 [선택적 인증서 샘플](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2ab13-258">For more information, see the [Optional certificates sample](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span></span>

<span data-ttu-id="2ab13-259">다음 방법에서는 선택적 클라이언트 인증서를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-259">The following approach supports optional client certificates:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="2ab13-260">도메인 및 하위 도메인에 대 한 바인딩 설정:</span><span class="sxs-lookup"><span data-stu-id="2ab13-260">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="2ab13-261">예를 들어 및에 대 한 바인딩을 설정 `contoso.com` `myClient.contoso.com` 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-261">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="2ab13-262">호스트에는 `contoso.com` 클라이언트 인증서가 필요 하지 `myClient.contoso.com` 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-262">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="2ab13-263">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2ab13-263">For more information, see:</span></span>
    * <span data-ttu-id="2ab13-264">[Kestrel](/fundamentals/servers/kestrel):</span><span class="sxs-lookup"><span data-stu-id="2ab13-264">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="2ab13-265">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="2ab13-265">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel/endpoints#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="2ab13-266">참고 Kestrel은 현재 하나의 바인딩에서 여러 TLS 구성을 지원 하지 않으므로 고유한 Ip 또는 포트를 사용 하는 두 개의 바인딩이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-266">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="2ab13-267"> https://github.com/dotnet/runtime/issues/31097을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="2ab13-267">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="2ab13-268">IIS</span><span class="sxs-lookup"><span data-stu-id="2ab13-268">IIS</span></span>
      * [<span data-ttu-id="2ab13-269">IIS 호스팅</span><span class="sxs-lookup"><span data-stu-id="2ab13-269">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="2ab13-270">IIS에 대 한 보안 구성</span><span class="sxs-lookup"><span data-stu-id="2ab13-270">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="2ab13-271">HTTP.sys: [Windows Server 구성](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="2ab13-271">HTTP.sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="2ab13-272">도메인 및 하위 도메인에 대 한 바인딩 설정:</span><span class="sxs-lookup"><span data-stu-id="2ab13-272">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="2ab13-273">예를 들어 및에 대 한 바인딩을 설정 `contoso.com` `myClient.contoso.com` 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-273">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="2ab13-274">호스트에는 `contoso.com` 클라이언트 인증서가 필요 하지 `myClient.contoso.com` 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-274">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="2ab13-275">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2ab13-275">For more information, see:</span></span>
    * <span data-ttu-id="2ab13-276">[Kestrel](/fundamentals/servers/kestrel):</span><span class="sxs-lookup"><span data-stu-id="2ab13-276">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="2ab13-277">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="2ab13-277">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="2ab13-278">참고 Kestrel은 현재 하나의 바인딩에서 여러 TLS 구성을 지원 하지 않으므로 고유한 Ip 또는 포트를 사용 하는 두 개의 바인딩이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-278">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="2ab13-279"> https://github.com/dotnet/runtime/issues/31097을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="2ab13-279">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="2ab13-280">IIS</span><span class="sxs-lookup"><span data-stu-id="2ab13-280">IIS</span></span>
      * [<span data-ttu-id="2ab13-281">IIS 호스팅</span><span class="sxs-lookup"><span data-stu-id="2ab13-281">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="2ab13-282">IIS에 대 한 보안 구성</span><span class="sxs-lookup"><span data-stu-id="2ab13-282">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="2ab13-283">HTTP.sys: [Windows Server 구성](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="2ab13-283">HTTP.sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>

::: moniker-end

* <span data-ttu-id="2ab13-284">클라이언트 인증서가 필요 하 고 없는 웹 앱에 대 한 요청의 경우:</span><span class="sxs-lookup"><span data-stu-id="2ab13-284">For requests to the web app that require a client certificate and don't have one:</span></span>
  * <span data-ttu-id="2ab13-285">클라이언트 인증서 보호 된 하위 도메인을 사용 하 여 동일한 페이지로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-285">Redirect to the same page using the client certificate protected subdomain.</span></span>
  * <span data-ttu-id="2ab13-286">예를 들어를로 리디렉션합니다 `myClient.contoso.com/requestedPage` .</span><span class="sxs-lookup"><span data-stu-id="2ab13-286">For example, redirect to `myClient.contoso.com/requestedPage`.</span></span> <span data-ttu-id="2ab13-287">에 대 한 요청은와 `myClient.contoso.com/requestedPage` 다른 호스트 이름이 기 때문에 `contoso.com/requestedPage` 클라이언트에서 다른 연결을 설정 하 고 클라이언트 인증서가 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-287">Because the request to `myClient.contoso.com/requestedPage` is a different hostname than `contoso.com/requestedPage`, the client establishes a different connection and the client certificate is provided.</span></span>
  * <span data-ttu-id="2ab13-288">자세한 내용은 <xref:security/authorization/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2ab13-288">For more information, see <xref:security/authorization/introduction>.</span></span>

<span data-ttu-id="2ab13-289">[이 GitHub 토론](https://github.com/dotnet/AspNetCore.Docs/issues/18720) 문제에서 선택적 클라이언트 인증서에 대 한 질문, 설명 및 기타 피드백을 남겨 두세요.</span><span class="sxs-lookup"><span data-stu-id="2ab13-289">Leave questions, comments, and other feedback on optional client certificates in [this GitHub discussion](https://github.com/dotnet/AspNetCore.Docs/issues/18720) issue.</span></span>

<span data-ttu-id="2ab13-290">&dagger; SNI (서버 이름 표시)는 SSL 협상의 일부로 가상 도메인을 포함 하는 TLS 확장입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-290">&dagger; Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="2ab13-291">이는 실제로 가상 도메인 이름 또는 호스트 이름을 사용 하 여 네트워크 끝점을 식별할 수 있다는 의미입니다.</span><span class="sxs-lookup"><span data-stu-id="2ab13-291">This effectively means the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>
