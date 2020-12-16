---
title: ASP.NET Core에서 HTTPS 적용
author: rick-anderson
description: ASP.NET Core 웹 앱에서 HTTPS/TLS를 요구 하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
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
uid: security/enforcing-ssl
ms.openlocfilehash: 3277fda0d1dcb5121a2172b3fc1e4869ed6f8430
ms.sourcegitcommit: fc4cce2767e34f81079510f34bd54e9d0aa86497
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592871"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="b9a33-103">ASP.NET Core에서 HTTPS 적용</span><span class="sxs-lookup"><span data-stu-id="b9a33-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="b9a33-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b9a33-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b9a33-105">이 문서에서는 다음을 수행 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-105">This document shows how to:</span></span>

* <span data-ttu-id="b9a33-106">모든 요청에 대해 HTTPS를 요구 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="b9a33-107">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="b9a33-108">API가 없으면 클라이언트가 첫 번째 요청에서 중요 한 데이터를 전송 하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="b9a33-109">API 프로젝트</span><span class="sxs-lookup"><span data-stu-id="b9a33-109">API projects</span></span>
>
> <span data-ttu-id="b9a33-110">중요 한 정보를 수신 하는 웹 Api에 [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) 를 사용 **하지** 마세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="b9a33-111">`RequireHttpsAttribute` http 상태 코드를 사용 하 여 브라우저를 HTTP에서 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="b9a33-112">API 클라이언트는 HTTP에서 HTTPS로의 리디렉션을 인식 하거나 준수 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="b9a33-113">이러한 클라이언트는 HTTP를 통해 정보를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="b9a33-114">웹 Api는 다음 중 하나를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="b9a33-115">HTTP에서 수신 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="b9a33-116">상태 코드 400 (잘못 된 요청)이 포함 된 연결을 닫고 요청을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="b9a33-117">HSTS 및 API 프로젝트</span><span class="sxs-lookup"><span data-stu-id="b9a33-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="b9a33-118">HSTS는 일반적으로 브라우저 전용 명령 이므로 기본 API 프로젝트는 [Hsts](#hsts) 를 포함 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="b9a33-119">전화 또는 데스크톱 앱과 같은 다른 호출자는 지침을 따르지 **않습니다** .</span><span class="sxs-lookup"><span data-stu-id="b9a33-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="b9a33-120">브라우저 내 에서도 HTTP를 통한 API에 대 한 인증 된 단일 호출은 안전 하지 않은 네트워크에서 위험이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="b9a33-121">안전한 방법은 HTTPS를 통해서만 수신 대기 하 고 응답 하도록 API 프로젝트를 구성 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="b9a33-122">API 프로젝트</span><span class="sxs-lookup"><span data-stu-id="b9a33-122">API projects</span></span>
>
> <span data-ttu-id="b9a33-123">중요 한 정보를 수신 하는 웹 Api에 [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) 를 사용 **하지** 마세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="b9a33-124">`RequireHttpsAttribute` http 상태 코드를 사용 하 여 브라우저를 HTTP에서 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="b9a33-125">API 클라이언트는 HTTP에서 HTTPS로의 리디렉션을 인식 하거나 준수 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="b9a33-126">이러한 클라이언트는 HTTP를 통해 정보를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="b9a33-127">웹 Api는 다음 중 하나를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="b9a33-128">HTTP에서 수신 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="b9a33-129">상태 코드 400 (잘못 된 요청)이 포함 된 연결을 닫고 요청을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="b9a33-130">HTTPS 필요</span><span class="sxs-lookup"><span data-stu-id="b9a33-130">Require HTTPS</span></span>

