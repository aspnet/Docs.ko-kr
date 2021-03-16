---
title: 프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성
author: rick-anderson
description: 중요한 요청 정보를 종종 숨기는 프록시 서버 및 부하 분산 장치 뒤에 호스트되는 앱의 구성에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 28a802414fd59f684a56e2b735140438d33be740
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109951"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="a16c4-103">프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성</span><span class="sxs-lookup"><span data-stu-id="a16c4-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="a16c4-104">작성자: [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="a16c4-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a16c4-105">ASP.NET Core의 권장 구성에서 앱은 IIS/ASP.NET Core 모듈, Nginx 또는 Apache를 사용하여 호스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="a16c4-106">프록시 서버, 부하 분산 장치 및 기타 네트워크 어플라이언스는 종종 앱에 도달하기 전에 요청에 대한 정보를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="a16c4-107">HTTPS 요청이 HTTP를 통해 프록시된 경우 원래 스키마(HTTPS)가 손실되므로 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="a16c4-108">앱은 프록시에서 요청을 수신하고 인터넷 또는 회사 네트워크의 실제 소스를 수신하는 것이 아니므로 원래 클라이언트 IP 주소도 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="a16c4-109">이 정보는 요청 처리 시 중요할 수 있습니다(예: 리디렉션, 인증, 링크 생성, 정책 평가 및 클라이언트 지리적 위치).</span><span class="sxs-lookup"><span data-stu-id="a16c4-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="a16c4-110">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="a16c4-110">Forwarded headers</span></span>

<span data-ttu-id="a16c4-111">규칙에 따라 프록시는 HTTP 헤더에서 정보를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="a16c4-112">헤더</span><span class="sxs-lookup"><span data-stu-id="a16c4-112">Header</span></span> | <span data-ttu-id="a16c4-113">설명</span><span class="sxs-lookup"><span data-stu-id="a16c4-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="a16c4-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="a16c4-114">X-Forwarded-For</span></span> | <span data-ttu-id="a16c4-115">요청 및 프록시 체인의 후속 프록시를 시작한 클라이언트에 대한 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="a16c4-116">이 매개 변수에는 IP 주소(및 선택적으로 포트 번호)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="a16c4-117">프록시 서버의 체인에서 첫 번째 매개 변수는 요청이 처음 만들어진 클라이언트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="a16c4-118">그 뒤에 후속 프록시 식별자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="a16c4-119">체인의 마지막 프록시는 매개 변수 목록에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="a16c4-120">마지막 프록시의 IP 주소 및 선택적으로 포트 번호는 전송 계층에서 원격 IP 주소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="a16c4-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="a16c4-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="a16c4-122">원래 체계(HTTP/HTTPS)의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="a16c4-123">요청이 여러 프록시를 트래버스한 경우에는 값이 체계 목록일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="a16c4-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="a16c4-124">X-Forwarded-Host</span></span> | <span data-ttu-id="a16c4-125">호스트 헤더 필드의 원래 값입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-125">The original value of the Host header field.</span></span> <span data-ttu-id="a16c4-126">일반적으로 프록시는 호스트 헤더를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="a16c4-127">프록시가 호스트 헤더를 유효성 검사하거나 알려진 정상 값으로 제한하지 않는 시스템에 영향을 미치는 권한 상승 취약성에 대한 자세한 내용은 [Microsoft 보안 공지 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="a16c4-128">전달된 헤더 미들웨어(<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>)는 헤더를 읽고 <xref:Microsoft.AspNetCore.Http.HttpContext>의 관련 필드를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-128">The Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>), reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="a16c4-129">미들웨어가 다음을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-129">The middleware updates:</span></span>

* <span data-ttu-id="a16c4-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): `X-Forwarded-For` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="a16c4-131">추가 설정은 미들웨어가 `RemoteIpAddress`를 설정하는 방법에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="a16c4-132">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="a16c4-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): `X-Forwarded-Proto` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="a16c4-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): `X-Forwarded-Host` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="a16c4-135">이전 내용에 대한 자세한 정보는 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/21615)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-135">For more information on the preceding, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21615).</span></span>

