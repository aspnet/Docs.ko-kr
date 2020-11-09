---
title: 프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성
author: rick-anderson
description: 중요한 요청 정보를 종종 숨기는 프록시 서버 및 부하 분산 장치 뒤에 호스트되는 앱의 구성에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 461f6d2105d38c5dbea2f8cf479e027c2edede14
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057624"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="0741d-103">프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성</span><span class="sxs-lookup"><span data-stu-id="0741d-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="0741d-104">작성자: [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="0741d-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0741d-105">ASP.NET Core의 권장 구성에서 앱은 IIS/ASP.NET Core 모듈, Nginx 또는 Apache를 사용하여 호스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="0741d-106">프록시 서버, 부하 분산 장치 및 기타 네트워크 어플라이언스는 종종 앱에 도달하기 전에 요청에 대한 정보를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="0741d-107">HTTPS 요청이 HTTP를 통해 프록시된 경우 원래 스키마(HTTPS)가 손실되므로 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="0741d-108">앱은 프록시에서 요청을 수신하고 인터넷 또는 회사 네트워크의 실제 소스를 수신하는 것이 아니므로 원래 클라이언트 IP 주소도 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="0741d-109">이 정보는 요청 처리 시 중요할 수 있습니다(예: 리디렉션, 인증, 링크 생성, 정책 평가 및 클라이언트 지리적 위치).</span><span class="sxs-lookup"><span data-stu-id="0741d-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="0741d-110">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="0741d-110">Forwarded headers</span></span>

<span data-ttu-id="0741d-111">규칙에 따라 프록시는 HTTP 헤더에서 정보를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="0741d-112">헤더</span><span class="sxs-lookup"><span data-stu-id="0741d-112">Header</span></span> | <span data-ttu-id="0741d-113">설명</span><span class="sxs-lookup"><span data-stu-id="0741d-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="0741d-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="0741d-114">X-Forwarded-For</span></span> | <span data-ttu-id="0741d-115">요청 및 프록시 체인의 후속 프록시를 시작한 클라이언트에 대한 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="0741d-116">이 매개 변수에는 IP 주소(및 선택적으로 포트 번호)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="0741d-117">프록시 서버의 체인에서 첫 번째 매개 변수는 요청이 처음 만들어진 클라이언트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="0741d-118">그 뒤에 후속 프록시 식별자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="0741d-119">체인의 마지막 프록시는 매개 변수 목록에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="0741d-120">마지막 프록시의 IP 주소 및 선택적으로 포트 번호는 전송 계층에서 원격 IP 주소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="0741d-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="0741d-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="0741d-122">원래 체계(HTTP/HTTPS)의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="0741d-123">요청이 여러 프록시를 트래버스한 경우에는 값이 체계 목록일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="0741d-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="0741d-124">X-Forwarded-Host</span></span> | <span data-ttu-id="0741d-125">호스트 헤더 필드의 원래 값입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-125">The original value of the Host header field.</span></span> <span data-ttu-id="0741d-126">일반적으로 프록시는 호스트 헤더를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="0741d-127">프록시가 호스트 헤더를 유효성 검사하거나 알려진 정상 값으로 제한하지 않는 시스템에 영향을 미치는 권한 상승 취약성에 대한 자세한 내용은 [Microsoft 보안 공지 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="0741d-128">전달된 헤더 미들웨어(<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>)는 헤더를 읽고 <xref:Microsoft.AspNetCore.Http.HttpContext>의 관련 필드를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-128">The Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>), reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="0741d-129">미들웨어가 다음을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-129">The middleware updates:</span></span>

* <span data-ttu-id="0741d-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): `X-Forwarded-For` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="0741d-131">추가 설정은 미들웨어가 `RemoteIpAddress`를 설정하는 방법에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="0741d-132">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="0741d-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): `X-Forwarded-Proto` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="0741d-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): `X-Forwarded-Host` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="0741d-135">전달된 헤더 미들웨어 [기본 설정](#forwarded-headers-middleware-options)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="0741d-136">기본 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-136">The default settings are:</span></span>

* <span data-ttu-id="0741d-137">앱과 요청 소스 사이에는 ‘하나의 프록시’만 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="0741d-138">알려진 프록시 및 알려진 네트워크의 경우 루프백 주소만 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="0741d-139">전달된 헤더의 이름은 `X-Forwarded-For` 및 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="0741d-140">일부 네트워크 어플라이언스는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 추가하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="0741d-141">프록시된 요청이 앱에 도달할 때 이러한 헤더를 포함하지 않는 경우에는 어플라이언스 제조업체의 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="0741d-142">어플라이언스가 `X-Forwarded-For` 및 `X-Forwarded-Proto`와는 다른 헤더 이름을 사용하는 경우 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 어플라이언스에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="0741d-143">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 및 [다른 헤더 이름을 사용하는 프록시 구성](#configuration-for-a-proxy-that-uses-different-header-names)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="0741d-144">IIS/IIS Express 및 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="0741d-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="0741d-145">앱이 IIS 및 ASP.NET Core 모듈 뒤에서 호스트되는 [Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)인 경우 전달된 헤더 미들웨어가 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)에 의해 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="0741d-146">전달된 헤더 미들웨어는 전달된 헤더 관련 신뢰 문제(예: [IP 스푸핑](https://www.iplocation.net/ip-spoofing))로 인해 ASP.NET Core 모듈에 특정한 제한된 구성을 사용하여 미들웨어 파이프라인에서 첫 번째로 실행될 수 있도록 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="0741d-147">미들웨어는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 구성되고 단일 localhost 프록시로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="0741d-148">추가 구성이 필요한 경우 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="0741d-149">기타 프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="0741d-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="0741d-150">[Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)를 호스트할 때 [IIS 통합](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 사용 외에는 전달된 헤더 미들웨어가 기본적으로 사용하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="0741d-151">앱에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 전달된 헤더를 처리하려면 전달된 헤더 미들웨어를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="0741d-152">미들웨어를 사용하도록 설정한 후 미들웨어에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않은 경우 기본 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="0741d-153"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 사용하여 `Startup.ConfigureServices`의 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="0741d-154">전달된 헤더 미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="0741d-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="0741d-155">전달된 헤더 미들웨어는 다른 미들웨어보다 먼저 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="0741d-156">이 순서를 지정하면 전달된 헤더 정보에 따라 달라지는 미들웨어는 처리하기 위해 헤더 값을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="0741d-157">전달된 헤더 미들웨어는 진단 및 오류 처리 뒤에 실행될 수 있지만 `UseHsts`를 호출하기 전에 실행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="0741d-158">또는 진단 전에 `UseForwardedHeaders`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="0741d-159">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않거나 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 확장 메서드에 직접 지정하지 않은 경우 전달할 기본 헤더는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="0741d-160">전달할 헤더를 사용하여 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="0741d-161">Nginx 구성</span><span class="sxs-lookup"><span data-stu-id="0741d-161">Nginx configuration</span></span>

<span data-ttu-id="0741d-162">`X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하려면 <xref:host-and-deploy/linux-nginx#configure-nginx>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="0741d-163">자세한 내용은 [NGINX: 전달된 헤더 사용](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="0741d-164">Apache 구성</span><span class="sxs-lookup"><span data-stu-id="0741d-164">Apache configuration</span></span>

<span data-ttu-id="0741d-165">`X-Forwarded-For`가 자동으로 추가됩니다( [Apache 모듈 mod_proxy: 역방향 프록시 요청 헤더](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) 참조).</span><span class="sxs-lookup"><span data-stu-id="0741d-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="0741d-166">`X-Forwarded-Proto` 헤더를 전달하는 방법에 대한 내용은 <xref:host-and-deploy/linux-apache#configure-apache>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="0741d-167">전달된 헤더 미들웨어 옵션</span><span class="sxs-lookup"><span data-stu-id="0741d-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="0741d-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>는 전달된 헤더 미들웨어의 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="0741d-169">다음 예제에서는 기본값을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-169">The following example changes the default values:</span></span>

* <span data-ttu-id="0741d-170">전달된 헤더의 항목 수를 `2`로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="0741d-171">`127.0.10.1`의 알려진 프록시 주소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="0741d-172">전달된 헤더 이름을 기본값 `X-Forwarded-For`에서 `X-Forwarded-For-My-Custom-Header-Name`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="0741d-173">옵션</span><span class="sxs-lookup"><span data-stu-id="0741d-173">Option</span></span> | <span data-ttu-id="0741d-174">설명</span><span class="sxs-lookup"><span data-stu-id="0741d-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="0741d-175">`X-Forwarded-Host` 헤더에 의한 호스트를 제공된 값으로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="0741d-176">값은 ordinal-ignore-case를 사용하여 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="0741d-177">포트 번호는 제외해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="0741d-178">목록이 비어 있으면 모든 호스트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="0741d-179">최상위 수준 와일드카드 `*`는 비어 있지 않은 모든 호스트를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="0741d-180">하위 도메인 와일드카드는 허용되지만 루트 도메인과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="0741d-181">예를 들어 `*.contoso.com`은 하위 도메인 `foo.contoso.com`과 일치하지만 루트 도메인 `contoso.com`과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="0741d-182">유니코드 호스트 이름은 허용되지만 일치시킬 [Punycode](https://tools.ietf.org/html/rfc3492)로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="0741d-183">[IPv6 주소](https://tools.ietf.org/html/rfc4291)는 경계 대괄호를 포함하고 [기존 형식](https://tools.ietf.org/html/rfc4291#section-2.2)이어야 합니다(예: `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="0741d-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="0741d-184">IPv6 주소는 서로 다른 형식 간에 논리적 같음을 확인하기 위해 특별히 처리되지 않으며 정규화가 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="0741d-185">허용된 호스트를 제한하지 못하면 공격자가 서비스에서 생성된 링크를 스푸핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="0741d-186">기본값은 빈 `IList<string>`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="0741d-187">[ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="0741d-188">이 옵션은 프록시/전달자가 `X-Forwarded-For` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="0741d-189">기본값은 `X-Forwarded-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="0741d-190">처리해야 할 전달자를 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="0741d-191">적용되는 필드 목록은 [ForwardedHeaders 열거형](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="0741d-192">이 속성에 할당된 일반적인 값은 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="0741d-193">기본값은 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="0741d-194">[ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="0741d-195">이 옵션은 프록시/전달자가 `X-Forwarded-Host` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="0741d-196">기본값은 `X-Forwarded-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="0741d-197">[ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="0741d-198">이 옵션은 프록시/전달자가 `X-Forwarded-Proto` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="0741d-199">기본값은 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="0741d-200">처리되는 헤더의 항목 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="0741d-201">제한을 사용하지 않도록 `null`로 설정하지만, `KnownProxies` 또는 `KnownNetworks`가 구성된 경우에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="0741d-202">비-`null` 값을 설정하면 잘못 구성된 프록시 및 네트워크에서 측면 채널에서 오는 악성 요청을 예방하지만 보증하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="0741d-203">전달된 헤더 미들웨어는 헤더를 역순으로 오른쪽에서 왼쪽으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="0741d-204">기본값(`1`)만 사용된다면 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="0741d-205">기본값은 `1`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="0741d-206">전달된 헤더를 허용하기 위한 알려진 네트워크의 주소 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="0741d-207">CIDR(Classless Interdomain Routing) 표기법을 사용하여 IP 범위를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="0741d-208">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="0741d-209">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="0741d-210">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="0741d-211">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-211">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="0741d-212">기본값은 `IPAddress.Loopback`에 대한 단일 항목을 포함하는 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>>입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="0741d-213">전달된 헤더를 허용하기 위한 알려진 프록시의 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="0741d-214">`KnownProxies`를 사용하여 정확한 IP 주소 일치 항목을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="0741d-215">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="0741d-216">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="0741d-217">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="0741d-218">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-218">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="0741d-219">기본값은 `IPAddress.IPv6Loopback`에 대한 단일 항목을 포함하는 `IList`\<<xref:System.Net.IPAddress>>입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-219">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="0741d-220">[ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="0741d-221">기본값은 `X-Original-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-221">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="0741d-222">[ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="0741d-223">기본값은 `X-Original-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-223">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="0741d-224">[ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-224">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="0741d-225">기본값은 `X-Original-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-225">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="0741d-226">처리 중인 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 간에 동기화할 헤더 값 수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-226">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="0741d-227">ASP.NET Core 1.x의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-227">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="0741d-228">ASP.NET Core 2.0 이상의 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-228">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="0741d-229">시나리오 및 사용 사례</span><span class="sxs-lookup"><span data-stu-id="0741d-229">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="0741d-230">전달된 헤더를 추가할 수 없고 모든 요청이 안전한 경우</span><span class="sxs-lookup"><span data-stu-id="0741d-230">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="0741d-231">일부 경우에는 앱으로 프록시된 요청에 전달된 헤더를 추가할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-231">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="0741d-232">모든 공용 외부 요청이 HTTPS가 되도록 프록시가 적용되는 경우 미들웨어를 사용하기 전에 `Startup.Configure`에서 체계를 수동으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-232">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="0741d-233">이 코드는 개발 또는 스테이징 환경에서 환경 변수 또는 기타 구성 설정을 통해 사용하지 않도록 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-233">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="0741d-234">요청 경로를 변경하는 프록시 및 경로 기준을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-234">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="0741d-235">일부 프록시는 경로를 그대로 전달하지만 라우팅이 제대로 작동하도록 제거해야 하는 앱 기본 경로를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-235">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="0741d-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 미들웨어는 경로를 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)로 분할하고 앱 기본 경로를 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="0741d-237">`/foo`가 `/foo/api/1`로 전달되는 프록시 경로의 앱 기본 경로인 경우 미들웨어는 다음 명령을 사용하여 `Request.PathBase`를 `/foo`로 설정하고 `Request.Path`를 `/api/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-237">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="0741d-238">미들웨어가 역방향으로 다시 호출되면 원래 경로 및 경로 기준이 다시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-238">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="0741d-239">미들웨어 순서 처리에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-239">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="0741d-240">프록시가 경로를 자르는 경우(예: `/foo/api/1`을 `/api/1`에 전달) 요청의 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 속성을 설정하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-240">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="0741d-241">프록시가 경로 데이터를 추가하는 경우 경로의 일부를 삭제한 다음, <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*>를 사용하고 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 속성에 할당하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-241">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="0741d-242">다른 헤더 이름을 사용하는 프록시에 대한 구성</span><span class="sxs-lookup"><span data-stu-id="0741d-242">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="0741d-243">프록시가 `X-Forwarded-For` 및 `X-Forwarded-Proto`라는 헤더를 사용하여 프록시 주소/포트 및 원래 체계 정보를 전달하지 않는 경우, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 프록시에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-243">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="0741d-244">IPv6 주소로 표시되는 IPv4 주소 구성</span><span class="sxs-lookup"><span data-stu-id="0741d-244">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="0741d-245">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1` 또는 `::ffff:a00:1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-245">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="0741d-246">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-246">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="0741d-247">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-247">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="0741d-248">다음 예제에서는 전달된 헤더를 제공하는 네트워크 주소가 IPv6 형식의 `KnownNetworks` 목록에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-248">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="0741d-249">IPv4 주소: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="0741d-249">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="0741d-250">변환된 IPv6 주소: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="0741d-250">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="0741d-251">변환된 접두사 길이: 104</span><span class="sxs-lookup"><span data-stu-id="0741d-251">Converted prefix length: 104</span></span>

<span data-ttu-id="0741d-252">16진수 형식으로 주소를 제공할 수도 있습니다(IPv6에서 표시된 `10.11.12.1`을 `::ffff:0a0b:0c01`로 표시).</span><span class="sxs-lookup"><span data-stu-id="0741d-252">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="0741d-253">IPv4 주소를 IPv6로 변환할 때 CIDR 접두사 길이(예제에서는 `8`)에 96을 추가하여 추가 `::ffff:` IPv6 접두사를 처리합니다(8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="0741d-253">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="0741d-254">Linux용 스키마 및 비 IIS 역방향 프록시 전달</span><span class="sxs-lookup"><span data-stu-id="0741d-254">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="0741d-255"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 및 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>을(를) 호출하는 앱은 Azure Linux App Service, Azure Linux VM(가상 머신) 또는 IIS 이외의 다른 역방향 프록시 뒤에 배포된 경우 사이트를 무한 루프에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-255">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="0741d-256">TLS는 역방향 프록시에 의해 종료되며, Kestrel은 올바른 요청 체계를 인식하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-256">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="0741d-257">OAuth 및 OIDC도 잘못된 리디렉션을 생성하므로 이 구성에서 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-257">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="0741d-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>은 IIS 뒤에서 실행되는 경우 전달된 헤더 미들웨어를 추가하고 구성하지만 Linux에 대한 일치하는 자동 구성이 없습니다(Apache 또는 Nginx 통합).</span><span class="sxs-lookup"><span data-stu-id="0741d-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="0741d-259">비 IIS 시나리오에서 프록시의 스키마를 전달하려면 전달된 헤더 미들웨어를 추가하고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-259">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="0741d-260">`Startup.ConfigureServices`에서 다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-260">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="0741d-261">인증서 전달</span><span class="sxs-lookup"><span data-stu-id="0741d-261">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="0741d-262">Azure</span><span class="sxs-lookup"><span data-stu-id="0741d-262">Azure</span></span>

<span data-ttu-id="0741d-263">인증서 전달에 대한 Azure App Service를 구성하려면 [Azure App Service에 대한 TLS 상호 인증 구성](/azure/app-service/app-service-web-configure-tls-mutual-auth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-263">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="0741d-264">다음 지침은 ASP.NET Core 앱 구성과 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-264">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="0741d-265">`Startup.Configure`에서 `app.UseAuthentication();` 호출 앞에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-265">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="0741d-266">인증서 전달 미들웨어를 구성하여 Azure를 사용하는 헤더 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-266">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="0741d-267">`Startup.ConfigureServices`에서 다음 코드를 추가하여 미들웨어가 인증서를 빌드하는 헤더를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-267">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="0741d-268">다른 웹 프록시</span><span class="sxs-lookup"><span data-stu-id="0741d-268">Other web proxies</span></span>

<span data-ttu-id="0741d-269">IIS 또는 Azure App Service의 ARR(애플리케이션 요청 라우팅)이 아닌 프록시를 사용하는 경우 HTTP 헤더에서 받은 인증서를 전달하도록 프록시를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-269">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="0741d-270">`Startup.Configure`에서 `app.UseAuthentication();` 호출 앞에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-270">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="0741d-271">인증서 전달 미들웨어를 구성하여 헤더 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-271">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="0741d-272">`Startup.ConfigureServices`에서 다음 코드를 추가하여 미들웨어가 인증서를 빌드하는 헤더를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-272">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="0741d-273">프록시가 Nginx에서와 같이 인증서를 base64 인코딩하지 않는 경우 `HeaderConverter` 옵션을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-273">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="0741d-274">`Startup.ConfigureServices`에서 다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-274">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="0741d-275">문제 해결</span><span class="sxs-lookup"><span data-stu-id="0741d-275">Troubleshoot</span></span>

<span data-ttu-id="0741d-276">헤더가 예상대로 전달되지 않으면 [로깅](xref:fundamentals/logging/index)을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-276">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="0741d-277">로그가 문제를 해결하기에 충분한 정보를 제공하지 않으면 서버가 수신하는 요청 헤더를 열거합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-277">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="0741d-278">인라인 미들웨어를 사용하여 앱 응답에 요청 헤더를 쓰거나 헤더를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-278">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="0741d-279">앱 응답에 헤더를 기록하려면 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 호출 바로 뒤에 다음 터미널 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-279">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="0741d-280">응답 본문 대신 로그에 기록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-280">You can write to logs instead of the response body.</span></span> <span data-ttu-id="0741d-281">로그에 기록하면 디버깅 중 사이트가 정상적으로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-281">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="0741d-282">응답 본문이 아닌 로그를 기록하려면:</span><span class="sxs-lookup"><span data-stu-id="0741d-282">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="0741d-283">[시작 시 로그 만들기](xref:fundamentals/logging/index#create-logs-in-startup)에 설명된 대로 `ILogger<Startup>`을 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-283">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="0741d-284">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 호출 바로 뒤에 다음 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-284">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="0741d-285">처리 시 `X-Forwarded-{For|Proto|Host}` 값이 `X-Original-{For|Proto|Host}`로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-285">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="0741d-286">제공된 헤더에 여러 값이 있는 경우 전달된 헤더 미들웨어는 오른쪽에서 왼쪽으로 역순으로 헤더를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-286">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="0741d-287">기본 `ForwardLimit`는 `1`(일)이므로 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-287">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="0741d-288">요청의 원래 원격 IP는 전달된 헤더가 처리되기 전에 `KnownProxies` 또는 `KnownNetworks` 목록의 항목과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-288">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="0741d-289">이렇게 하면 신뢰할 수 없는 프록시에서 전달자가 허용되지 않아 헤더 스푸핑이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-289">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="0741d-290">알 수 없는 프록시가 검색되면 로그에 프록시 주소가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-290">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="0741d-291">앞의 예제에서 10.0.0.100은 프록시 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-291">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="0741d-292">서버가 신뢰할 수 있는 프록시인 경우 서버의 IP 주소를 `Startup.ConfigureServices`의 `KnownProxies`에 추가합니다(또는 신뢰할 수 있는 네트워크를 `KnownNetworks`에 추가).</span><span class="sxs-lookup"><span data-stu-id="0741d-292">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0741d-293">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-293">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="0741d-294">신뢰할 수 있는 프록시 및 네트워크만 헤더를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-294">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="0741d-295">그렇지 않을 경우 [IP 스푸핑](https://www.iplocation.net/ip-spoofing) 공격이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-295">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0741d-296">추가 자료</span><span class="sxs-lookup"><span data-stu-id="0741d-296">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="0741d-297">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)(Microsoft 보안 공지 CVE-2018-0787: ASP.NET Core 권한 상승 취약성)</span><span class="sxs-lookup"><span data-stu-id="0741d-297">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0741d-298">ASP.NET Core의 권장 구성에서 앱은 IIS/ASP.NET Core 모듈, Nginx 또는 Apache를 사용하여 호스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-298">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="0741d-299">프록시 서버, 부하 분산 장치 및 기타 네트워크 어플라이언스는 종종 앱에 도달하기 전에 요청에 대한 정보를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-299">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="0741d-300">HTTPS 요청이 HTTP를 통해 프록시된 경우 원래 스키마(HTTPS)가 손실되므로 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-300">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="0741d-301">앱은 프록시에서 요청을 수신하고 인터넷 또는 회사 네트워크의 실제 소스를 수신하는 것이 아니므로 원래 클라이언트 IP 주소도 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-301">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="0741d-302">이 정보는 요청 처리 시 중요할 수 있습니다(예: 리디렉션, 인증, 링크 생성, 정책 평가 및 클라이언트 지리적 위치).</span><span class="sxs-lookup"><span data-stu-id="0741d-302">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="0741d-303">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="0741d-303">Forwarded headers</span></span>

<span data-ttu-id="0741d-304">규칙에 따라 프록시는 HTTP 헤더에서 정보를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-304">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="0741d-305">헤더</span><span class="sxs-lookup"><span data-stu-id="0741d-305">Header</span></span> | <span data-ttu-id="0741d-306">설명</span><span class="sxs-lookup"><span data-stu-id="0741d-306">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="0741d-307">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="0741d-307">X-Forwarded-For</span></span> | <span data-ttu-id="0741d-308">요청 및 프록시 체인의 후속 프록시를 시작한 클라이언트에 대한 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-308">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="0741d-309">이 매개 변수에는 IP 주소(및 선택적으로 포트 번호)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-309">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="0741d-310">프록시 서버의 체인에서 첫 번째 매개 변수는 요청이 처음 만들어진 클라이언트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-310">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="0741d-311">그 뒤에 후속 프록시 식별자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-311">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="0741d-312">체인의 마지막 프록시는 매개 변수 목록에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-312">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="0741d-313">마지막 프록시의 IP 주소 및 선택적으로 포트 번호는 전송 계층에서 원격 IP 주소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-313">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="0741d-314">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="0741d-314">X-Forwarded-Proto</span></span> | <span data-ttu-id="0741d-315">원래 체계(HTTP/HTTPS)의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-315">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="0741d-316">요청이 여러 프록시를 트래버스한 경우에는 값이 체계 목록일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-316">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="0741d-317">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="0741d-317">X-Forwarded-Host</span></span> | <span data-ttu-id="0741d-318">호스트 헤더 필드의 원래 값입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-318">The original value of the Host header field.</span></span> <span data-ttu-id="0741d-319">일반적으로 프록시는 호스트 헤더를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-319">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="0741d-320">프록시가 호스트 헤더를 유효성 검사하거나 알려진 정상 값으로 제한하지 않는 시스템에 영향을 미치는 권한 상승 취약성에 대한 자세한 내용은 [Microsoft 보안 공지 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-320">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="0741d-321">[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 패키지의 전달된 헤더 미들웨어는 헤더를 읽고 <xref:Microsoft.AspNetCore.Http.HttpContext>의 관련 필드를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-321">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="0741d-322">미들웨어가 다음을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-322">The middleware updates:</span></span>

* <span data-ttu-id="0741d-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): `X-Forwarded-For` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="0741d-324">추가 설정은 미들웨어가 `RemoteIpAddress`를 설정하는 방법에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-324">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="0741d-325">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-325">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="0741d-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): `X-Forwarded-Proto` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="0741d-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): `X-Forwarded-Host` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="0741d-328">전달된 헤더 미들웨어 [기본 설정](#forwarded-headers-middleware-options)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-328">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="0741d-329">기본 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-329">The default settings are:</span></span>

* <span data-ttu-id="0741d-330">앱과 요청 소스 사이에는 ‘하나의 프록시’만 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-330">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="0741d-331">알려진 프록시 및 알려진 네트워크의 경우 루프백 주소만 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-331">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="0741d-332">전달된 헤더의 이름은 `X-Forwarded-For` 및 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-332">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="0741d-333">일부 네트워크 어플라이언스는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 추가하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-333">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="0741d-334">프록시된 요청이 앱에 도달할 때 이러한 헤더를 포함하지 않는 경우에는 어플라이언스 제조업체의 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-334">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="0741d-335">어플라이언스가 `X-Forwarded-For` 및 `X-Forwarded-Proto`와는 다른 헤더 이름을 사용하는 경우 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 어플라이언스에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-335">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="0741d-336">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 및 [다른 헤더 이름을 사용하는 프록시 구성](#configuration-for-a-proxy-that-uses-different-header-names)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-336">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="0741d-337">IIS/IIS Express 및 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="0741d-337">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="0741d-338">앱이 IIS 및 ASP.NET Core 모듈 뒤에서 호스트되는 [Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)인 경우 전달된 헤더 미들웨어가 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)에 의해 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-338">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="0741d-339">전달된 헤더 미들웨어는 전달된 헤더 관련 신뢰 문제(예: [IP 스푸핑](https://www.iplocation.net/ip-spoofing))로 인해 ASP.NET Core 모듈에 특정한 제한된 구성을 사용하여 미들웨어 파이프라인에서 첫 번째로 실행될 수 있도록 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-339">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="0741d-340">미들웨어는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 구성되고 단일 localhost 프록시로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-340">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="0741d-341">추가 구성이 필요한 경우 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-341">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="0741d-342">기타 프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="0741d-342">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="0741d-343">[Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)를 호스트할 때 [IIS 통합](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 사용 외에는 전달된 헤더 미들웨어가 기본적으로 사용하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-343">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="0741d-344">앱에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 전달된 헤더를 처리하려면 전달된 헤더 미들웨어를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-344">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="0741d-345">미들웨어를 사용하도록 설정한 후 미들웨어에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않은 경우 기본 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-345">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="0741d-346"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 사용하여 `Startup.ConfigureServices`의 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-346">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0741d-347">다른 미들웨어를 호출하기 전에 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-347">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="0741d-348">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않거나 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 확장 메서드에 직접 지정하지 않은 경우 전달할 기본 헤더는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-348">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="0741d-349">전달할 헤더를 사용하여 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-349">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="0741d-350">Nginx 구성</span><span class="sxs-lookup"><span data-stu-id="0741d-350">Nginx configuration</span></span>

<span data-ttu-id="0741d-351">`X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하려면 <xref:host-and-deploy/linux-nginx#configure-nginx>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-351">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="0741d-352">자세한 내용은 [NGINX: 전달된 헤더 사용](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-352">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="0741d-353">Apache 구성</span><span class="sxs-lookup"><span data-stu-id="0741d-353">Apache configuration</span></span>

<span data-ttu-id="0741d-354">`X-Forwarded-For`가 자동으로 추가됩니다( [Apache 모듈 mod_proxy: 역방향 프록시 요청 헤더](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) 참조).</span><span class="sxs-lookup"><span data-stu-id="0741d-354">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="0741d-355">`X-Forwarded-Proto` 헤더를 전달하는 방법에 대한 내용은 <xref:host-and-deploy/linux-apache#configure-apache>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-355">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="0741d-356">전달된 헤더 미들웨어 옵션</span><span class="sxs-lookup"><span data-stu-id="0741d-356">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="0741d-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>는 전달된 헤더 미들웨어의 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="0741d-358">다음 예제에서는 기본값을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-358">The following example changes the default values:</span></span>

* <span data-ttu-id="0741d-359">전달된 헤더의 항목 수를 `2`로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-359">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="0741d-360">`127.0.10.1`의 알려진 프록시 주소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-360">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="0741d-361">전달된 헤더 이름을 기본값 `X-Forwarded-For`에서 `X-Forwarded-For-My-Custom-Header-Name`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-361">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="0741d-362">옵션</span><span class="sxs-lookup"><span data-stu-id="0741d-362">Option</span></span> | <span data-ttu-id="0741d-363">설명</span><span class="sxs-lookup"><span data-stu-id="0741d-363">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="0741d-364">`X-Forwarded-Host` 헤더에 의한 호스트를 제공된 값으로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-364">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="0741d-365">값은 ordinal-ignore-case를 사용하여 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-365">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="0741d-366">포트 번호는 제외해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-366">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="0741d-367">목록이 비어 있으면 모든 호스트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-367">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="0741d-368">최상위 수준 와일드카드 `*`는 비어 있지 않은 모든 호스트를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-368">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="0741d-369">하위 도메인 와일드카드는 허용되지만 루트 도메인과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-369">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="0741d-370">예를 들어 `*.contoso.com`은 하위 도메인 `foo.contoso.com`과 일치하지만 루트 도메인 `contoso.com`과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-370">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="0741d-371">유니코드 호스트 이름은 허용되지만 일치시킬 [Punycode](https://tools.ietf.org/html/rfc3492)로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-371">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="0741d-372">[IPv6 주소](https://tools.ietf.org/html/rfc4291)는 경계 대괄호를 포함하고 [기존 형식](https://tools.ietf.org/html/rfc4291#section-2.2)이어야 합니다(예: `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="0741d-372">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="0741d-373">IPv6 주소는 서로 다른 형식 간에 논리적 같음을 확인하기 위해 특별히 처리되지 않으며 정규화가 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-373">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="0741d-374">허용된 호스트를 제한하지 못하면 공격자가 서비스에서 생성된 링크를 스푸핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-374">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="0741d-375">기본값은 빈 `IList<string>`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-375">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="0741d-376">[ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-376">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="0741d-377">이 옵션은 프록시/전달자가 `X-Forwarded-For` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-377">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="0741d-378">기본값은 `X-Forwarded-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-378">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="0741d-379">처리해야 할 전달자를 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-379">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="0741d-380">적용되는 필드 목록은 [ForwardedHeaders 열거형](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-380">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="0741d-381">이 속성에 할당된 일반적인 값은 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-381">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="0741d-382">기본값은 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-382">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="0741d-383">[ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-383">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="0741d-384">이 옵션은 프록시/전달자가 `X-Forwarded-Host` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-384">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="0741d-385">기본값은 `X-Forwarded-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-385">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="0741d-386">[ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-386">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="0741d-387">이 옵션은 프록시/전달자가 `X-Forwarded-Proto` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-387">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="0741d-388">기본값은 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-388">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="0741d-389">처리되는 헤더의 항목 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-389">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="0741d-390">제한을 사용하지 않도록 `null`로 설정하지만, `KnownProxies` 또는 `KnownNetworks`가 구성된 경우에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-390">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="0741d-391">비-`null` 값을 설정하면 잘못 구성된 프록시 및 네트워크에서 측면 채널에서 오는 악성 요청을 예방하지만 보증하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-391">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="0741d-392">전달된 헤더 미들웨어는 헤더를 역순으로 오른쪽에서 왼쪽으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-392">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="0741d-393">기본값(`1`)만 사용된다면 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-393">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="0741d-394">기본값은 `1`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-394">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="0741d-395">전달된 헤더를 허용하기 위한 알려진 네트워크의 주소 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-395">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="0741d-396">CIDR(Classless Interdomain Routing) 표기법을 사용하여 IP 범위를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-396">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="0741d-397">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-397">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="0741d-398">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-398">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="0741d-399">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-399">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="0741d-400">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-400">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="0741d-401">기본값은 `IPAddress.Loopback`에 대한 단일 항목을 포함하는 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>>입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-401">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="0741d-402">전달된 헤더를 허용하기 위한 알려진 프록시의 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-402">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="0741d-403">`KnownProxies`를 사용하여 정확한 IP 주소 일치 항목을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-403">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="0741d-404">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-404">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="0741d-405">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-405">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="0741d-406">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-406">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="0741d-407">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-407">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="0741d-408">기본값은 `IPAddress.IPv6Loopback`에 대한 단일 항목을 포함하는 `IList`\<<xref:System.Net.IPAddress>>입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-408">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="0741d-409">[ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="0741d-410">기본값은 `X-Original-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-410">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="0741d-411">[ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-411">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="0741d-412">기본값은 `X-Original-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-412">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="0741d-413">[ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-413">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="0741d-414">기본값은 `X-Original-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-414">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="0741d-415">처리 중인 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 간에 동기화할 헤더 값 수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-415">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="0741d-416">ASP.NET Core 1.x의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-416">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="0741d-417">ASP.NET Core 2.0 이상의 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-417">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="0741d-418">시나리오 및 사용 사례</span><span class="sxs-lookup"><span data-stu-id="0741d-418">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="0741d-419">전달된 헤더를 추가할 수 없고 모든 요청이 안전한 경우</span><span class="sxs-lookup"><span data-stu-id="0741d-419">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="0741d-420">일부 경우에는 앱으로 프록시된 요청에 전달된 헤더를 추가할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-420">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="0741d-421">모든 공용 외부 요청이 HTTPS가 되도록 프록시가 적용되는 경우 미들웨어를 사용하기 전에 `Startup.Configure`에서 체계를 수동으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-421">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="0741d-422">이 코드는 개발 또는 스테이징 환경에서 환경 변수 또는 기타 구성 설정을 통해 사용하지 않도록 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-422">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="0741d-423">요청 경로를 변경하는 프록시 및 경로 기준을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-423">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="0741d-424">일부 프록시는 경로를 그대로 전달하지만 라우팅이 제대로 작동하도록 제거해야 하는 앱 기본 경로를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-424">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="0741d-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 미들웨어는 경로를 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)로 분할하고 앱 기본 경로를 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="0741d-426">`/foo`가 `/foo/api/1`로 전달되는 프록시 경로의 앱 기본 경로인 경우 미들웨어는 다음 명령을 사용하여 `Request.PathBase`를 `/foo`로 설정하고 `Request.Path`를 `/api/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-426">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="0741d-427">미들웨어가 역방향으로 다시 호출되면 원래 경로 및 경로 기준이 다시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-427">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="0741d-428">미들웨어 순서 처리에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-428">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="0741d-429">프록시가 경로를 자르는 경우(예: `/foo/api/1`을 `/api/1`에 전달) 요청의 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 속성을 설정하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-429">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="0741d-430">프록시가 경로 데이터를 추가하는 경우 경로의 일부를 삭제한 다음, <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*>를 사용하고 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 속성에 할당하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-430">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="0741d-431">다른 헤더 이름을 사용하는 프록시에 대한 구성</span><span class="sxs-lookup"><span data-stu-id="0741d-431">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="0741d-432">프록시가 `X-Forwarded-For` 및 `X-Forwarded-Proto`라는 헤더를 사용하여 프록시 주소/포트 및 원래 체계 정보를 전달하지 않는 경우, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 프록시에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-432">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="0741d-433">IPv6 주소로 표시되는 IPv4 주소 구성</span><span class="sxs-lookup"><span data-stu-id="0741d-433">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="0741d-434">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1` 또는 `::ffff:a00:1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-434">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="0741d-435">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-435">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="0741d-436">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-436">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="0741d-437">다음 예제에서는 전달된 헤더를 제공하는 네트워크 주소가 IPv6 형식의 `KnownNetworks` 목록에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-437">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="0741d-438">IPv4 주소: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="0741d-438">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="0741d-439">변환된 IPv6 주소: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="0741d-439">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="0741d-440">변환된 접두사 길이: 104</span><span class="sxs-lookup"><span data-stu-id="0741d-440">Converted prefix length: 104</span></span>

<span data-ttu-id="0741d-441">16진수 형식으로 주소를 제공할 수도 있습니다(IPv6에서 표시된 `10.11.12.1`을 `::ffff:0a0b:0c01`로 표시).</span><span class="sxs-lookup"><span data-stu-id="0741d-441">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="0741d-442">IPv4 주소를 IPv6로 변환할 때 CIDR 접두사 길이(예제에서는 `8`)에 96을 추가하여 추가 `::ffff:` IPv6 접두사를 처리합니다(8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="0741d-442">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="0741d-443">Linux용 스키마 및 비 IIS 역방향 프록시 전달</span><span class="sxs-lookup"><span data-stu-id="0741d-443">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="0741d-444"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 및 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>을(를) 호출하는 앱은 Azure Linux App Service, Azure Linux VM(가상 머신) 또는 IIS 이외의 다른 역방향 프록시 뒤에 배포된 경우 사이트를 무한 루프에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-444">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="0741d-445">TLS는 역방향 프록시에 의해 종료되며, Kestrel은 올바른 요청 체계를 인식하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-445">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="0741d-446">OAuth 및 OIDC도 잘못된 리디렉션을 생성하므로 이 구성에서 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-446">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="0741d-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>은 IIS 뒤에서 실행되는 경우 전달된 헤더 미들웨어를 추가하고 구성하지만 Linux에 대한 일치하는 자동 구성이 없습니다(Apache 또는 Nginx 통합).</span><span class="sxs-lookup"><span data-stu-id="0741d-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="0741d-448">비 IIS 시나리오에서 프록시의 스키마를 전달하려면 전달된 헤더 미들웨어를 추가하고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-448">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="0741d-449">`Startup.ConfigureServices`에서 다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-449">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="0741d-450">문제 해결</span><span class="sxs-lookup"><span data-stu-id="0741d-450">Troubleshoot</span></span>

<span data-ttu-id="0741d-451">헤더가 예상대로 전달되지 않으면 [로깅](xref:fundamentals/logging/index)을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-451">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="0741d-452">로그가 문제를 해결하기에 충분한 정보를 제공하지 않으면 서버가 수신하는 요청 헤더를 열거합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-452">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="0741d-453">인라인 미들웨어를 사용하여 앱 응답에 요청 헤더를 쓰거나 헤더를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-453">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="0741d-454">앱 응답에 헤더를 기록하려면 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 호출 바로 뒤에 다음 터미널 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-454">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="0741d-455">응답 본문 대신 로그에 기록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-455">You can write to logs instead of the response body.</span></span> <span data-ttu-id="0741d-456">로그에 기록하면 디버깅 중 사이트가 정상적으로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-456">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="0741d-457">응답 본문이 아닌 로그를 기록하려면:</span><span class="sxs-lookup"><span data-stu-id="0741d-457">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="0741d-458">[시작 시 로그 만들기](xref:fundamentals/logging/index#create-logs-in-startup)에 설명된 대로 `ILogger<Startup>`을 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-458">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="0741d-459">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 호출 바로 뒤에 다음 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-459">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="0741d-460">처리 시 `X-Forwarded-{For|Proto|Host}` 값이 `X-Original-{For|Proto|Host}`로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-460">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="0741d-461">제공된 헤더에 여러 값이 있는 경우 전달된 헤더 미들웨어는 오른쪽에서 왼쪽으로 역순으로 헤더를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-461">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="0741d-462">기본 `ForwardLimit`는 `1`(일)이므로 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-462">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="0741d-463">요청의 원래 원격 IP는 전달된 헤더가 처리되기 전에 `KnownProxies` 또는 `KnownNetworks` 목록의 항목과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-463">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="0741d-464">이렇게 하면 신뢰할 수 없는 프록시에서 전달자가 허용되지 않아 헤더 스푸핑이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-464">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="0741d-465">알 수 없는 프록시가 검색되면 로그에 프록시 주소가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-465">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="0741d-466">앞의 예제에서 10.0.0.100은 프록시 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-466">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="0741d-467">서버가 신뢰할 수 있는 프록시인 경우 서버의 IP 주소를 `Startup.ConfigureServices`의 `KnownProxies`에 추가합니다(또는 신뢰할 수 있는 네트워크를 `KnownNetworks`에 추가).</span><span class="sxs-lookup"><span data-stu-id="0741d-467">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0741d-468">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0741d-468">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="0741d-469">신뢰할 수 있는 프록시 및 네트워크만 헤더를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-469">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="0741d-470">그렇지 않을 경우 [IP 스푸핑](https://www.iplocation.net/ip-spoofing) 공격이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="0741d-470">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0741d-471">추가 자료</span><span class="sxs-lookup"><span data-stu-id="0741d-471">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="0741d-472">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)(Microsoft 보안 공지 CVE-2018-0787: ASP.NET Core 권한 상승 취약성)</span><span class="sxs-lookup"><span data-stu-id="0741d-472">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