<span data-ttu-id="b9a33-131">프로덕션 ASP.NET Core 웹 앱을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="b9a33-132">Https 리디렉션 미들웨어 ( <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> )-HTTP 요청을 https로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="b9a33-133">클라이언트에 HSTS (HTTP Strict Transport Security Protocol) 헤더를 전송 하는 HSTS 미들웨어 ([Usehsts](#http-strict-transport-security-protocol-hsts))</span><span class="sxs-lookup"><span data-stu-id="b9a33-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="b9a33-134">역방향 프록시 구성에 배포 된 앱을 통해 프록시가 연결 보안 (HTTPS)을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="b9a33-135">프록시가 HTTPS 리디렉션을 처리 하는 경우에는 HTTPS 리디렉션 미들웨어를 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="b9a33-136">또한 프록시 서버에서 HSTS 헤더 쓰기를 처리 하는 경우 (예: [IIS 10.0 (1709) 이상에서 기본 hsts 지원](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)) 앱에 Hsts 미들웨어가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="b9a33-137">자세한 내용은 [프로젝트를 만들 때 HTTPS/HSTS 옵트아웃 (Opt out)](#opt-out-of-httpshsts-on-project-creation)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="b9a33-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="b9a33-138">UseHttpsRedirection</span></span>

<span data-ttu-id="b9a33-139">클래스에서를 호출 하는 코드는 다음과 `UseHttpsRedirection` `Startup` 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="b9a33-140">앞에서 강조 표시 된 코드:</span><span class="sxs-lookup"><span data-stu-id="b9a33-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="b9a33-141">기본 [HttpsRedirectionOptions](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect))를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="b9a33-142">는 [](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) `ASPNETCORE_HTTPS_PORT` 환경 변수 또는 [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)에 의해 재정의 되지 않는 한 기본 HttpsRedirectionOptions (null)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="b9a33-143">영구 리디렉션이 아닌 임시 리디렉션을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="b9a33-144">링크 캐싱은 개발 환경에서 불안정한 동작을 일으킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="b9a33-145">앱이 개발 환경에 있지 않은 경우 영구 리디렉션 상태 코드를 보내려면 [프로덕션에서 영구 리디렉션 구성](#configure-permanent-redirects-in-production) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="b9a33-146">[Hsts](#http-strict-transport-security-protocol-hsts) 를 사용 하 여 보안 리소스 요청만 앱에 전송 되어야 한다는 것을 클라이언트에 알리는 것이 좋습니다 (프로덕션 환경 에서만).</span><span class="sxs-lookup"><span data-stu-id="b9a33-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="b9a33-147">포트 구성</span><span class="sxs-lookup"><span data-stu-id="b9a33-147">Port configuration</span></span>

<span data-ttu-id="b9a33-148">미들웨어가 안전 하지 않은 요청을 HTTPS로 리디렉션하는 데 포트를 사용할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="b9a33-149">포트를 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="b9a33-149">If no port is available:</span></span>

* <span data-ttu-id="b9a33-150">HTTPS로의 리디렉션이 발생 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="b9a33-151">미들웨어는 "리디렉션을 위한 https 포트를 결정 하지 못했습니다." 경고를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="b9a33-152">다음 방법 중 하나를 사용 하 여 HTTPS 포트를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="b9a33-153">[HttpsRedirectionOptions](#options)를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="b9a33-154">`https_port` [호스트 설정을](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port)다음과 같이 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-154">Set the `https_port` [host setting](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="b9a33-155">호스트 구성에서.</span><span class="sxs-lookup"><span data-stu-id="b9a33-155">In host configuration.</span></span>
  * <span data-ttu-id="b9a33-156">`ASPNETCORE_HTTPS_PORT`환경 변수를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="b9a33-157">에서 최상위 항목을 추가 하 여 *appsettings.json* 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="b9a33-158">[ASPNETCORE_URLS 환경 변수](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls)를 사용 하 여 보안 체계가 있는 포트를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="b9a33-159">환경 변수는 서버를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-159">The environment variable configures the server.</span></span> <span data-ttu-id="b9a33-160">미들웨어는를 통해 HTTPS 포트를 간접적으로 검색 합니다 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="b9a33-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="b9a33-161">이 방법은 역방향 프록시 배포에서 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="b9a33-162">`https_port` [호스트 설정을](xref:fundamentals/host/web-host#https-port)다음과 같이 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="b9a33-163">호스트 구성에서.</span><span class="sxs-lookup"><span data-stu-id="b9a33-163">In host configuration.</span></span>
  * <span data-ttu-id="b9a33-164">`ASPNETCORE_HTTPS_PORT`환경 변수를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="b9a33-165">에서 최상위 항목을 추가 하 여 *appsettings.json* 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="b9a33-166">[ASPNETCORE_URLS 환경 변수](xref:fundamentals/host/web-host#server-urls)를 사용 하 여 보안 체계가 있는 포트를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="b9a33-167">환경 변수는 서버를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-167">The environment variable configures the server.</span></span> <span data-ttu-id="b9a33-168">미들웨어는를 통해 HTTPS 포트를 간접적으로 검색 합니다 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="b9a33-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="b9a33-169">이 방법은 역방향 프록시 배포에서 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="b9a33-170">개발에서 *launchsettings.js* 의 HTTPS URL을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="b9a33-171">IIS Express 사용 하는 경우 HTTPS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="b9a33-172">[Kestrel](xref:fundamentals/servers/kestrel) 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys) 서버의 공용에 지 배포에 대 한 HTTPS URL 끝점을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="b9a33-173">앱에서 **HTTPS 포트** 를 하나만 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="b9a33-174">미들웨어는를 통해 포트를 검색 합니다 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="b9a33-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="b9a33-175">앱이 역방향 프록시 구성에서 실행 되는 경우에는를 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="b9a33-176">이 섹션에 설명 된 다른 방법 중 하나를 사용 하 여 포트를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="b9a33-177">Edge 배포</span><span class="sxs-lookup"><span data-stu-id="b9a33-177">Edge deployments</span></span> 

<span data-ttu-id="b9a33-178">Kestrel 또는 HTTP.sys를 공용에 지 서버로 사용 하는 경우 Kestrel 또는 HTTP.sys는 둘 모두를 수신 하도록 구성 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="b9a33-179">클라이언트가 리디렉션되는 보안 포트 (일반적으로 프로덕션의 경우 443, 개발 중인 경우 5001)</span><span class="sxs-lookup"><span data-stu-id="b9a33-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="b9a33-180">안전 하지 않은 포트 (일반적으로 프로덕션의 경우 80, 개발에서 5000)</span><span class="sxs-lookup"><span data-stu-id="b9a33-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="b9a33-181">응용 프로그램이 안전 하지 않은 요청을 받고 클라이언트를 보안 포트로 리디렉션하도록 클라이언트에서 보안 되지 않은 포트에 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="b9a33-182">자세한 내용은 [Kestrel 끝점 구성](xref:fundamentals/servers/kestrel#endpoint-configuration) 또는을 참조 하세요 <xref:fundamentals/servers/httpsys> .</span><span class="sxs-lookup"><span data-stu-id="b9a33-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="b9a33-183">배포 시나리오</span><span class="sxs-lookup"><span data-stu-id="b9a33-183">Deployment scenarios</span></span>

<span data-ttu-id="b9a33-184">클라이언트와 서버 간의 모든 방화벽에는 트래픽에 대 한 통신 포트도 열려 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="b9a33-185">역방향 프록시 구성에서 요청을 전달 하는 경우 HTTPS 리디렉션 미들웨어를 호출 하기 전에 [전달 된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer) 를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="b9a33-186">전달 된 헤더 미들웨어는 `Request.Scheme` 헤더를 사용 하 여를 업데이트 합니다 `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="b9a33-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="b9a33-187">미들웨어는 리디렉션 Uri 및 기타 보안 정책이 제대로 작동 하도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="b9a33-188">전달 된 헤더 미들웨어를 사용 하지 않는 경우 백엔드 앱은 올바른 체계를 수신 하지 않고 리디렉션 루프에서 종료 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="b9a33-189">일반적인 최종 사용자 오류 메시지는 너무 많은 리디렉션이 발생 한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="b9a33-190">Azure App Service에 배포할 때 [자습서: Azure Web Apps에 기존 사용자 지정 SSL 인증서 바인딩](/azure/app-service/app-service-web-tutorial-custom-ssl)의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="b9a33-191">옵션</span><span class="sxs-lookup"><span data-stu-id="b9a33-191">Options</span></span>

<span data-ttu-id="b9a33-192">다음 강조 표시 된 코드는 [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) 을 호출 하 여 미들웨어 옵션을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="b9a33-193">`AddHttpsRedirection`또는의 값을 변경 하는 경우에만를 호출 해야 `HttpsPort` `RedirectStatusCode` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="b9a33-194">앞에서 강조 표시 된 코드:</span><span class="sxs-lookup"><span data-stu-id="b9a33-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="b9a33-195">[HttpsRedirectionOptions](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) 를 기본값인로 설정 <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="b9a33-196"><xref:Microsoft.AspNetCore.Http.StatusCodes>에 대 한 할당에 클래스의 필드를 사용 `RedirectStatusCode` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="b9a33-197">HTTPS 포트를 5001으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-197">Sets the HTTPS port to 5001.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="b9a33-198">프로덕션 환경에서 영구 리디렉션 구성</span><span class="sxs-lookup"><span data-stu-id="b9a33-198">Configure permanent redirects in production</span></span>

<span data-ttu-id="b9a33-199">미들웨어는 기본적으로 모든 리디렉션을 사용 하 여 [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) 를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-199">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="b9a33-200">앱이 개발 환경에 있지 않은 경우 영구 리디렉션 상태 코드를 보내려면 비 개발 환경에 대 한 조건부 검사에서 미들웨어 옵션 구성을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-200">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b9a33-201">*Startup.cs* 에서 서비스를 구성 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="b9a33-201">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="b9a33-202">*Startup.cs* 에서 서비스를 구성 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="b9a33-202">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="b9a33-203">HTTPS 리디렉션 미들웨어 대체 방법</span><span class="sxs-lookup"><span data-stu-id="b9a33-203">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="b9a33-204">HTTPS 리디렉션 미들웨어 ()를 사용 하 `UseHttpsRedirection` 는 대신 URL 재작성 미들웨어 ()를 사용할 수 `AddRedirectToHttps` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-204">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="b9a33-205">`AddRedirectToHttps` 리디렉션이 실행 될 때 상태 코드 및 포트를 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-205">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="b9a33-206">자세한 내용은 [URL 재작성 미들웨어](xref:fundamentals/url-rewriting)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-206">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="b9a33-207">추가 리디렉션 규칙을 요구 하지 않고 HTTPS로 리디렉션하는 경우 `UseHttpsRedirection` 이 항목에서 설명 하는 Https 리디렉션 미들웨어 ()를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-207">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="b9a33-208">HTTP HSTS (Strict Transport Security Protocol)</span><span class="sxs-lookup"><span data-stu-id="b9a33-208">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="b9a33-209">[OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)에 따라 [hsts (HTTP Strict Transport security)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) 는 응답 헤더를 사용 하 여 웹 앱에서 지정 하는 옵트인 (opt in) 보안 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="b9a33-210">[HSTS를 지 원하는 브라우저가](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) 이 헤더를 수신 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="b9a33-210">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="b9a33-211">브라우저는 HTTP를 통한 통신 전송을 방지 하는 도메인에 대 한 구성을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-211">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="b9a33-212">브라우저는 HTTPS를 통한 모든 통신을 강제로 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-212">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="b9a33-213">브라우저는 사용자가 신뢰할 수 없거나 유효 하지 않은 인증서를 사용 하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-213">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="b9a33-214">브라우저는 사용자가 이러한 인증서를 일시적으로 신뢰할 수 있도록 하는 프롬프트를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-214">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="b9a33-215">HSTS는 클라이언트에 의해 적용 되므로 다음과 같은 몇 가지 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-215">Because HSTS is enforced by the client, it has some limitations:</span></span>

* <span data-ttu-id="b9a33-216">클라이언트는 HSTS를 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-216">The client must support HSTS.</span></span>
* <span data-ttu-id="b9a33-217">HSTS는 HSTS 정책을 설정 하기 위해 하나 이상의 성공한 HTTPS 요청이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-217">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="b9a33-218">응용 프로그램은 모든 HTTP 요청을 확인 하 고 HTTP 요청을 리디렉션하거나 거부 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-218">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="b9a33-219">ASP.NET Core 2.1 이상에서는 확장 메서드를 사용 하 여 HSTS를 구현 `UseHsts` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-219">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="b9a33-220">다음 코드는 `UseHsts` 앱이 [개발 모드가](xref:fundamentals/environments)아닐 때를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-220">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="b9a33-221">`UseHsts` HSTS 설정은 브라우저에서 매우 캐시할 수 있으므로 개발에는 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-221">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="b9a33-222">기본적으로는 `UseHsts` 로컬 루프백 주소를 제외 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-222">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="b9a33-223">처음으로 HTTPS를 구현 하는 프로덕션 환경의 경우 메서드 중 하나를 사용 하 여 초기 [HstsOptions](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) 를 작은 값으로 설정 합니다. <xref:System.TimeSpan></span><span class="sxs-lookup"><span data-stu-id="b9a33-223">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="b9a33-224">HTTPS 인프라를 HTTP로 되돌려야 하는 경우에는 값을 하루에 한 번 이상으로 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-224">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="b9a33-225">HTTPS 구성의 유지 가능성을 확신 하는 경우 HSTS 값을 늘립니다 `max-age` . 일반적으로 사용 되는 값은 1 년입니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-225">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS `max-age` value; a commonly used value is one year.</span></span>

<span data-ttu-id="b9a33-226">코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-226">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="b9a33-227">헤더의 미리 로드 매개 변수를 설정 합니다 `Strict-Transport-Security` .</span><span class="sxs-lookup"><span data-stu-id="b9a33-227">Sets the preload parameter of the `Strict-Transport-Security` header.</span></span> <span data-ttu-id="b9a33-228">프리 로드는 [RFC hsts 사양의](https://tools.ietf.org/html/rfc6797)일부가 아니지만 웹 브라우저가 새로 설치 시 hsts 사이트를 미리 로드 하도록 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-228">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="b9a33-229">자세한 내용은 [https://hstspreload.org/](https://hstspreload.org/)을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-229">For more information, see [https://hstspreload.org/](https://hstspreload.org/).</span></span>
* <span data-ttu-id="b9a33-230">도메인을 호스트 하는 데 HSTS 정책을 적용 하는 [Includesubdomain 도메인](https://tools.ietf.org/html/rfc6797#section-6.1.2)을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-230">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="b9a33-231">`max-age`헤더의 매개 변수를 60 일로 명시적으로 설정 `Strict-Transport-Security` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-231">Explicitly sets the `max-age` parameter of the `Strict-Transport-Security` header to 60 days.</span></span> <span data-ttu-id="b9a33-232">설정 되지 않은 경우 기본값은 30 일입니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-232">If not set, defaults to 30 days.</span></span> <span data-ttu-id="b9a33-233">자세한 내용은 [최대 기간 지시문](https://tools.ietf.org/html/rfc6797#section-6.1.1)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-233">For more information, see the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span></span>
* <span data-ttu-id="b9a33-234">`example.com`제외할 호스트 목록에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-234">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="b9a33-235">`UseHsts` 다음 루프백 호스트를 제외 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-235">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="b9a33-236">`localhost` : IPv4 루프백 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-236">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="b9a33-237">`127.0.0.1` : IPv4 루프백 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-237">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="b9a33-238">`[::1]` : IPv6 루프백 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-238">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="b9a33-239">프로젝트를 만들 때 HTTPS/HSTS 옵트아웃 (Opt out)</span><span class="sxs-lookup"><span data-stu-id="b9a33-239">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="b9a33-240">네트워크의 공용 가장자리에서 연결 보안이 처리 되는 일부 백 엔드 서비스 시나리오에서는 각 노드에서 연결 보안을 구성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-240">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="b9a33-241">Visual Studio 또는 [dotnet new](/dotnet/core/tools/dotnet-new) 명령에서 템플릿에서 생성 된 웹 앱은 [HTTPS 리디렉션과](#require-https) [hsts](#http-strict-transport-security-protocol-hsts)를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-241">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="b9a33-242">이러한 시나리오가 필요 하지 않은 배포의 경우 템플릿에서 앱을 만들 때 HTTPS/HSTS를 옵트아웃 (opt out) 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-242">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="b9a33-243">HTTPS/HSTS를 옵트아웃 (opt out) 하려면:</span><span class="sxs-lookup"><span data-stu-id="b9a33-243">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9a33-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9a33-244">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b9a33-245">**HTTPS에 대해 구성** 확인란의 선택을 취소 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-245">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![HTTPS에 대해 구성 확인란을 선택 하지 않은 새 ASP.NET Core 웹 응용 프로그램 대화 상자](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![HTTPS에 대해 구성 확인란을 선택 하지 않은 새 ASP.NET Core 웹 응용 프로그램 대화 상자](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="b9a33-248">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b9a33-248">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="b9a33-249">`--no-https` 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-249">Use the `--no-https` option.</span></span> <span data-ttu-id="b9a33-250">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-250">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="b9a33-251">Windows 및 macOS에서 ASP.NET Core HTTPS 개발 인증서 신뢰</span><span class="sxs-lookup"><span data-stu-id="b9a33-251">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="b9a33-252">.NET Core SDK에는 HTTPS 개발 인증서가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-252">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="b9a33-253">인증서는 첫 실행 환경의 일부로 설치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-253">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="b9a33-254">예를 들어는 `dotnet --info` 다음 출력의 변형을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-254">For example, `dotnet --info` produces a variation of the following output:</span></span>

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="b9a33-255">.NET Core SDK를 설치하면 로컬 사용자 인증서 저장소에 ASP.NET Core HTTPS 개발 인증서가 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-255">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="b9a33-256">인증서가 설치 되었지만 트러스트 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-256">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="b9a33-257">인증서를 신뢰 하려면 dotnet 도구를 실행 하는 일회성 단계를 수행 합니다 `dev-certs` .</span><span class="sxs-lookup"><span data-stu-id="b9a33-257">To trust the certificate, perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="b9a33-258">다음 명령은 `dev-certs` 도구에 대한 도움말을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-258">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="b9a33-259">Docker 용 개발자 인증서를 설정 하는 방법</span><span class="sxs-lookup"><span data-stu-id="b9a33-259">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="b9a33-260">이 [GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/6199)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-260">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="ssl-linux"></a>

## <a name="trust-https-certificate-on-linux"></a><span data-ttu-id="b9a33-261">Linux에서 HTTPS 인증서 신뢰</span><span class="sxs-lookup"><span data-stu-id="b9a33-261">Trust HTTPS certificate on Linux</span></span>

<!-- Instructions to be updated by engineering team after 5.0 RTM. -->

<span data-ttu-id="b9a33-262">Linux에 대 한 지침은 배포 설명서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-262">For instructions on Linux, refer to the distribution documentation.</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="b9a33-263">Linux 용 Windows 하위 시스템에서 HTTPS 인증서 신뢰</span><span class="sxs-lookup"><span data-stu-id="b9a33-263">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="b9a33-264">WSL (Linux 용 Windows 하위 시스템)은 HTTPS 자체 서명 된 인증서를 생성 합니다. WSL 인증서를 신뢰 하도록 Windows 인증서 저장소를 구성 하려면:</span><span class="sxs-lookup"><span data-stu-id="b9a33-264">The Windows Subsystem for Linux (WSL) generates an HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="b9a33-265">다음 명령을 실행 하 여 WSL 생성 인증서를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-265">Run the following command to export the WSL-generated certificate:</span></span>

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* <span data-ttu-id="b9a33-266">WSL 창에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-266">In a WSL window, run the following command:</span></span>

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  <span data-ttu-id="b9a33-267">위의 명령은 Linux에서 Windows 신뢰할 수 있는 인증서를 사용 하도록 환경 변수를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-267">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="b9a33-268">인증서 문제 해결</span><span class="sxs-lookup"><span data-stu-id="b9a33-268">Troubleshoot certificate problems</span></span>

<span data-ttu-id="b9a33-269">이 섹션에서는 ASP.NET Core HTTPS 개발 인증서를 [설치 하 고 신뢰할 수](#trust)있는 경우에 대 한 도움말을 제공 하지만 인증서를 신뢰할 수 없다는 브라우저 경고가 계속 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-269">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="b9a33-270">ASP.NET Core HTTPS 개발 인증서는 [Kestrel](xref:fundamentals/servers/kestrel)에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-270">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="b9a33-271">IIS Express 인증서를 복구 하려면 [이 Stackoverflow](https://stackoverflow.com/a/20048613/502537) 문제를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-271">To repair the IIS Express certificate, see [this Stackoverflow](https://stackoverflow.com/a/20048613/502537) issue.</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="b9a33-272">모든 플랫폼-인증서를 신뢰할 수 없음</span><span class="sxs-lookup"><span data-stu-id="b9a33-272">All platforms - certificate not trusted</span></span>

<span data-ttu-id="b9a33-273">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-273">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="b9a33-274">열려 있는 모든 브라우저 인스턴스를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-274">Close any browser instances open.</span></span> <span data-ttu-id="b9a33-275">앱에 대 한 새 브라우저 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-275">Open a new browser window to app.</span></span> <span data-ttu-id="b9a33-276">인증서 신뢰는 브라우저에 의해 캐시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-276">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="b9a33-277">위의 명령은 대부분의 브라우저 트러스트 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-277">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="b9a33-278">브라우저에서 여전히 인증서를 신뢰 하지 않는 경우에는 다음 플랫폼 관련 제안 사항을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-278">If the browser is still not trusting the certificate, follow the platform-specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="b9a33-279">Docker-인증서를 신뢰할 수 없음</span><span class="sxs-lookup"><span data-stu-id="b9a33-279">Docker - certificate not trusted</span></span>

* <span data-ttu-id="b9a33-280">*C:\Users \{ USER} \AppData\Roaming\ASP.NET\Https* 폴더를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-280">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="b9a33-281">솔루션을 정리 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-281">Clean the solution.</span></span> <span data-ttu-id="b9a33-282">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-282">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="b9a33-283">개발 도구를 다시 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-283">Restart the development tool.</span></span> <span data-ttu-id="b9a33-284">예를 들어 Visual Studio, Visual Studio Code 또는 Mac용 Visual Studio입니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-284">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="b9a33-285">Windows-인증서를 신뢰할 수 없음</span><span class="sxs-lookup"><span data-stu-id="b9a33-285">Windows - certificate not trusted</span></span>

* <span data-ttu-id="b9a33-286">인증서 저장소의 인증서를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-286">Check the certificates in the certificate store.</span></span> <span data-ttu-id="b9a33-287">`localhost`및의 이름을 가진 인증서가 있어야 합니다. `ASP.NET Core HTTPS development certificate` `Current User > Personal > Certificates``Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="b9a33-287">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="b9a33-288">모든 찾은 인증서를 개인 및 신뢰할 수 있는 루트 인증 기관에서 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-288">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="b9a33-289">IIS Express localhost 인증서를 제거 **하지** 마십시오.</span><span class="sxs-lookup"><span data-stu-id="b9a33-289">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="b9a33-290">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-290">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="b9a33-291">열려 있는 모든 브라우저 인스턴스를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-291">Close any browser instances open.</span></span> <span data-ttu-id="b9a33-292">앱에 대 한 새 브라우저 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-292">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="b9a33-293">OS X-인증서를 신뢰할 수 없음</span><span class="sxs-lookup"><span data-stu-id="b9a33-293">OS X - certificate not trusted</span></span>

* <span data-ttu-id="b9a33-294">키 집합 액세스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-294">Open KeyChain Access.</span></span>
* <span data-ttu-id="b9a33-295">시스템 키 집합을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-295">Select the System keychain.</span></span>
* <span data-ttu-id="b9a33-296">Localhost 인증서가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-296">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="b9a33-297">`+`모든 사용자가 신뢰할 수 있음을 나타내기 위해 아이콘에 기호가 포함 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-297">Check that it contains a `+` symbol on the icon to indicate it's trusted for all users.</span></span>
* <span data-ttu-id="b9a33-298">시스템 키 집합에서 인증서를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-298">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="b9a33-299">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-299">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="b9a33-300">열려 있는 모든 브라우저 인스턴스를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-300">Close any browser instances open.</span></span> <span data-ttu-id="b9a33-301">앱에 대 한 새 브라우저 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-301">Open a new browser window to app.</span></span>

<span data-ttu-id="b9a33-302">Visual Studio에서 인증서 문제를 해결 하려면 [IIS Express (dotnet/AspNetCore #16892)를 사용 하는 HTTPS 오류](https://github.com/dotnet/AspNetCore/issues/16892) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-302">See [HTTPS Error using IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="b9a33-303">Visual Studio에서 사용 되는 SSL 인증서 IIS Express</span><span class="sxs-lookup"><span data-stu-id="b9a33-303">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="b9a33-304">IIS Express 인증서의 문제를 해결 하려면 Visual Studio 설치 관리자에서 **복구** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-304">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span> <span data-ttu-id="b9a33-305">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/aspnetcore/issues/16892)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-305">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/16892).</span></span>

<a name="trust-ff"></a>

### <a name="firefox-sec_error_inadequate_key_usage-certificate-error"></a><span data-ttu-id="b9a33-306">Firefox SEC_ERROR_INADEQUATE_KEY_USAGE 인증서 오류</span><span class="sxs-lookup"><span data-stu-id="b9a33-306">Firefox SEC_ERROR_INADEQUATE_KEY_USAGE certificate error</span></span>

<span data-ttu-id="b9a33-307">Firefox 브라우저는 자체 인증서 저장소를 사용 하므로 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 또는 [kestrel](xref:fundamentals/servers/kestrel) 개발자 인증서를 신뢰 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-307">The Firefox browser uses it's own certificate store, and therefore doesn't trust the [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) or [Kestrel](xref:fundamentals/servers/kestrel) developer certificates.</span></span>

<span data-ttu-id="b9a33-308">IIS Express 또는 Kestrel과 함께 Firefox를 사용 하려면 다음을 설정 합니다.  `security.enterprise_roots.enabled` = `true`</span><span class="sxs-lookup"><span data-stu-id="b9a33-308">To use Firefox with IIS Express or Kestrel, set  `security.enterprise_roots.enabled` = `true`</span></span>

1. <span data-ttu-id="b9a33-309">`about:config`FireFox 브라우저에서를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-309">Enter `about:config` in the FireFox browser.</span></span>
1. <span data-ttu-id="b9a33-310">위험에 동의 하 고 위험을 수락 하는 경우 **계속을** 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9a33-310">Select **Accept the Risk and Continue** if you accept the risk.</span></span>
1. <span data-ttu-id="b9a33-311">**모두 표시** 선택</span><span class="sxs-lookup"><span data-stu-id="b9a33-311">Select **Show All**</span></span>
1. <span data-ttu-id="b9a33-312">설정 `security.enterprise_roots.enabled` = `true`</span><span class="sxs-lookup"><span data-stu-id="b9a33-312">Set `security.enterprise_roots.enabled` = `true`</span></span>
1. <span data-ttu-id="b9a33-313">Firefox 종료 및 다시 시작</span><span class="sxs-lookup"><span data-stu-id="b9a33-313">Exit and restart Firefox</span></span>

<span data-ttu-id="b9a33-314">자세한 내용은 [Firefox에서 ca (인증 기관) 설정](https://support.mozilla.org/kb/setting-certificate-authorities-firefox)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b9a33-314">For more information, see [Setting Up Certificate Authorities (CAs) in Firefox](https://support.mozilla.org/kb/setting-certificate-authorities-firefox).</span></span>

## <a name="additional-information"></a><span data-ttu-id="b9a33-315">추가 정보</span><span class="sxs-lookup"><span data-stu-id="b9a33-315">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="b9a33-316">Apache: HTTPS 구성을 사용 하 여 Linux의 호스트 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9a33-316">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="b9a33-317">Nginx를 사용 하 여 Linux에서 호스트 ASP.NET Core: HTTPS 구성</span><span class="sxs-lookup"><span data-stu-id="b9a33-317">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="b9a33-318">IIS에서 SSL을 설정하는 방법</span><span class="sxs-lookup"><span data-stu-id="b9a33-318">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="b9a33-319">OWASP HSTS 브라우저 지원</span><span class="sxs-lookup"><span data-stu-id="b9a33-319">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