<span data-ttu-id="a16c4-136">전달된 헤더 미들웨어 [기본 설정](#forwarded-headers-middleware-options)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-136">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="a16c4-137">기본 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-137">The default settings are:</span></span>

* <span data-ttu-id="a16c4-138">앱과 요청 소스 사이에는 ‘하나의 프록시’만 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-138">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="a16c4-139">알려진 프록시 및 알려진 네트워크의 경우 루프백 주소만 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-139">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="a16c4-140">전달된 헤더의 이름은 `X-Forwarded-For` 및 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-140">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="a16c4-141">일부 네트워크 어플라이언스는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 추가하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-141">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="a16c4-142">프록시된 요청이 앱에 도달할 때 이러한 헤더를 포함하지 않는 경우에는 어플라이언스 제조업체의 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-142">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="a16c4-143">어플라이언스가 `X-Forwarded-For` 및 `X-Forwarded-Proto`와는 다른 헤더 이름을 사용하는 경우 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 어플라이언스에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-143">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="a16c4-144">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 및 [다른 헤더 이름을 사용하는 프록시 구성](#configuration-for-a-proxy-that-uses-different-header-names)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-144">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="a16c4-145">IIS/IIS Express 및 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="a16c4-145">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="a16c4-146">앱이 IIS 및 ASP.NET Core 모듈 뒤에서 호스트되는 [Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)인 경우 전달된 헤더 미들웨어가 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)에 의해 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-146">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="a16c4-147">전달된 헤더 미들웨어는 전달된 헤더 관련 신뢰 문제(예: [IP 스푸핑](https://www.iplocation.net/ip-spoofing))로 인해 ASP.NET Core 모듈에 특정한 제한된 구성을 사용하여 미들웨어 파이프라인에서 첫 번째로 실행될 수 있도록 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-147">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="a16c4-148">미들웨어는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 구성되고 단일 localhost 프록시로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-148">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="a16c4-149">추가 구성이 필요한 경우 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-149">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a16c4-150">기타 프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="a16c4-150">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a16c4-151">[Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)를 호스트할 때 [IIS 통합](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 사용 외에는 전달된 헤더 미들웨어가 기본적으로 사용하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-151">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="a16c4-152">앱에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 전달된 헤더를 처리하려면 전달된 헤더 미들웨어를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-152">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="a16c4-153">미들웨어를 사용하도록 설정한 후 미들웨어에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않은 경우 기본 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-153">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="a16c4-154"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 사용하여 `Startup.ConfigureServices`의 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-154">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="a16c4-155">전달된 헤더 미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="a16c4-155">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="a16c4-156">전달된 헤더 미들웨어는 다른 미들웨어보다 먼저 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-156">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="a16c4-157">이 순서를 지정하면 전달된 헤더 정보에 따라 달라지는 미들웨어는 처리하기 위해 헤더 값을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-157">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="a16c4-158">전달된 헤더 미들웨어는 진단 및 오류 처리 뒤에 실행될 수 있지만 `UseHsts`를 호출하기 전에 실행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-158">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="a16c4-159">또는 진단 전에 `UseForwardedHeaders`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-159">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="a16c4-160">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않거나 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 확장 메서드에 직접 지정하지 않은 경우 전달할 기본 헤더는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-160">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="a16c4-161">전달할 헤더를 사용하여 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-161">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="a16c4-162">Nginx 구성</span><span class="sxs-lookup"><span data-stu-id="a16c4-162">Nginx configuration</span></span>

<span data-ttu-id="a16c4-163">`X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하려면 <xref:host-and-deploy/linux-nginx#configure-nginx>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-163">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="a16c4-164">자세한 내용은 [NGINX: 전달된 헤더 사용](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-164">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="a16c4-165">Apache 구성</span><span class="sxs-lookup"><span data-stu-id="a16c4-165">Apache configuration</span></span>

<span data-ttu-id="a16c4-166">`X-Forwarded-For`가 자동으로 추가됩니다( [Apache 모듈 mod_proxy: 역방향 프록시 요청 헤더](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) 참조).</span><span class="sxs-lookup"><span data-stu-id="a16c4-166">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="a16c4-167">`X-Forwarded-Proto` 헤더를 전달하는 방법에 대한 내용은 <xref:host-and-deploy/linux-apache#configure-apache>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-167">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="a16c4-168">전달된 헤더 미들웨어 옵션</span><span class="sxs-lookup"><span data-stu-id="a16c4-168">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="a16c4-169"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>는 전달된 헤더 미들웨어의 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-169"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="a16c4-170">다음 예제에서는 기본값을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-170">The following example changes the default values:</span></span>

* <span data-ttu-id="a16c4-171">전달된 헤더의 항목 수를 `2`로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-171">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="a16c4-172">`127.0.10.1`의 알려진 프록시 주소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-172">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="a16c4-173">전달된 헤더 이름을 기본값 `X-Forwarded-For`에서 `X-Forwarded-For-My-Custom-Header-Name`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-173">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="a16c4-174">옵션</span><span class="sxs-lookup"><span data-stu-id="a16c4-174">Option</span></span> | <span data-ttu-id="a16c4-175">설명</span><span class="sxs-lookup"><span data-stu-id="a16c4-175">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="a16c4-176">`X-Forwarded-Host` 헤더에 의한 호스트를 제공된 값으로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-176">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="a16c4-177">값은 ordinal-ignore-case를 사용하여 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-177">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="a16c4-178">포트 번호는 제외해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-178">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="a16c4-179">목록이 비어 있으면 모든 호스트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-179">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="a16c4-180">최상위 수준 와일드카드 `*`는 비어 있지 않은 모든 호스트를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-180">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="a16c4-181">하위 도메인 와일드카드는 허용되지만 루트 도메인과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-181">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="a16c4-182">예를 들어 `*.contoso.com`은 하위 도메인 `foo.contoso.com`과 일치하지만 루트 도메인 `contoso.com`과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-182">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="a16c4-183">유니코드 호스트 이름은 허용되지만 일치시킬 [Punycode](https://tools.ietf.org/html/rfc3492)로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-183">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="a16c4-184">[IPv6 주소](https://tools.ietf.org/html/rfc4291)는 경계 대괄호를 포함하고 [기존 형식](https://tools.ietf.org/html/rfc4291#section-2.2)이어야 합니다(예: `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="a16c4-184">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="a16c4-185">IPv6 주소는 서로 다른 형식 간에 논리적 같음을 확인하기 위해 특별히 처리되지 않으며 정규화가 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-185">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="a16c4-186">허용된 호스트를 제한하지 못하면 공격자가 서비스에서 생성된 링크를 스푸핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-186">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="a16c4-187">기본값은 빈 `IList<string>`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-187">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="a16c4-188">[ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-188">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="a16c4-189">이 옵션은 프록시/전달자가 `X-Forwarded-For` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-189">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a16c4-190">기본값은 `X-Forwarded-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-190">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="a16c4-191">처리해야 할 전달자를 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-191">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="a16c4-192">적용되는 필드 목록은 [ForwardedHeaders 열거형](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-192">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="a16c4-193">이 속성에 할당된 일반적인 값은 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-193">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="a16c4-194">기본값은 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-194">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="a16c4-195">[ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-195">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="a16c4-196">이 옵션은 프록시/전달자가 `X-Forwarded-Host` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-196">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a16c4-197">기본값은 `X-Forwarded-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-197">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="a16c4-198">[ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-198">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="a16c4-199">이 옵션은 프록시/전달자가 `X-Forwarded-Proto` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-199">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a16c4-200">기본값은 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-200">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="a16c4-201">처리되는 헤더의 항목 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-201">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="a16c4-202">제한을 사용하지 않도록 `null`로 설정하지만, `KnownProxies` 또는 `KnownNetworks`가 구성된 경우에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-202">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="a16c4-203">비-`null` 값을 설정하면 잘못 구성된 프록시 및 네트워크에서 측면 채널에서 오는 악성 요청을 예방하지만 보증하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-203">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="a16c4-204">전달된 헤더 미들웨어는 헤더를 역순으로 오른쪽에서 왼쪽으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-204">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a16c4-205">기본값(`1`)만 사용된다면 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-205">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="a16c4-206">기본값은 `1`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-206">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="a16c4-207">전달된 헤더를 허용하기 위한 알려진 네트워크의 주소 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-207">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="a16c4-208">CIDR(Classless Interdomain Routing) 표기법을 사용하여 IP 범위를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-208">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="a16c4-209">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-209">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a16c4-210">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-210">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a16c4-211">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-211">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="a16c4-212">기본값은 `IPAddress.Loopback`에 대한 단일 항목을 포함하는 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>>입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="a16c4-213">전달된 헤더를 허용하기 위한 알려진 프록시의 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="a16c4-214">`KnownProxies`를 사용하여 정확한 IP 주소 일치 항목을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="a16c4-215">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a16c4-216">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a16c4-217">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="a16c4-218">기본값은 `IPAddress.IPv6Loopback`에 대한 단일 항목을 포함하는 `IList`\<<xref:System.Net.IPAddress>>입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-218">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="a16c4-219">[ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-219">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="a16c4-220">기본값은 `X-Original-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-220">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="a16c4-221">[ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-221">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="a16c4-222">기본값은 `X-Original-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-222">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="a16c4-223">[ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-223">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="a16c4-224">기본값은 `X-Original-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-224">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="a16c4-225">처리 중인 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 간에 동기화할 헤더 값 수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-225">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="a16c4-226">ASP.NET Core 1.x의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-226">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="a16c4-227">ASP.NET Core 2.0 이상의 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-227">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="a16c4-228">시나리오 및 사용 사례</span><span class="sxs-lookup"><span data-stu-id="a16c4-228">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="a16c4-229">전달된 헤더를 추가할 수 없고 모든 요청이 안전한 경우</span><span class="sxs-lookup"><span data-stu-id="a16c4-229">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="a16c4-230">일부 경우에는 앱으로 프록시된 요청에 전달된 헤더를 추가할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-230">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="a16c4-231">모든 공용 외부 요청이 HTTPS가 되도록 프록시가 적용되는 경우 미들웨어를 사용하기 전에 `Startup.Configure`에서 체계를 수동으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-231">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="a16c4-232">이 코드는 개발 또는 스테이징 환경에서 환경 변수 또는 기타 구성 설정을 통해 사용하지 않도록 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-232">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="a16c4-233">요청 경로를 변경하는 프록시 및 경로 기준을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-233">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="a16c4-234">일부 프록시는 경로를 그대로 전달하지만 라우팅이 제대로 작동하도록 제거해야 하는 앱 기본 경로를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-234">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="a16c4-235">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 미들웨어는 경로를 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)로 분할하고 앱 기본 경로를 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-235">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="a16c4-236">`/foo`가 `/foo/api/1`로 전달되는 프록시 경로의 앱 기본 경로인 경우 미들웨어는 다음 명령을 사용하여 `Request.PathBase`를 `/foo`로 설정하고 `Request.Path`를 `/api/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-236">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="a16c4-237">미들웨어가 역방향으로 다시 호출되면 원래 경로 및 경로 기준이 다시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-237">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="a16c4-238">미들웨어 순서 처리에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-238">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="a16c4-239">프록시가 경로를 자르는 경우(예: `/foo/api/1`을 `/api/1`에 전달) 요청의 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 속성을 설정하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-239">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="a16c4-240">프록시가 경로 데이터를 추가하는 경우 경로의 일부를 삭제한 다음, <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*>를 사용하고 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 속성에 할당하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-240">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="a16c4-241">다른 헤더 이름을 사용하는 프록시에 대한 구성</span><span class="sxs-lookup"><span data-stu-id="a16c4-241">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="a16c4-242">프록시가 `X-Forwarded-For` 및 `X-Forwarded-Proto`라는 헤더를 사용하여 프록시 주소/포트 및 원래 체계 정보를 전달하지 않는 경우, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 프록시에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-242">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="a16c4-243">Linux용 스키마 및 비 IIS 역방향 프록시 전달</span><span class="sxs-lookup"><span data-stu-id="a16c4-243">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="a16c4-244"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 및 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>을(를) 호출하는 앱은 Azure Linux App Service, Azure Linux VM(가상 머신) 또는 IIS 이외의 다른 역방향 프록시 뒤에 배포된 경우 사이트를 무한 루프에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-244">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="a16c4-245">TLS는 역방향 프록시에 의해 종료되며, Kestrel은 올바른 요청 체계를 인식하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-245">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="a16c4-246">OAuth 및 OIDC도 잘못된 리디렉션을 생성하므로 이 구성에서 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-246">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="a16c4-247"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>은 IIS 뒤에서 실행되는 경우 전달된 헤더 미들웨어를 추가하고 구성하지만 Linux에 대한 일치하는 자동 구성이 없습니다(Apache 또는 Nginx 통합).</span><span class="sxs-lookup"><span data-stu-id="a16c4-247"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="a16c4-248">비 IIS 시나리오에서 프록시의 스키마를 전달하려면 전달된 헤더 미들웨어를 추가하고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-248">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="a16c4-249">`Startup.ConfigureServices`에서 다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-249">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="a16c4-250">인증서 전달</span><span class="sxs-lookup"><span data-stu-id="a16c4-250">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="a16c4-251">Azure</span><span class="sxs-lookup"><span data-stu-id="a16c4-251">Azure</span></span>

<span data-ttu-id="a16c4-252">인증서 전달에 대한 Azure App Service를 구성하려면 [Azure App Service에 대한 TLS 상호 인증 구성](/azure/app-service/app-service-web-configure-tls-mutual-auth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-252">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="a16c4-253">다음 지침은 ASP.NET Core 앱 구성과 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-253">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="a16c4-254">`Startup.Configure`에서 `app.UseAuthentication();` 호출 앞에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-254">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="a16c4-255">인증서 전달 미들웨어를 구성하여 Azure를 사용하는 헤더 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-255">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="a16c4-256">`Startup.ConfigureServices`에서 다음 코드를 추가하여 미들웨어가 인증서를 빌드하는 헤더를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-256">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="a16c4-257">다른 웹 프록시</span><span class="sxs-lookup"><span data-stu-id="a16c4-257">Other web proxies</span></span>

<span data-ttu-id="a16c4-258">IIS 또는 Azure App Service의 ARR(애플리케이션 요청 라우팅)이 아닌 프록시를 사용하는 경우 HTTP 헤더에서 받은 인증서를 전달하도록 프록시를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-258">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="a16c4-259">`Startup.Configure`에서 `app.UseAuthentication();` 호출 앞에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-259">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="a16c4-260">인증서 전달 미들웨어를 구성하여 헤더 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-260">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="a16c4-261">`Startup.ConfigureServices`에서 다음 코드를 추가하여 미들웨어가 인증서를 빌드하는 헤더를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-261">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="a16c4-262">프록시가 Nginx에서와 같이 인증서를 base64 인코딩하지 않는 경우 `HeaderConverter` 옵션을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-262">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="a16c4-263">`Startup.ConfigureServices`에서 다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-263">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="a16c4-264">문제 해결</span><span class="sxs-lookup"><span data-stu-id="a16c4-264">Troubleshoot</span></span>

<span data-ttu-id="a16c4-265">헤더가 예상대로 전달되지 않으면 [로깅](xref:fundamentals/logging/index)을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-265">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a16c4-266">로그가 문제를 해결하기에 충분한 정보를 제공하지 않으면 서버가 수신하는 요청 헤더를 열거합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-266">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="a16c4-267">인라인 미들웨어를 사용하여 앱 응답에 요청 헤더를 쓰거나 헤더를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-267">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="a16c4-268">앱 응답에 헤더를 기록하려면 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 호출 바로 뒤에 다음 터미널 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-268">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="a16c4-269">응답 본문 대신 로그에 기록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-269">You can write to logs instead of the response body.</span></span> <span data-ttu-id="a16c4-270">로그에 기록하면 디버깅 중 사이트가 정상적으로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-270">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="a16c4-271">응답 본문이 아닌 로그를 기록하려면:</span><span class="sxs-lookup"><span data-stu-id="a16c4-271">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="a16c4-272">[시작 시 로그 만들기](xref:fundamentals/logging/index#create-logs-in-startup)에 설명된 대로 `ILogger<Startup>`을 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-272">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="a16c4-273">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 호출 바로 뒤에 다음 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-273">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a16c4-274">처리 시 `X-Forwarded-{For|Proto|Host}` 값이 `X-Original-{For|Proto|Host}`로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-274">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="a16c4-275">제공된 헤더에 여러 값이 있는 경우 전달된 헤더 미들웨어는 오른쪽에서 왼쪽으로 역순으로 헤더를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-275">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a16c4-276">기본 `ForwardLimit`는 `1`(일)이므로 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-276">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="a16c4-277">요청의 원래 원격 IP는 전달된 헤더가 처리되기 전에 `KnownProxies` 또는 `KnownNetworks` 목록의 항목과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-277">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="a16c4-278">이렇게 하면 신뢰할 수 없는 프록시에서 전달자가 허용되지 않아 헤더 스푸핑이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-278">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="a16c4-279">알 수 없는 프록시가 검색되면 로그에 프록시 주소가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-279">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="a16c4-280">앞의 예제에서 10.0.0.100은 프록시 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-280">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="a16c4-281">서버가 신뢰할 수 있는 프록시인 경우 서버의 IP 주소를 `Startup.ConfigureServices`의 `KnownProxies`에 추가합니다(또는 신뢰할 수 있는 네트워크를 `KnownNetworks`에 추가).</span><span class="sxs-lookup"><span data-stu-id="a16c4-281">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a16c4-282">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-282">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="a16c4-283">신뢰할 수 있는 프록시 및 네트워크만 헤더를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-283">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="a16c4-284">그렇지 않을 경우 [IP 스푸핑](https://www.iplocation.net/ip-spoofing) 공격이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-284">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a16c4-285">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a16c4-285">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="a16c4-286">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)(Microsoft 보안 공지 CVE-2018-0787: ASP.NET Core 권한 상승 취약성)</span><span class="sxs-lookup"><span data-stu-id="a16c4-286">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a16c4-287">ASP.NET Core의 권장 구성에서 앱은 IIS/ASP.NET Core 모듈, Nginx 또는 Apache를 사용하여 호스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-287">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="a16c4-288">프록시 서버, 부하 분산 장치 및 기타 네트워크 어플라이언스는 종종 앱에 도달하기 전에 요청에 대한 정보를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-288">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="a16c4-289">HTTPS 요청이 HTTP를 통해 프록시된 경우 원래 스키마(HTTPS)가 손실되므로 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-289">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="a16c4-290">앱은 프록시에서 요청을 수신하고 인터넷 또는 회사 네트워크의 실제 소스를 수신하는 것이 아니므로 원래 클라이언트 IP 주소도 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-290">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="a16c4-291">이 정보는 요청 처리 시 중요할 수 있습니다(예: 리디렉션, 인증, 링크 생성, 정책 평가 및 클라이언트 지리적 위치).</span><span class="sxs-lookup"><span data-stu-id="a16c4-291">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="a16c4-292">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="a16c4-292">Forwarded headers</span></span>

<span data-ttu-id="a16c4-293">규칙에 따라 프록시는 HTTP 헤더에서 정보를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-293">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="a16c4-294">헤더</span><span class="sxs-lookup"><span data-stu-id="a16c4-294">Header</span></span> | <span data-ttu-id="a16c4-295">설명</span><span class="sxs-lookup"><span data-stu-id="a16c4-295">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="a16c4-296">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="a16c4-296">X-Forwarded-For</span></span> | <span data-ttu-id="a16c4-297">요청 및 프록시 체인의 후속 프록시를 시작한 클라이언트에 대한 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-297">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="a16c4-298">이 매개 변수에는 IP 주소(및 선택적으로 포트 번호)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-298">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="a16c4-299">프록시 서버의 체인에서 첫 번째 매개 변수는 요청이 처음 만들어진 클라이언트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-299">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="a16c4-300">그 뒤에 후속 프록시 식별자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-300">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="a16c4-301">체인의 마지막 프록시는 매개 변수 목록에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-301">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="a16c4-302">마지막 프록시의 IP 주소 및 선택적으로 포트 번호는 전송 계층에서 원격 IP 주소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-302">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="a16c4-303">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="a16c4-303">X-Forwarded-Proto</span></span> | <span data-ttu-id="a16c4-304">원래 체계(HTTP/HTTPS)의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-304">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="a16c4-305">요청이 여러 프록시를 트래버스한 경우에는 값이 체계 목록일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-305">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="a16c4-306">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="a16c4-306">X-Forwarded-Host</span></span> | <span data-ttu-id="a16c4-307">호스트 헤더 필드의 원래 값입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-307">The original value of the Host header field.</span></span> <span data-ttu-id="a16c4-308">일반적으로 프록시는 호스트 헤더를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-308">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="a16c4-309">프록시가 호스트 헤더를 유효성 검사하거나 알려진 정상 값으로 제한하지 않는 시스템에 영향을 미치는 권한 상승 취약성에 대한 자세한 내용은 [Microsoft 보안 공지 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-309">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="a16c4-310">[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 패키지의 전달된 헤더 미들웨어는 헤더를 읽고 <xref:Microsoft.AspNetCore.Http.HttpContext>의 관련 필드를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-310">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="a16c4-311">미들웨어가 다음을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-311">The middleware updates:</span></span>

* <span data-ttu-id="a16c4-312">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): `X-Forwarded-For` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-312">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="a16c4-313">추가 설정은 미들웨어가 `RemoteIpAddress`를 설정하는 방법에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-313">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="a16c4-314">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-314">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="a16c4-315">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): `X-Forwarded-Proto` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-315">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="a16c4-316">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): `X-Forwarded-Host` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-316">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="a16c4-317">전달된 헤더 미들웨어 [기본 설정](#forwarded-headers-middleware-options)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-317">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="a16c4-318">기본 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-318">The default settings are:</span></span>

* <span data-ttu-id="a16c4-319">앱과 요청 소스 사이에는 ‘하나의 프록시’만 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-319">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="a16c4-320">알려진 프록시 및 알려진 네트워크의 경우 루프백 주소만 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-320">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="a16c4-321">전달된 헤더의 이름은 `X-Forwarded-For` 및 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-321">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="a16c4-322">일부 네트워크 어플라이언스는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 추가하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-322">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="a16c4-323">프록시된 요청이 앱에 도달할 때 이러한 헤더를 포함하지 않는 경우에는 어플라이언스 제조업체의 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-323">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="a16c4-324">어플라이언스가 `X-Forwarded-For` 및 `X-Forwarded-Proto`와는 다른 헤더 이름을 사용하는 경우 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 어플라이언스에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-324">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="a16c4-325">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 및 [다른 헤더 이름을 사용하는 프록시 구성](#configuration-for-a-proxy-that-uses-different-header-names)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-325">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="a16c4-326">IIS/IIS Express 및 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="a16c4-326">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="a16c4-327">앱이 IIS 및 ASP.NET Core 모듈 뒤에서 호스트되는 [Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)인 경우 전달된 헤더 미들웨어가 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)에 의해 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-327">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="a16c4-328">전달된 헤더 미들웨어는 전달된 헤더 관련 신뢰 문제(예: [IP 스푸핑](https://www.iplocation.net/ip-spoofing))로 인해 ASP.NET Core 모듈에 특정한 제한된 구성을 사용하여 미들웨어 파이프라인에서 첫 번째로 실행될 수 있도록 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-328">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="a16c4-329">미들웨어는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 구성되고 단일 localhost 프록시로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-329">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="a16c4-330">추가 구성이 필요한 경우 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-330">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a16c4-331">기타 프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="a16c4-331">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a16c4-332">[Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)를 호스트할 때 [IIS 통합](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 사용 외에는 전달된 헤더 미들웨어가 기본적으로 사용하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-332">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="a16c4-333">앱에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 전달된 헤더를 처리하려면 전달된 헤더 미들웨어를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-333">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="a16c4-334">미들웨어를 사용하도록 설정한 후 미들웨어에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않은 경우 기본 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-334">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="a16c4-335"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 사용하여 `Startup.ConfigureServices`의 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-335">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a16c4-336">다른 미들웨어를 호출하기 전에 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-336">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="a16c4-337">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않거나 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 확장 메서드에 직접 지정하지 않은 경우 전달할 기본 헤더는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-337">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="a16c4-338">전달할 헤더를 사용하여 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-338">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="a16c4-339">Nginx 구성</span><span class="sxs-lookup"><span data-stu-id="a16c4-339">Nginx configuration</span></span>

<span data-ttu-id="a16c4-340">`X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하려면 <xref:host-and-deploy/linux-nginx#configure-nginx>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-340">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="a16c4-341">자세한 내용은 [NGINX: 전달된 헤더 사용](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-341">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="a16c4-342">Apache 구성</span><span class="sxs-lookup"><span data-stu-id="a16c4-342">Apache configuration</span></span>

<span data-ttu-id="a16c4-343">`X-Forwarded-For`가 자동으로 추가됩니다( [Apache 모듈 mod_proxy: 역방향 프록시 요청 헤더](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) 참조).</span><span class="sxs-lookup"><span data-stu-id="a16c4-343">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="a16c4-344">`X-Forwarded-Proto` 헤더를 전달하는 방법에 대한 내용은 <xref:host-and-deploy/linux-apache#configure-apache>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-344">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="a16c4-345">전달된 헤더 미들웨어 옵션</span><span class="sxs-lookup"><span data-stu-id="a16c4-345">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="a16c4-346"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>는 전달된 헤더 미들웨어의 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-346"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="a16c4-347">다음 예제에서는 기본값을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-347">The following example changes the default values:</span></span>

* <span data-ttu-id="a16c4-348">전달된 헤더의 항목 수를 `2`로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-348">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="a16c4-349">`127.0.10.1`의 알려진 프록시 주소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-349">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="a16c4-350">전달된 헤더 이름을 기본값 `X-Forwarded-For`에서 `X-Forwarded-For-My-Custom-Header-Name`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-350">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="a16c4-351">옵션</span><span class="sxs-lookup"><span data-stu-id="a16c4-351">Option</span></span> | <span data-ttu-id="a16c4-352">설명</span><span class="sxs-lookup"><span data-stu-id="a16c4-352">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="a16c4-353">`X-Forwarded-Host` 헤더에 의한 호스트를 제공된 값으로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-353">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="a16c4-354">값은 ordinal-ignore-case를 사용하여 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-354">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="a16c4-355">포트 번호는 제외해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-355">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="a16c4-356">목록이 비어 있으면 모든 호스트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-356">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="a16c4-357">최상위 수준 와일드카드 `*`는 비어 있지 않은 모든 호스트를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-357">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="a16c4-358">하위 도메인 와일드카드는 허용되지만 루트 도메인과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-358">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="a16c4-359">예를 들어 `*.contoso.com`은 하위 도메인 `foo.contoso.com`과 일치하지만 루트 도메인 `contoso.com`과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-359">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="a16c4-360">유니코드 호스트 이름은 허용되지만 일치시킬 [Punycode](https://tools.ietf.org/html/rfc3492)로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-360">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="a16c4-361">[IPv6 주소](https://tools.ietf.org/html/rfc4291)는 경계 대괄호를 포함하고 [기존 형식](https://tools.ietf.org/html/rfc4291#section-2.2)이어야 합니다(예: `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="a16c4-361">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="a16c4-362">IPv6 주소는 서로 다른 형식 간에 논리적 같음을 확인하기 위해 특별히 처리되지 않으며 정규화가 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-362">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="a16c4-363">허용된 호스트를 제한하지 못하면 공격자가 서비스에서 생성된 링크를 스푸핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-363">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="a16c4-364">기본값은 빈 `IList<string>`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-364">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="a16c4-365">[ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-365">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="a16c4-366">이 옵션은 프록시/전달자가 `X-Forwarded-For` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-366">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a16c4-367">기본값은 `X-Forwarded-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-367">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="a16c4-368">처리해야 할 전달자를 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-368">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="a16c4-369">적용되는 필드 목록은 [ForwardedHeaders 열거형](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-369">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="a16c4-370">이 속성에 할당된 일반적인 값은 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-370">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="a16c4-371">기본값은 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-371">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="a16c4-372">[ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="a16c4-373">이 옵션은 프록시/전달자가 `X-Forwarded-Host` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a16c4-374">기본값은 `X-Forwarded-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-374">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="a16c4-375">[ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-375">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="a16c4-376">이 옵션은 프록시/전달자가 `X-Forwarded-Proto` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-376">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a16c4-377">기본값은 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-377">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="a16c4-378">처리되는 헤더의 항목 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-378">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="a16c4-379">제한을 사용하지 않도록 `null`로 설정하지만, `KnownProxies` 또는 `KnownNetworks`가 구성된 경우에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-379">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="a16c4-380">비-`null` 값을 설정하면 잘못 구성된 프록시 및 네트워크에서 측면 채널에서 오는 악성 요청을 예방하지만 보증하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-380">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="a16c4-381">전달된 헤더 미들웨어는 헤더를 역순으로 오른쪽에서 왼쪽으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-381">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a16c4-382">기본값(`1`)만 사용된다면 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-382">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="a16c4-383">기본값은 `1`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-383">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="a16c4-384">전달된 헤더를 허용하기 위한 알려진 네트워크의 주소 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-384">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="a16c4-385">CIDR(Classless Interdomain Routing) 표기법을 사용하여 IP 범위를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-385">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="a16c4-386">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-386">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a16c4-387">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-387">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a16c4-388">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-388">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="a16c4-389">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-389">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="a16c4-390">기본값은 `IPAddress.Loopback`에 대한 단일 항목을 포함하는 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>>입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-390">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="a16c4-391">전달된 헤더를 허용하기 위한 알려진 프록시의 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-391">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="a16c4-392">`KnownProxies`를 사용하여 정확한 IP 주소 일치 항목을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-392">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="a16c4-393">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a16c4-394">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a16c4-395">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="a16c4-396">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="a16c4-397">기본값은 `IPAddress.IPv6Loopback`에 대한 단일 항목을 포함하는 `IList`\<<xref:System.Net.IPAddress>>입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-397">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="a16c4-398">[ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-398">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="a16c4-399">기본값은 `X-Original-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-399">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="a16c4-400">[ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-400">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="a16c4-401">기본값은 `X-Original-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-401">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="a16c4-402">[ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-402">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="a16c4-403">기본값은 `X-Original-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-403">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="a16c4-404">처리 중인 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 간에 동기화할 헤더 값 수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-404">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="a16c4-405">ASP.NET Core 1.x의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-405">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="a16c4-406">ASP.NET Core 2.0 이상의 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-406">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="a16c4-407">시나리오 및 사용 사례</span><span class="sxs-lookup"><span data-stu-id="a16c4-407">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="a16c4-408">전달된 헤더를 추가할 수 없고 모든 요청이 안전한 경우</span><span class="sxs-lookup"><span data-stu-id="a16c4-408">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="a16c4-409">일부 경우에는 앱으로 프록시된 요청에 전달된 헤더를 추가할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-409">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="a16c4-410">모든 공용 외부 요청이 HTTPS가 되도록 프록시가 적용되는 경우 미들웨어를 사용하기 전에 `Startup.Configure`에서 체계를 수동으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-410">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="a16c4-411">이 코드는 개발 또는 스테이징 환경에서 환경 변수 또는 기타 구성 설정을 통해 사용하지 않도록 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-411">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="a16c4-412">요청 경로를 변경하는 프록시 및 경로 기준을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-412">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="a16c4-413">일부 프록시는 경로를 그대로 전달하지만 라우팅이 제대로 작동하도록 제거해야 하는 앱 기본 경로를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-413">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="a16c4-414">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 미들웨어는 경로를 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)로 분할하고 앱 기본 경로를 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-414">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="a16c4-415">`/foo`가 `/foo/api/1`로 전달되는 프록시 경로의 앱 기본 경로인 경우 미들웨어는 다음 명령을 사용하여 `Request.PathBase`를 `/foo`로 설정하고 `Request.Path`를 `/api/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-415">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="a16c4-416">미들웨어가 역방향으로 다시 호출되면 원래 경로 및 경로 기준이 다시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-416">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="a16c4-417">미들웨어 순서 처리에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-417">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="a16c4-418">프록시가 경로를 자르는 경우(예: `/foo/api/1`을 `/api/1`에 전달) 요청의 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 속성을 설정하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-418">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="a16c4-419">프록시가 경로 데이터를 추가하는 경우 경로의 일부를 삭제한 다음, <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*>를 사용하고 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 속성에 할당하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-419">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="a16c4-420">다른 헤더 이름을 사용하는 프록시에 대한 구성</span><span class="sxs-lookup"><span data-stu-id="a16c4-420">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="a16c4-421">프록시가 `X-Forwarded-For` 및 `X-Forwarded-Proto`라는 헤더를 사용하여 프록시 주소/포트 및 원래 체계 정보를 전달하지 않는 경우, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 프록시에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-421">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="a16c4-422">IPv6 주소로 표시되는 IPv4 주소 구성</span><span class="sxs-lookup"><span data-stu-id="a16c4-422">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="a16c4-423">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1` 또는 `::ffff:a00:1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-423">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="a16c4-424">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-424">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a16c4-425">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-425">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="a16c4-426">다음 예제에서는 전달된 헤더를 제공하는 네트워크 주소가 IPv6 형식의 `KnownNetworks` 목록에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-426">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="a16c4-427">IPv4 주소: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="a16c4-427">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="a16c4-428">변환된 IPv6 주소: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="a16c4-428">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="a16c4-429">변환된 접두사 길이: 104</span><span class="sxs-lookup"><span data-stu-id="a16c4-429">Converted prefix length: 104</span></span>

<span data-ttu-id="a16c4-430">16진수 형식으로 주소를 제공할 수도 있습니다(IPv6에서 표시된 `10.11.12.1`을 `::ffff:0a0b:0c01`로 표시).</span><span class="sxs-lookup"><span data-stu-id="a16c4-430">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="a16c4-431">IPv4 주소를 IPv6로 변환할 때 CIDR 접두사 길이(예제에서는 `8`)에 96을 추가하여 추가 `::ffff:` IPv6 접두사를 처리합니다(8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="a16c4-431">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="a16c4-432">Linux용 스키마 및 비 IIS 역방향 프록시 전달</span><span class="sxs-lookup"><span data-stu-id="a16c4-432">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="a16c4-433"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 및 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>을(를) 호출하는 앱은 Azure Linux App Service, Azure Linux VM(가상 머신) 또는 IIS 이외의 다른 역방향 프록시 뒤에 배포된 경우 사이트를 무한 루프에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-433">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="a16c4-434">TLS는 역방향 프록시에 의해 종료되며, Kestrel은 올바른 요청 체계를 인식하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-434">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="a16c4-435">OAuth 및 OIDC도 잘못된 리디렉션을 생성하므로 이 구성에서 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-435">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="a16c4-436"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>은 IIS 뒤에서 실행되는 경우 전달된 헤더 미들웨어를 추가하고 구성하지만 Linux에 대한 일치하는 자동 구성이 없습니다(Apache 또는 Nginx 통합).</span><span class="sxs-lookup"><span data-stu-id="a16c4-436"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="a16c4-437">비 IIS 시나리오에서 프록시의 스키마를 전달하려면 전달된 헤더 미들웨어를 추가하고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-437">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="a16c4-438">`Startup.ConfigureServices`에서 다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-438">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="a16c4-439">문제 해결</span><span class="sxs-lookup"><span data-stu-id="a16c4-439">Troubleshoot</span></span>

<span data-ttu-id="a16c4-440">헤더가 예상대로 전달되지 않으면 [로깅](xref:fundamentals/logging/index)을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-440">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a16c4-441">로그가 문제를 해결하기에 충분한 정보를 제공하지 않으면 서버가 수신하는 요청 헤더를 열거합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-441">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="a16c4-442">인라인 미들웨어를 사용하여 앱 응답에 요청 헤더를 쓰거나 헤더를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-442">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="a16c4-443">앱 응답에 헤더를 기록하려면 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 호출 바로 뒤에 다음 터미널 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-443">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="a16c4-444">응답 본문 대신 로그에 기록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-444">You can write to logs instead of the response body.</span></span> <span data-ttu-id="a16c4-445">로그에 기록하면 디버깅 중 사이트가 정상적으로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-445">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="a16c4-446">응답 본문이 아닌 로그를 기록하려면:</span><span class="sxs-lookup"><span data-stu-id="a16c4-446">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="a16c4-447">[시작 시 로그 만들기](xref:fundamentals/logging/index#create-logs-in-startup)에 설명된 대로 `ILogger<Startup>`을 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-447">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="a16c4-448">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 호출 바로 뒤에 다음 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-448">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a16c4-449">처리 시 `X-Forwarded-{For|Proto|Host}` 값이 `X-Original-{For|Proto|Host}`로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-449">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="a16c4-450">제공된 헤더에 여러 값이 있는 경우 전달된 헤더 미들웨어는 오른쪽에서 왼쪽으로 역순으로 헤더를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-450">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a16c4-451">기본 `ForwardLimit`는 `1`(일)이므로 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-451">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="a16c4-452">요청의 원래 원격 IP는 전달된 헤더가 처리되기 전에 `KnownProxies` 또는 `KnownNetworks` 목록의 항목과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-452">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="a16c4-453">이렇게 하면 신뢰할 수 없는 프록시에서 전달자가 허용되지 않아 헤더 스푸핑이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-453">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="a16c4-454">알 수 없는 프록시가 검색되면 로그에 프록시 주소가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-454">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="a16c4-455">앞의 예제에서 10.0.0.100은 프록시 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-455">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="a16c4-456">서버가 신뢰할 수 있는 프록시인 경우 서버의 IP 주소를 `Startup.ConfigureServices`의 `KnownProxies`에 추가합니다(또는 신뢰할 수 있는 네트워크를 `KnownNetworks`에 추가).</span><span class="sxs-lookup"><span data-stu-id="a16c4-456">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a16c4-457">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a16c4-457">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="a16c4-458">신뢰할 수 있는 프록시 및 네트워크만 헤더를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-458">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="a16c4-459">그렇지 않을 경우 [IP 스푸핑](https://www.iplocation.net/ip-spoofing) 공격이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="a16c4-459">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a16c4-460">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a16c4-460">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="a16c4-461">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)(Microsoft 보안 공지 CVE-2018-0787: ASP.NET Core 권한 상승 취약성)</span><span class="sxs-lookup"><span data-stu-id="a16c4-461">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
