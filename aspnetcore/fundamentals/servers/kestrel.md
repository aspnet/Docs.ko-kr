---
title: ASP.NET Core에서 Kestrel 웹 서버 구현
author: rick-anderson
description: ASP.NET Core의 플랫폼 간 웹 서버인 Kestrel에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: d53cafb605939fd85bdbb71b2fbf13e7bd7a9b7b
ms.sourcegitcommit: cb984e0d7dc23a88c3a4121f23acfaea0acbfe1e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571001"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="14931-103">ASP.NET Core에서 Kestrel 웹 서버 구현</span><span class="sxs-lookup"><span data-stu-id="14931-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="14931-104">작성자: [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) 및 [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="14931-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="14931-105">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="14931-106">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버이며 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-106">Kestrel is the web server that's included and enabled by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="14931-107">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="14931-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="14931-108">HTTPS</span></span>
* <span data-ttu-id="14931-109">[HTTP/2](xref:fundamentals/servers/kestrel/http2)(macOS&dagger; 제외)</span><span class="sxs-lookup"><span data-stu-id="14931-109">[HTTP/2](xref:fundamentals/servers/kestrel/http2) (except on macOS&dagger;)</span></span>
* <span data-ttu-id="14931-110">[Websocket](xref:fundamentals/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="14931-110">Opaque upgrade used to enable [WebSockets](xref:fundamentals/websockets)</span></span>
* <span data-ttu-id="14931-111">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="14931-111">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="14931-112">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="14931-113">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="14931-114">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14931-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started"></a><span data-ttu-id="14931-115">시작하기</span><span class="sxs-lookup"><span data-stu-id="14931-115">Get started</span></span>

<span data-ttu-id="14931-116">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-116">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="14931-117">*Program.cs* 에서 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-117">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/5.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="14931-118">호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#set-up-a-host>의 *호스트 설정* 및 *기본 작성기 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-118">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14931-119">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="14931-119">Additional resources</span></span>

<a name="endpoint-configuration"></a>
* <xref:fundamentals/servers/kestrel/endpoints>
<a name="kestrel-options"></a>
* <xref:fundamentals/servers/kestrel/options>
<a name="http2-support"></a>
* <xref:fundamentals/servers/kestrel/http2>
<a name="when-to-use-kestrel-with-a-reverse-proxy"></a>
* <xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy>
<a name="host-filtering"></a>
* <xref:fundamentals/servers/kestrel/host-filtering>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="14931-120">RFC 7230: 메시지 구문 및 라우팅(섹션 5.4: 호스트)</span><span class="sxs-lookup"><span data-stu-id="14931-120">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
* <span data-ttu-id="14931-121">Linux에서 UNIX 소켓을 사용하는 경우 앱을 종료할 때 소켓이 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-121">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="14931-122">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/aspnetcore/issues/14134)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-122">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>

> [!NOTE]
> <span data-ttu-id="14931-123">ASP.NET Core 5.0을 기준으로 Kestrel의 libuv 전송은 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-123">As of ASP.NET Core 5.0, Kestrel's libuv transport is obsolete.</span></span> <span data-ttu-id="14931-124">Libuv 전송은 Windows ARM64와 같은 새로운 OS 플랫폼을 지원하는 업데이트를 수신하지 않으며 이후 릴리스에서 제거될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-124">The libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="14931-125">사용되지 않는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> 메서드에 대한 모든 호출을 제거하고 대신 Kestrel의 기본 소켓 전송을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-125">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="14931-126">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-126">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="14931-127">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-127">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="14931-128">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-128">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="14931-129">HTTPS</span><span class="sxs-lookup"><span data-stu-id="14931-129">HTTPS</span></span>
* <span data-ttu-id="14931-130">[Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="14931-130">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="14931-131">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="14931-131">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="14931-132">HTTP/2(macOS&dagger; 제외)</span><span class="sxs-lookup"><span data-stu-id="14931-132">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="14931-133">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-133">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="14931-134">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-134">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="14931-135">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14931-135">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="14931-136">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="14931-136">HTTP/2 support</span></span>

<span data-ttu-id="14931-137">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해 [HTTP/2](https://httpwg.org/specs/rfc7540.html)를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-137">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="14931-138">운영 체제&dagger;</span><span class="sxs-lookup"><span data-stu-id="14931-138">Operating system&dagger;</span></span>
  * <span data-ttu-id="14931-139">Windows Server 2016/Windows 10 이상&Dagger;</span><span class="sxs-lookup"><span data-stu-id="14931-139">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="14931-140">Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)</span><span class="sxs-lookup"><span data-stu-id="14931-140">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="14931-141">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="14931-141">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="14931-142">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="14931-142">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="14931-143">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="14931-143">TLS 1.2 or later connection</span></span>

<span data-ttu-id="14931-144">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-144">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="14931-145">&Dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-145">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="14931-146">이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-146">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="14931-147">TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-147">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="14931-148">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-148">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="14931-149">.NET Core 3.0부터 HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-149">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="14931-150">구성에 대한 자세한 내용은 [Kestrel 옵션](#kestrel-options) 및 [ListenOptions.Protocols](#listenoptionsprotocols) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-150">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="14931-151">Kestrel을 역방향 프록시와 함께 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="14931-151">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="14931-152">Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버* 와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-152">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="14931-153">역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-153">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="14931-154">Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-154">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="14931-156">Kestrel은 역방향 프록시 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-156">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="14931-158">역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-158">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="14931-159">역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-159">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="14931-160">Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-160">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="14931-161">포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-161">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="14931-162">역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-162">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="14931-163">역방향 프록시:</span><span class="sxs-lookup"><span data-stu-id="14931-163">A reverse proxy:</span></span>

* <span data-ttu-id="14931-164">호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-164">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="14931-165">구성 및 방어의 추가 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-165">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="14931-166">기존 인프라와 잘 통합될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-166">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="14931-167">부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-167">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="14931-168">역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-168">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="14931-169">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-169">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="14931-170">ASP.NET Core 앱의 Kestrel</span><span class="sxs-lookup"><span data-stu-id="14931-170">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="14931-171">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-171">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="14931-172">*Program.cs* 에서 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-172">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="14931-173">호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#set-up-a-host>의 *호스트 설정* 및 *기본 작성기 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-173">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="14931-174">`ConfigureWebHostDefaults`를 호출한 후 추가 구성을 제공하려면 `ConfigureKestrel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-174">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="14931-175">Kestrel 옵션</span><span class="sxs-lookup"><span data-stu-id="14931-175">Kestrel options</span></span>

<span data-ttu-id="14931-176">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-176">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="14931-177"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-177">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="14931-178">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-178">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="14931-179">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-179">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="14931-180">이 문서의 뒷부분에 나오는 예제에서 Kestrel 옵션은 C# 코드로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-180">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="14931-181">Kestrel 옵션은 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-181">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="14931-182">예를 들어 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider)는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-182">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="14931-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 및 [엔드포인트 구성](#endpoint-configuration)은 구성 공급자에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="14931-184">나머지 Kestrel 구성은 C# 코드로 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-184">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="14931-185">다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-185">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="14931-186">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-186">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="14931-187">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-187">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="14931-188">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-188">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="14931-189">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-189">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="14931-190">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-190">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="14931-191">*Program.cs* 에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-191">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="14931-192">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-192">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="14931-193">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="14931-193">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="14931-194">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-194">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="14931-195">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-195">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="14931-196">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="14931-196">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="14931-197">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-197">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="14931-198">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-198">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="14931-199">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-199">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="14931-200">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-200">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="14931-201">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="14931-201">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="14931-202">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-202">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="14931-203">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-203">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="14931-204">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="14931-204">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="14931-205">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-205">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="14931-206">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-206">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="14931-207">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-207">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="14931-208">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-208">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="14931-209">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="14931-209">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="14931-210">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-210">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="14931-211">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-211">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="14931-212">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-212">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="14931-213">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-213">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="14931-214">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-214">A minimum rate also applies to the response.</span></span> <span data-ttu-id="14931-215">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-215">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="14931-216">*Program.cs* 에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-216">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="14931-217">미들웨어에서 요청당 최소 속도 제한을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-217">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="14931-218">요청 멀티플렉싱에 대한 프로토콜의 지원으로 인해 요청별 속도 제한을 수정하는 것이 HTTP/2에 대해 일반적으로 지원되지 않으므로, 이전 샘플에서 참조된 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>는 HTTP/2 요청에 대해 `HttpContext.Features`에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="14931-219">그러나 HTTP/2 요청에 대해서도 `IHttpMinRequestBodyDataRateFeature.MinDataRate`를 `null`로 설정하여 요청별 읽기 속도 제한을 *완전히 비활성화* 할 수 있으므로 HTTP/2 요청에 대한 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>는 여전히 존재합니다`HttpContext.Features`.</span><span class="sxs-lookup"><span data-stu-id="14931-219">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="14931-220">`IHttpMinRequestBodyDataRateFeature.MinDataRate` 읽기를 시도하거나 `null`이 아닌 값으로 설정하려고 하면 HTTP/2 요청이 있을 때 `NotSupportedException`이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-220">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="14931-221">`KestrelServerOptions.Limits`를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-221">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="14931-222">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="14931-222">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="14931-223">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-223">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="14931-224">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-224">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="14931-225">연결당 최대 스트림</span><span class="sxs-lookup"><span data-stu-id="14931-225">Maximum streams per connection</span></span>

<span data-ttu-id="14931-226">`Http2.MaxStreamsPerConnection`은 HTTP/2 연결당 동시 요청 스트림 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-226">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="14931-227">초과 스트림은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-227">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="14931-228">기본값은 100입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-228">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="14931-229">헤더 테이블 크기</span><span class="sxs-lookup"><span data-stu-id="14931-229">Header table size</span></span>

<span data-ttu-id="14931-230">HPACK 디코더는 HTTP/2 연결에 대한 HTTP 헤더의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="14931-230">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="14931-231">`Http2.HeaderTableSize`는 HPACK 디코더가 사용하는 헤더 압축 테이블의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-231">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="14931-232">값은 8진수로 제공되며 영(0)보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-232">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="14931-233">기본값은 4096입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-233">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="14931-234">최대 프레임 크기</span><span class="sxs-lookup"><span data-stu-id="14931-234">Maximum frame size</span></span>

<span data-ttu-id="14931-235">`Http2.MaxFrameSize`는 서버에서 받거나 보낸 HTTP/2 연결 프레임 페이로드의 최대 허용 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-235">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="14931-236">값은 8진수로 제공되며 2^14(16,384)와 2^24-1(16,777,215) 사이여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-236">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="14931-237">기본값은 2^14(16,384)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-237">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="14931-238">최대 요청 헤더 크기</span><span class="sxs-lookup"><span data-stu-id="14931-238">Maximum request header size</span></span>

<span data-ttu-id="14931-239">`Http2.MaxRequestHeaderFieldSize`는 요청 헤더 값의 8진수로 허용되는 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-239">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="14931-240">이 한도는 모두 압축된 표현과 압축되지 않은 표현으로 이름과 값 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-240">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="14931-241">값은 0보다 커야 합니다(0).</span><span class="sxs-lookup"><span data-stu-id="14931-241">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="14931-242">기본값은 8,192입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-242">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="14931-243">초기 연결 창 크기</span><span class="sxs-lookup"><span data-stu-id="14931-243">Initial connection window size</span></span>

<span data-ttu-id="14931-244">`Http2.InitialConnectionWindowSize`는 연결당 모든 요청(스트림)을 통해 한 번에 집계하는 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-244">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="14931-245">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-245">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="14931-246">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-246">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="14931-247">기본값은 128KB(131,072)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-247">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="14931-248">초기 스트림 창 크기</span><span class="sxs-lookup"><span data-stu-id="14931-248">Initial stream window size</span></span>

<span data-ttu-id="14931-249">`Http2.InitialStreamWindowSize`는 요청(스트림)당 한 번에 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-249">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="14931-250">요청은 `Http2.InitialConnectionWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-250">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="14931-251">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-251">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="14931-252">기본값은 96KB(98,304)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-252">The default value is 96 KB (98,304).</span></span>

### <a name="trailers"></a><span data-ttu-id="14931-253">트레일러</span><span class="sxs-lookup"><span data-stu-id="14931-253">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="14931-254">다시 설정</span><span class="sxs-lookup"><span data-stu-id="14931-254">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="14931-255">동기 I/O</span><span class="sxs-lookup"><span data-stu-id="14931-255">Synchronous I/O</span></span>

<span data-ttu-id="14931-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 I/O가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="14931-257">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-257">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="14931-258">차단 동기 I/O 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-258">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="14931-259">비동기 I/O를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 사용하도록 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-259">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="14931-260">다음 예제에서는 동기 I/O를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-260">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="14931-261">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-261">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="14931-262">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="14931-262">Endpoint configuration</span></span>

<span data-ttu-id="14931-263">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-263">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="14931-264">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="14931-264">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="14931-265">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-265">Specify URLs using the:</span></span>

* <span data-ttu-id="14931-266">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="14931-266">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="14931-267">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="14931-267">`--urls` command-line argument.</span></span>
* <span data-ttu-id="14931-268">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="14931-268">`urls` host configuration key.</span></span>
* <span data-ttu-id="14931-269">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="14931-269">`UseUrls` extension method.</span></span>

<span data-ttu-id="14931-270">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-270">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="14931-271">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="14931-271">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="14931-272">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-272">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="14931-273">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="14931-273">A development certificate is created:</span></span>

* <span data-ttu-id="14931-274">[.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.</span><span class="sxs-lookup"><span data-stu-id="14931-274">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="14931-275">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-275">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="14931-276">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-276">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="14931-277">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-277">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="14931-278"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-278">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="14931-279">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="14931-279">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="14931-280">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="14931-280">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="14931-281">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="14931-281">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="14931-282">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-282">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="14931-283">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="14931-283">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="14931-284"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="14931-285">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="14931-285">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="14931-286">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-286">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="14931-287">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="14931-287">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="14931-288"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-288">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="14931-289">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="14931-289">Configure(IConfiguration)</span></span>

<span data-ttu-id="14931-290">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="14931-290">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="14931-291">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-291">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="14931-292">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="14931-292">ListenOptions.UseHttps</span></span>

<span data-ttu-id="14931-293">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-293">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="14931-294">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="14931-294">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="14931-295">`UseHttps`: 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-295">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="14931-296">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-296">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="14931-297">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="14931-297">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="14931-298">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-298">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="14931-299">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-299">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="14931-300">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-300">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="14931-301">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-301">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="14931-302">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-302">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="14931-303">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-303">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="14931-304">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-304">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="14931-305">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-305">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="14931-306">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-306">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="14931-307">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-307">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="14931-308">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-308">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="14931-309">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-309">Supported configurations described next:</span></span>

* <span data-ttu-id="14931-310">구성 없음</span><span class="sxs-lookup"><span data-stu-id="14931-310">No configuration</span></span>
* <span data-ttu-id="14931-311">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="14931-311">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="14931-312">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="14931-312">Change the defaults in code</span></span>

<span data-ttu-id="14931-313">*구성 없음*</span><span class="sxs-lookup"><span data-stu-id="14931-313">*No configuration*</span></span>

<span data-ttu-id="14931-314">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="14931-314">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="14931-315">*구성에서 기본 인증서를 바꿈*</span><span class="sxs-lookup"><span data-stu-id="14931-315">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="14931-316">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-316">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="14931-317">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-317">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="14931-318">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-318">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="14931-319">다음 *appsettings.json* 예제에서</span><span class="sxs-lookup"><span data-stu-id="14931-319">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="14931-320">잘못된 인증서 사용을 허가하려면 **AllowInvalid** 를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="14931-320">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="14931-321">인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본** 에서 정의된 인증서 또는 개발 인증서로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-321">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="14931-322">모든 인증서 노드에 대해 **경로** 및 **암호** 를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-322">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="14931-323">예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-323">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="14931-324">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="14931-324">Schema notes:</span></span>

* <span data-ttu-id="14931-325">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-325">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="14931-326">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-326">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="14931-327">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-327">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="14931-328">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-328">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="14931-329">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="14931-329">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="14931-330">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-330">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="14931-331">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-331">The `Certificate` section is optional.</span></span> <span data-ttu-id="14931-332">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-332">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="14931-333">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-333">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="14931-334">`Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-334">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="14931-335">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-335">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="14931-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="14931-337">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-337">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="14931-338">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-338">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="14931-339">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-339">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="14931-340">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-340">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="14931-341">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-341">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="14931-342">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-342">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="14931-343">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-343">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="14931-344">*코드에서 기본값 변경*</span><span class="sxs-lookup"><span data-stu-id="14931-344">*Change the defaults in code*</span></span>

<span data-ttu-id="14931-345">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-345">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="14931-346">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-346">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="14931-347">*SNI에 대한 Kestrel 지원*</span><span class="sxs-lookup"><span data-stu-id="14931-347">*Kestrel support for SNI*</span></span>

<span data-ttu-id="14931-348">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-348">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="14931-349">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-349">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="14931-350">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-350">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="14931-351">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-351">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="14931-352">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-352">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="14931-353">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-353">SNI support requires:</span></span>

* <span data-ttu-id="14931-354">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-354">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="14931-355">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-355">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="14931-356">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-356">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="14931-357">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-357">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="14931-358">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-358">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="14931-359">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="14931-359">Connection logging</span></span>

<span data-ttu-id="14931-360"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-360">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="14931-361">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-361">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="14931-362">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-362">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="14931-363">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-363">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="14931-364">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="14931-364">Bind to a TCP socket</span></span>

<span data-ttu-id="14931-365"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-365">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="14931-366">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-366">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="14931-367">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-367">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="14931-368">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="14931-368">Bind to a Unix socket</span></span>

<span data-ttu-id="14931-369">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-369">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="14931-370">Nginx 구성 파일에서 `server` > `location` > `proxy_pass` 항목을 `http://unix:/tmp/{KESTREL SOCKET}:/;`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-370">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="14931-371">`{KESTREL SOCKET}`은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>에 제공된 소켓의 이름입니다(예: 이전 예제의 `kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="14931-371">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="14931-372">소켓이 Nginx에서 쓰기 가능한지 확인합니다(예: `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="14931-372">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="14931-373">포트 0</span><span class="sxs-lookup"><span data-stu-id="14931-373">Port 0</span></span>

<span data-ttu-id="14931-374">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-374">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="14931-375">다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="14931-375">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="14931-376">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-376">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="14931-377">제한 사항</span><span class="sxs-lookup"><span data-stu-id="14931-377">Limitations</span></span>

<span data-ttu-id="14931-378">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-378">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="14931-379">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="14931-379">`--urls` command-line argument</span></span>
* <span data-ttu-id="14931-380">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="14931-380">`urls` host configuration key</span></span>
* <span data-ttu-id="14931-381">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="14931-381">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="14931-382">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-382">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="14931-383">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-383">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="14931-384">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="14931-384">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="14931-385">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-385">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="14931-386">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="14931-386">IIS endpoint configuration</span></span>

<span data-ttu-id="14931-387">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-387">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="14931-388">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-388">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="14931-389">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="14931-389">ListenOptions.Protocols</span></span>

<span data-ttu-id="14931-390">`Protocols` 속성은 연결 엔드포인트 또는 서버에 대해 사용할 수 있는 HTTP 프로토콜(`HttpProtocols`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-390">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="14931-391">`HttpProtocols` 열거형의 `Protocols` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-391">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="14931-392">`HttpProtocols` 열거형 값</span><span class="sxs-lookup"><span data-stu-id="14931-392">`HttpProtocols` enum value</span></span> | <span data-ttu-id="14931-393">허용되는 연결 프로토콜</span><span class="sxs-lookup"><span data-stu-id="14931-393">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="14931-394">HTTP/1.1 전용.</span><span class="sxs-lookup"><span data-stu-id="14931-394">HTTP/1.1 only.</span></span> <span data-ttu-id="14931-395">TLS와 함께 또는 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-395">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="14931-396">HTTP/2 전용.</span><span class="sxs-lookup"><span data-stu-id="14931-396">HTTP/2 only.</span></span> <span data-ttu-id="14931-397">클라이언트가 [이전 기술 모드](https://tools.ietf.org/html/rfc7540#section-3.4)를 지원하는 경우에만 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-397">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="14931-398">HTTP/1.1 및 HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="14931-398">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="14931-399">HTTP/2를 사용하려면 클라이언트가 TLS [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 핸드셰이크에서 HTTP/2를 선택해야 합니다. 그렇지 않으면 연결 기본값은 HTTP/1.1입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-399">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="14931-400">모든 엔드포인트의 기본 `ListenOptions.Protocols` 값은 `HttpProtocols.Http1AndHttp2`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-400">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="14931-401">HTTP/2에 대한 TLS 제한 사항:</span><span class="sxs-lookup"><span data-stu-id="14931-401">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="14931-402">TLS 버전 1.2 이상</span><span class="sxs-lookup"><span data-stu-id="14931-402">TLS version 1.2 or later</span></span>
* <span data-ttu-id="14931-403">재협상 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="14931-403">Renegotiation disabled</span></span>
* <span data-ttu-id="14931-404">압축 사용 안함</span><span class="sxs-lookup"><span data-stu-id="14931-404">Compression disabled</span></span>
* <span data-ttu-id="14931-405">최소 임시 키 교환 크기:</span><span class="sxs-lookup"><span data-stu-id="14931-405">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="14931-406">ECDHE(타원 곡선 Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 최소 224비트</span><span class="sxs-lookup"><span data-stu-id="14931-406">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="14931-407">DHE(유한체 Diffie-Hellman) &lbrack;`TLS12`&rbrack;: 최소 2048비트</span><span class="sxs-lookup"><span data-stu-id="14931-407">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="14931-408">암호 도구 모음은 금지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-408">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="14931-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;(P-256 타원 곡선 &lbrack;`FIPS186`&rbrack; 포함)는 기본적으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="14931-410">다음 예제는 포트 8000에서 HTTP/1.1 및 HTTP/2 연결을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-410">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="14931-411">연결은 제공된 인증서를 사용하여 TLS로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-411">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="14931-412">필요할 경우 연결 미들웨어를 사용하여 각 연결을 기준으로 특정 암호에 대한 TLS 핸드셰이크를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-412">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="14931-413">다음 예는 앱에서 지원하지 않는 모든 암호화 알고리즘에 대해 <xref:System.NotSupportedException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-413">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="14931-414">또는 [ITlsHandshakeFeature CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm)을 정의하고 허용되는 암호 그룹 목록과 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-414">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="14931-415">[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 암호화 알고리즘과 함께 사용되는 암호화는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-415">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="14931-416"><xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> 람다를 통해 연결 필터링을 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-416">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="14931-417">Linux에서 <xref:System.Net.Security.CipherSuitesPolicy>를 사용하여 각 연결을 기준으로 TLS 핸드셰이크를 필터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-417">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="14931-418">구성에서 프로토콜 설정</span><span class="sxs-lookup"><span data-stu-id="14931-418">*Set the protocol from configuration*</span></span>

<span data-ttu-id="14931-419">`CreateDefaultBuilder`는 기본적으로 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-419">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="14931-420">다음 *appsettings.json* 예제에서는 HTTP/1.1을 모든 엔드포인트의 기본 연결 프로토콜로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-420">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="14931-421">다음 *appsettings.json* 예제에서는 특정 엔드포인트의 HTTP/1.1 연결 프로토콜을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-421">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="14931-422">코드에서 지정한 프로토콜이 구성에서 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-422">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="14931-423">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="14931-423">URL prefixes</span></span>

<span data-ttu-id="14931-424">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="14931-425">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="14931-426">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="14931-427">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="14931-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="14931-428">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="14931-429">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="14931-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="14931-430">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="14931-431">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="14931-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="14931-432">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="14931-433">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="14931-434">서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="14931-435">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="14931-436">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="14931-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="14931-437">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="14931-438">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="14931-439">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="14931-440">호스트 필터링</span><span class="sxs-lookup"><span data-stu-id="14931-440">Host filtering</span></span>

<span data-ttu-id="14931-441">Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="14931-442">호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="14931-443">명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="14931-444">`Host` 헤더의 유효성이 검사되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="14931-445">해결 방법으로 호스트 필터링 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="14931-446">호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공하며, 이 패키지는 ASP.NET Core 앱을 위해 암시적으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="14931-447">미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="14931-448">호스트 필터링 미들웨어는 기본적으로 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="14931-449">미들웨어를 활성화하려면 *appsettings.json* /*appsettings.\<EnvironmentName>.json* 에서 `AllowedHosts` 키를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="14931-450">값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="14931-451">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="14931-451">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="14931-452">[전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="14931-453">전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="14931-454">전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="14931-455">호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="14931-456">전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="14931-457">Libuv 전송 구성</span><span class="sxs-lookup"><span data-stu-id="14931-457">Libuv transport configuration</span></span>

<span data-ttu-id="14931-458">Libuv를 사용해야 하는 프로젝트의 경우(<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span><span class="sxs-lookup"><span data-stu-id="14931-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="14931-459">앱의 프로젝트 파일에 [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) 패키지에 대한 종속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="14931-460">`IWebHostBuilder`에서 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> 호출:</span><span class="sxs-lookup"><span data-stu-id="14931-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseLibuv();
                  webBuilder.UseStartup<Startup>();
              });
  }
  ```

## <a name="http11-request-draining"></a><span data-ttu-id="14931-461">HTTP/1.1 요청 드레이닝</span><span class="sxs-lookup"><span data-stu-id="14931-461">HTTP/1.1 request draining</span></span>

<span data-ttu-id="14931-462">HTTP 연결을 여는 데 시간이 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="14931-462">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="14931-463">HTTPS의 경우 리소스도 많이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-463">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="14931-464">따라서 Kestrel은 HTTP/1.1 프로토콜에 따라 연결을 다시 사용하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-464">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="14931-465">연결을 다시 사용하려면 요청 본문이 완전히 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-465">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="14931-466">서버가 리디렉션 또는 404 응답을 반환하는 경우 앱이 항상 `POST` 요청 같은 요청 본문을 사용하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="14931-466">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="14931-467">`POST` 리디렉션 사례:</span><span class="sxs-lookup"><span data-stu-id="14931-467">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="14931-468">클라이언트가 이미 `POST` 데이터 일부를 전송했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-468">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="14931-469">서버가 301 응답을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-469">The server writes the 301 response.</span></span>
* <span data-ttu-id="14931-470">이전 요청 본문의 `POST` 데이터를 완전히 읽을 때까지 새 요청에 연결을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-470">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="14931-471">Kestrel은 요청 본문을 드레이닝하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-471">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="14931-472">요청 본문을 드레이닝하면 데이터가 처리되지 않고 읽고 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-472">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="14931-473">드레이닝 프로세스는 연결이 다시 사용되도록 허용하는 것과 남은 데이터를 드레이닝하는 데 걸리는 시간을 절충합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-473">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="14931-474">드레이닝의 시간 제한은 5초이며, 이는 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-474">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="14931-475">`Content-Length` 또는 `Transfer-Encoding` 헤더에 지정된 모든 데이터를 시간 제한 전에 읽지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="14931-475">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="14931-476">때에 따라 응답을 쓰기 전이나 쓴 후에 즉시 요청을 종료하려고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-476">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="14931-477">예를 들어 클라이언트는 제한적인 데이터 한도를 포함할 수 있으므로 업로드된 데이터를 제한하는 것이 우선일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-477">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="14931-478">이 경우 요청을 종료하려면 컨트롤러, Razor 페이지 또는 미들웨어에서 [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-478">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="14931-479">`Abort` 호출에 관련된 주의 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-479">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="14931-480">새 연결을 만드는 것은 느리고 비용이 많이 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-480">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="14931-481">연결이 닫히기 전에 클라이언트가 응답을 읽었다는 보장은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-481">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="14931-482">`Abort` 호출은 드문 경우이며 일반적인 오류가 아닌 심각한 오류 사례용으로 예약되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-482">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="14931-483">특정 문제를 해결해야 하는 경우에만 `Abort`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-483">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="14931-484">예를 들어 악의적인 클라이언트가 데이터를 `POST`하려고 하거나 클라이언트 코드에 크거나 많은 요청을 발생시키는 버그가 있는 경우 `Abort`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-484">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="14931-485">HTTP 404(찾을 수 없음)와 같은 일반적인 오류 상황에서는 `Abort`를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="14931-485">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="14931-486">`Abort`를 호출하기 전에 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A)를 호출하면 서버가 응답 쓰기를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-486">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="14931-487">그러나 클라이언트 동작은 예측할 수 없으며 연결이 중단되기 전에 응답을 읽지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-487">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="14931-488">프로토콜이 연결을 닫지 않고 개별 요청 스트림을 중단하는 동작을 지원하기 때문에 HTTP/2의 경우 이 프로세스가 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-488">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="14931-489">5초 드레이닝 시간 제한이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-489">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="14931-490">응답을 완료한 후에 읽지 않은 요청 본문 데이터가 있으면 서버는 HTTP/2 RST 프레임을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-490">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="14931-491">추가 요청 본문 데이터 프레임은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-491">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="14931-492">가능하면 클라이언트가 [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 요청 헤더를 사용하고 서버가 요청 본문을 보내기 시작하기 전에 응답할 때까지 기다리는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-492">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="14931-493">이렇게 하면 클라이언트는 응답을 검사하고 불필요한 데이터를 보내기 전에 중단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-493">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14931-494">추가 자료</span><span class="sxs-lookup"><span data-stu-id="14931-494">Additional resources</span></span>

* <span data-ttu-id="14931-495">Linux에서 UNIX 소켓을 사용하는 경우 앱을 종료할 때 소켓이 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-495">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="14931-496">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/aspnetcore/issues/14134)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-496">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="14931-497">RFC 7230: 메시지 구문 및 라우팅(섹션 5.4: 호스트)</span><span class="sxs-lookup"><span data-stu-id="14931-497">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="14931-498">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-498">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="14931-499">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-499">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="14931-500">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-500">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="14931-501">HTTPS</span><span class="sxs-lookup"><span data-stu-id="14931-501">HTTPS</span></span>
* <span data-ttu-id="14931-502">[Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="14931-502">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="14931-503">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="14931-503">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="14931-504">HTTP/2(macOS&dagger; 제외)</span><span class="sxs-lookup"><span data-stu-id="14931-504">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="14931-505">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-505">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="14931-506">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-506">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="14931-507">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14931-507">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="14931-508">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="14931-508">HTTP/2 support</span></span>

<span data-ttu-id="14931-509">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해 [HTTP/2](https://httpwg.org/specs/rfc7540.html)를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-509">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="14931-510">운영 체제&dagger;</span><span class="sxs-lookup"><span data-stu-id="14931-510">Operating system&dagger;</span></span>
  * <span data-ttu-id="14931-511">Windows Server 2016/Windows 10 이상&Dagger;</span><span class="sxs-lookup"><span data-stu-id="14931-511">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="14931-512">Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)</span><span class="sxs-lookup"><span data-stu-id="14931-512">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="14931-513">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="14931-513">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="14931-514">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="14931-514">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="14931-515">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="14931-515">TLS 1.2 or later connection</span></span>

<span data-ttu-id="14931-516">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-516">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="14931-517">&Dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-517">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="14931-518">이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-518">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="14931-519">TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-519">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="14931-520">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-520">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="14931-521">HTTP/2는 기본적으로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-521">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="14931-522">구성에 대한 자세한 내용은 [Kestrel 옵션](#kestrel-options) 및 [ListenOptions.Protocols](#listenoptionsprotocols) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-522">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="14931-523">Kestrel을 역방향 프록시와 함께 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="14931-523">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="14931-524">Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버* 와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-524">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="14931-525">역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-525">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="14931-526">Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-526">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="14931-528">Kestrel은 역방향 프록시 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-528">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="14931-530">역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-530">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="14931-531">역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-531">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="14931-532">Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-532">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="14931-533">포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-533">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="14931-534">역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-534">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="14931-535">역방향 프록시:</span><span class="sxs-lookup"><span data-stu-id="14931-535">A reverse proxy:</span></span>

* <span data-ttu-id="14931-536">호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-536">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="14931-537">구성 및 방어의 추가 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-537">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="14931-538">기존 인프라와 잘 통합될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-538">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="14931-539">부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-539">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="14931-540">역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-540">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="14931-541">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-541">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="14931-542">ASP.NET Core 앱에서 Kestrel을 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="14931-542">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="14931-543">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-543">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="14931-544">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-544">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="14931-545">*Program.cs* 에서 템플릿 코드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> 숨은 기능을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-545">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="14931-546">`CreateDefaultBuilder`와 호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>의 *호스트 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-546">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="14931-547">`CreateDefaultBuilder`를 호출한 후 추가 구성을 제공하려면 `ConfigureKestrel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-547">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="14931-548">앱이 호스트를 설정하기 위해 `CreateDefaultBuilder`를 호출하지 않는 경우 `ConfigureKestrel`을 호출하기 **전에** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-548">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="14931-549">Kestrel 옵션</span><span class="sxs-lookup"><span data-stu-id="14931-549">Kestrel options</span></span>

<span data-ttu-id="14931-550">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-550">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="14931-551"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-551">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="14931-552">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-552">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="14931-553">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-553">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="14931-554">다음 예에서 C# 코드로 구성된 Kestrel 옵션도 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-554">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="14931-555">예를 들어 파일 구성 공급자는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-555">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="14931-556">다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-556">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="14931-557">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-557">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="14931-558">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-558">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="14931-559">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-559">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="14931-560">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-560">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="14931-561">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-561">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="14931-562">*Program.cs* 에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-562">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="14931-563">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-563">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="14931-564">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="14931-564">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="14931-565">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-565">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="14931-566">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-566">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="14931-567">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="14931-567">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="14931-568">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-568">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="14931-569">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-569">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="14931-570">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-570">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="14931-571">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-571">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="14931-572">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="14931-572">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="14931-573">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-573">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="14931-574">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-574">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="14931-575">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="14931-575">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="14931-576">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-576">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="14931-577">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-577">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="14931-578">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-578">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="14931-579">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-579">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="14931-580">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="14931-580">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="14931-581">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-581">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="14931-582">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-582">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="14931-583">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-583">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="14931-584">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-584">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="14931-585">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-585">A minimum rate also applies to the response.</span></span> <span data-ttu-id="14931-586">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-586">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="14931-587">*Program.cs* 에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-587">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="14931-588">미들웨어에서 요청당 최소 속도 제한을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-588">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="14931-589">요청 멀티플렉싱에 대한 프로토콜의 지원으로 인해 요청별 속도 제한을 수정하는 것이 HTTP/2에 대해 지원되지 않으므로, 이전 샘플에서 참조된 속도 기능은 HTTP/2 요청에 대해 `HttpContext.Features`에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-589">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="14931-590">`KestrelServerOptions.Limits`를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-590">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="14931-591">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="14931-591">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="14931-592">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-592">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="14931-593">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-593">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="14931-594">연결당 최대 스트림</span><span class="sxs-lookup"><span data-stu-id="14931-594">Maximum streams per connection</span></span>

<span data-ttu-id="14931-595">`Http2.MaxStreamsPerConnection`은 HTTP/2 연결당 동시 요청 스트림 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-595">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="14931-596">초과 스트림은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-596">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="14931-597">기본값은 100입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-597">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="14931-598">헤더 테이블 크기</span><span class="sxs-lookup"><span data-stu-id="14931-598">Header table size</span></span>

<span data-ttu-id="14931-599">HPACK 디코더는 HTTP/2 연결에 대한 HTTP 헤더의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="14931-599">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="14931-600">`Http2.HeaderTableSize`는 HPACK 디코더가 사용하는 헤더 압축 테이블의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-600">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="14931-601">값은 8진수로 제공되며 영(0)보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-601">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="14931-602">기본값은 4096입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-602">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="14931-603">최대 프레임 크기</span><span class="sxs-lookup"><span data-stu-id="14931-603">Maximum frame size</span></span>

<span data-ttu-id="14931-604">`Http2.MaxFrameSize`는 수신할 HTTP/2 연결 프레임 페이로드의 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-604">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="14931-605">값은 8진수로 제공되며 2^14(16,384)와 2^24-1(16,777,215) 사이여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-605">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="14931-606">기본값은 2^14(16,384)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-606">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="14931-607">최대 요청 헤더 크기</span><span class="sxs-lookup"><span data-stu-id="14931-607">Maximum request header size</span></span>

<span data-ttu-id="14931-608">`Http2.MaxRequestHeaderFieldSize`는 요청 헤더 값의 8진수로 허용되는 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-608">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="14931-609">이 한도는 이름과 값이 모두 압축된 표현과 압축되지 않은 표현으로 함께 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-609">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="14931-610">값은 0보다 커야 합니다(0).</span><span class="sxs-lookup"><span data-stu-id="14931-610">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="14931-611">기본값은 8,192입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-611">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="14931-612">초기 연결 창 크기</span><span class="sxs-lookup"><span data-stu-id="14931-612">Initial connection window size</span></span>

<span data-ttu-id="14931-613">`Http2.InitialConnectionWindowSize`는 연결당 모든 요청(스트림)을 통해 한 번에 집계하는 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-613">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="14931-614">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-614">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="14931-615">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-615">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="14931-616">기본값은 128KB(131,072)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-616">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="14931-617">초기 스트림 창 크기</span><span class="sxs-lookup"><span data-stu-id="14931-617">Initial stream window size</span></span>

<span data-ttu-id="14931-618">`Http2.InitialStreamWindowSize`는 요청(스트림)당 한 번에 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-618">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="14931-619">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-619">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="14931-620">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-620">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="14931-621">기본값은 96KB(98,304)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-621">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="14931-622">동기 I/O</span><span class="sxs-lookup"><span data-stu-id="14931-622">Synchronous I/O</span></span>

<span data-ttu-id="14931-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 I/O가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="14931-624">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-624">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="14931-625">차단 동기 I/O 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-625">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="14931-626">비동기 I/O를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 사용하도록 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-626">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="14931-627">다음 예제에서는 동기 I/O를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-627">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="14931-628">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-628">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="14931-629">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="14931-629">Endpoint configuration</span></span>

<span data-ttu-id="14931-630">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-630">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="14931-631">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="14931-631">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="14931-632">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-632">Specify URLs using the:</span></span>

* <span data-ttu-id="14931-633">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="14931-633">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="14931-634">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="14931-634">`--urls` command-line argument.</span></span>
* <span data-ttu-id="14931-635">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="14931-635">`urls` host configuration key.</span></span>
* <span data-ttu-id="14931-636">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="14931-636">`UseUrls` extension method.</span></span>

<span data-ttu-id="14931-637">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-637">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="14931-638">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="14931-638">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="14931-639">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-639">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="14931-640">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="14931-640">A development certificate is created:</span></span>

* <span data-ttu-id="14931-641">[.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.</span><span class="sxs-lookup"><span data-stu-id="14931-641">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="14931-642">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-642">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="14931-643">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-643">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="14931-644">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-644">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="14931-645"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-645">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="14931-646">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="14931-646">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="14931-647">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="14931-647">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="14931-648">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="14931-648">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="14931-649">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-649">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="14931-650">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="14931-650">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="14931-651"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-651">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="14931-652">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="14931-652">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="14931-653">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-653">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="14931-654">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="14931-654">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="14931-655"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-655">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="14931-656">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="14931-656">Configure(IConfiguration)</span></span>

<span data-ttu-id="14931-657">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="14931-657">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="14931-658">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-658">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="14931-659">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="14931-659">ListenOptions.UseHttps</span></span>

<span data-ttu-id="14931-660">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-660">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="14931-661">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="14931-661">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="14931-662">`UseHttps`: 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-662">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="14931-663">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-663">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="14931-664">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="14931-664">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="14931-665">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-665">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="14931-666">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-666">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="14931-667">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-667">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="14931-668">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-668">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="14931-669">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-669">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="14931-670">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-670">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="14931-671">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-671">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="14931-672">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-672">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="14931-673">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-673">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="14931-674">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-674">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="14931-675">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-675">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="14931-676">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-676">Supported configurations described next:</span></span>

* <span data-ttu-id="14931-677">구성 없음</span><span class="sxs-lookup"><span data-stu-id="14931-677">No configuration</span></span>
* <span data-ttu-id="14931-678">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="14931-678">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="14931-679">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="14931-679">Change the defaults in code</span></span>

<span data-ttu-id="14931-680">*구성 없음*</span><span class="sxs-lookup"><span data-stu-id="14931-680">*No configuration*</span></span>

<span data-ttu-id="14931-681">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="14931-681">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="14931-682">*구성에서 기본 인증서를 바꿈*</span><span class="sxs-lookup"><span data-stu-id="14931-682">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="14931-683">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-683">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="14931-684">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-684">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="14931-685">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-685">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="14931-686">다음 *appsettings.json* 예제에서</span><span class="sxs-lookup"><span data-stu-id="14931-686">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="14931-687">잘못된 인증서 사용을 허가하려면 **AllowInvalid** 를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="14931-687">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="14931-688">인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본** 에서 정의된 인증서 또는 개발 인증서로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-688">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="14931-689">모든 인증서 노드에 대해 **경로** 및 **암호** 를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-689">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="14931-690">예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-690">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="14931-691">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="14931-691">Schema notes:</span></span>

* <span data-ttu-id="14931-692">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-692">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="14931-693">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-693">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="14931-694">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-694">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="14931-695">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-695">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="14931-696">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="14931-696">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="14931-697">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-697">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="14931-698">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-698">The `Certificate` section is optional.</span></span> <span data-ttu-id="14931-699">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-699">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="14931-700">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-700">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="14931-701">`Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-701">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="14931-702">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-702">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="14931-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="14931-704">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-704">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="14931-705">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-705">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="14931-706">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-706">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="14931-707">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-707">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="14931-708">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-708">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="14931-709">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-709">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="14931-710">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-710">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="14931-711">*코드에서 기본값 변경*</span><span class="sxs-lookup"><span data-stu-id="14931-711">*Change the defaults in code*</span></span>

<span data-ttu-id="14931-712">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-712">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="14931-713">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-713">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="14931-714">*SNI에 대한 Kestrel 지원*</span><span class="sxs-lookup"><span data-stu-id="14931-714">*Kestrel support for SNI*</span></span>

<span data-ttu-id="14931-715">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-715">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="14931-716">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-716">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="14931-717">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-717">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="14931-718">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-718">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="14931-719">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-719">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="14931-720">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-720">SNI support requires:</span></span>

* <span data-ttu-id="14931-721">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-721">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="14931-722">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-722">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="14931-723">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-723">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="14931-724">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-724">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="14931-725">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-725">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="14931-726">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="14931-726">Connection logging</span></span>

<span data-ttu-id="14931-727"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-727">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="14931-728">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-728">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="14931-729">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-729">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="14931-730">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-730">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="14931-731">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="14931-731">Bind to a TCP socket</span></span>

<span data-ttu-id="14931-732"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-732">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="14931-733">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-733">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="14931-734">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-734">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="14931-735">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="14931-735">Bind to a Unix socket</span></span>

<span data-ttu-id="14931-736">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-736">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="14931-737">Nginx 구성 파일에서 `server` > `location` > `proxy_pass` 항목을 `http://unix:/tmp/{KESTREL SOCKET}:/;`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-737">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="14931-738">`{KESTREL SOCKET}`은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>에 제공된 소켓의 이름입니다(예: 이전 예제의 `kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="14931-738">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="14931-739">소켓이 Nginx에서 쓰기 가능한지 확인합니다(예: `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="14931-739">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="14931-740">포트 0</span><span class="sxs-lookup"><span data-stu-id="14931-740">Port 0</span></span>

<span data-ttu-id="14931-741">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-741">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="14931-742">다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="14931-742">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="14931-743">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-743">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="14931-744">제한 사항</span><span class="sxs-lookup"><span data-stu-id="14931-744">Limitations</span></span>

<span data-ttu-id="14931-745">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-745">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="14931-746">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="14931-746">`--urls` command-line argument</span></span>
* <span data-ttu-id="14931-747">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="14931-747">`urls` host configuration key</span></span>
* <span data-ttu-id="14931-748">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="14931-748">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="14931-749">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-749">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="14931-750">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-750">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="14931-751">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="14931-751">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="14931-752">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-752">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="14931-753">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="14931-753">IIS endpoint configuration</span></span>

<span data-ttu-id="14931-754">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-754">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="14931-755">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-755">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="14931-756">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="14931-756">ListenOptions.Protocols</span></span>

<span data-ttu-id="14931-757">`Protocols` 속성은 연결 엔드포인트 또는 서버에 대해 사용할 수 있는 HTTP 프로토콜(`HttpProtocols`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-757">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="14931-758">`HttpProtocols` 열거형의 `Protocols` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-758">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="14931-759">`HttpProtocols` 열거형 값</span><span class="sxs-lookup"><span data-stu-id="14931-759">`HttpProtocols` enum value</span></span> | <span data-ttu-id="14931-760">허용되는 연결 프로토콜</span><span class="sxs-lookup"><span data-stu-id="14931-760">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="14931-761">HTTP/1.1 전용.</span><span class="sxs-lookup"><span data-stu-id="14931-761">HTTP/1.1 only.</span></span> <span data-ttu-id="14931-762">TLS와 함께 또는 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-762">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="14931-763">HTTP/2 전용.</span><span class="sxs-lookup"><span data-stu-id="14931-763">HTTP/2 only.</span></span> <span data-ttu-id="14931-764">클라이언트가 [이전 기술 모드](https://tools.ietf.org/html/rfc7540#section-3.4)를 지원하는 경우에만 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-764">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="14931-765">HTTP/1.1 및 HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="14931-765">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="14931-766">HTTP/2를 사용하려면 TLS 및 [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결이 필요합니다. 그렇지 않은 경우 연결은 기본적으로 HTTP/1.1로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-766">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="14931-767">기본 프로토콜은 HTTP/1.1입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-767">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="14931-768">HTTP/2에 대한 TLS 제한 사항:</span><span class="sxs-lookup"><span data-stu-id="14931-768">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="14931-769">TLS 버전 1.2 이상</span><span class="sxs-lookup"><span data-stu-id="14931-769">TLS version 1.2 or later</span></span>
* <span data-ttu-id="14931-770">재협상 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="14931-770">Renegotiation disabled</span></span>
* <span data-ttu-id="14931-771">압축 사용 안함</span><span class="sxs-lookup"><span data-stu-id="14931-771">Compression disabled</span></span>
* <span data-ttu-id="14931-772">최소 임시 키 교환 크기:</span><span class="sxs-lookup"><span data-stu-id="14931-772">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="14931-773">ECDHE(타원 곡선 Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 최소 224비트</span><span class="sxs-lookup"><span data-stu-id="14931-773">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="14931-774">DHE(유한체 Diffie-Hellman) &lbrack;`TLS12`&rbrack;: 최소 2048비트</span><span class="sxs-lookup"><span data-stu-id="14931-774">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="14931-775">암호 그룹 차단 안 됨</span><span class="sxs-lookup"><span data-stu-id="14931-775">Cipher suite not blocked</span></span>

<span data-ttu-id="14931-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;(P-256 타원 곡선 &lbrack;`FIPS186`&rbrack; 포함)는 기본적으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="14931-777">다음 예제는 포트 8000에서 HTTP/1.1 및 HTTP/2 연결을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-777">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="14931-778">연결은 제공된 인증서를 사용하여 TLS로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-778">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="14931-779">선택적으로, `IConnectionAdapter` 구현을 만들어 각 연결 단위로 특정 암호에 대한 TLS 핸드셰이크를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-779">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="14931-780">구성에서 프로토콜 설정</span><span class="sxs-lookup"><span data-stu-id="14931-780">*Set the protocol from configuration*</span></span>

<span data-ttu-id="14931-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>는 기본적으로 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="14931-782">다음 *appsettings.json* 예제에서는 모든 Kestrel의 엔드포인트에 기본 연결 프로토콜(HTTP/1.1 및 HTTP/2)이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-782">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="14931-783">다음 구성 파일 예제에서는 특정 엔드포인트에 대해 연결 프로토콜을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-783">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="14931-784">코드에서 지정한 프로토콜이 구성에서 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-784">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="14931-785">Libuv 전송 구성</span><span class="sxs-lookup"><span data-stu-id="14931-785">Libuv transport configuration</span></span>

<span data-ttu-id="14931-786">ASP.NET Core 2.1 릴리스에서 Kestrel의 기본 전송은 더 이상 Libuv에 기반하지 않으며 대신 관리 소켓에 기반합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-786">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="14931-787"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출하고 다음 패키지 중 하나를 사용하는 2.1로 업그레이드된 ASP.NET Core 2.0 앱의 주요 변경 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-787">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="14931-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)(직접 패키지 참조)</span><span class="sxs-lookup"><span data-stu-id="14931-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="14931-789">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="14931-789">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="14931-790">Libuv를 사용해야 하는 프로젝트의 경우:</span><span class="sxs-lookup"><span data-stu-id="14931-790">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="14931-791">[Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 패키지에 대한 종속성을 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-791">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="14931-792"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-792">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="14931-793">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="14931-793">URL prefixes</span></span>

<span data-ttu-id="14931-794">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-794">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="14931-795">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-795">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="14931-796">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-796">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="14931-797">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="14931-797">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="14931-798">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-798">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="14931-799">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="14931-799">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="14931-800">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-800">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="14931-801">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="14931-801">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="14931-802">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-802">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="14931-803">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-803">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="14931-804">서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-804">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="14931-805">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-805">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="14931-806">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="14931-806">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="14931-807">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-807">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="14931-808">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-808">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="14931-809">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-809">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="14931-810">호스트 필터링</span><span class="sxs-lookup"><span data-stu-id="14931-810">Host filtering</span></span>

<span data-ttu-id="14931-811">Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-811">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="14931-812">호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-812">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="14931-813">명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-813">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="14931-814">`Host` 헤더의 유효성이 검사되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-814">`Host` headers aren't validated.</span></span>

<span data-ttu-id="14931-815">해결 방법으로 호스트 필터링 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-815">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="14931-816">호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공되고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 또는 2.2)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-816">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="14931-817">미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-817">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="14931-818">호스트 필터링 미들웨어는 기본적으로 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-818">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="14931-819">미들웨어를 활성화하려면 *appsettings.json* /*appsettings.\<EnvironmentName>.json* 에서 `AllowedHosts` 키를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-819">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="14931-820">값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-820">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="14931-821">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="14931-821">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="14931-822">[전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-822">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="14931-823">전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-823">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="14931-824">전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-824">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="14931-825">호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-825">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="14931-826">전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-826">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="14931-827">HTTP/1.1 요청 드레이닝</span><span class="sxs-lookup"><span data-stu-id="14931-827">HTTP/1.1 request draining</span></span>

<span data-ttu-id="14931-828">HTTP 연결을 여는 데 시간이 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="14931-828">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="14931-829">HTTPS의 경우 리소스도 많이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-829">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="14931-830">따라서 Kestrel은 HTTP/1.1 프로토콜에 따라 연결을 다시 사용하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-830">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="14931-831">연결을 다시 사용하려면 요청 본문이 완전히 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-831">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="14931-832">서버가 리디렉션 또는 404 응답을 반환하는 경우 앱이 항상 `POST` 요청 같은 요청 본문을 사용하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="14931-832">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="14931-833">`POST` 리디렉션 사례:</span><span class="sxs-lookup"><span data-stu-id="14931-833">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="14931-834">클라이언트가 이미 `POST` 데이터 일부를 전송했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-834">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="14931-835">서버가 301 응답을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-835">The server writes the 301 response.</span></span>
* <span data-ttu-id="14931-836">이전 요청 본문의 `POST` 데이터를 완전히 읽을 때까지 새 요청에 연결을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-836">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="14931-837">Kestrel은 요청 본문을 드레이닝하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-837">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="14931-838">요청 본문을 드레이닝하면 데이터가 처리되지 않고 읽고 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-838">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="14931-839">드레이닝 프로세스는 연결이 다시 사용되도록 허용하는 것과 남은 데이터를 드레이닝하는 데 걸리는 시간을 절충합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-839">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="14931-840">드레이닝의 시간 제한은 5초이며, 이는 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-840">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="14931-841">`Content-Length` 또는 `Transfer-Encoding` 헤더에 지정된 모든 데이터를 시간 제한 전에 읽지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="14931-841">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="14931-842">때에 따라 응답을 쓰기 전이나 쓴 후에 즉시 요청을 종료하려고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-842">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="14931-843">예를 들어 클라이언트는 제한적인 데이터 한도를 포함할 수 있으므로 업로드된 데이터를 제한하는 것이 우선일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-843">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="14931-844">이 경우 요청을 종료하려면 컨트롤러, Razor 페이지 또는 미들웨어에서 [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-844">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="14931-845">`Abort` 호출에 관련된 주의 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-845">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="14931-846">새 연결을 만드는 것은 느리고 비용이 많이 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-846">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="14931-847">연결이 닫히기 전에 클라이언트가 응답을 읽었다는 보장은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-847">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="14931-848">`Abort` 호출은 드문 경우이며 일반적인 오류가 아닌 심각한 오류 사례용으로 예약되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-848">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="14931-849">특정 문제를 해결해야 하는 경우에만 `Abort`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-849">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="14931-850">예를 들어 악의적인 클라이언트가 데이터를 `POST`하려고 하거나 클라이언트 코드에 크거나 많은 요청을 발생시키는 버그가 있는 경우 `Abort`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-850">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="14931-851">HTTP 404(찾을 수 없음)와 같은 일반적인 오류 상황에서는 `Abort`를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="14931-851">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="14931-852">`Abort`를 호출하기 전에 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A)를 호출하면 서버가 응답 쓰기를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-852">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="14931-853">그러나 클라이언트 동작은 예측할 수 없으며 연결이 중단되기 전에 응답을 읽지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-853">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="14931-854">프로토콜이 연결을 닫지 않고 개별 요청 스트림을 중단하는 동작을 지원하기 때문에 HTTP/2의 경우 이 프로세스가 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-854">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="14931-855">5초 드레이닝 시간 제한이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-855">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="14931-856">응답을 완료한 후에 읽지 않은 요청 본문 데이터가 있으면 서버는 HTTP/2 RST 프레임을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-856">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="14931-857">추가 요청 본문 데이터 프레임은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-857">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="14931-858">가능하면 클라이언트가 [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 요청 헤더를 사용하고 서버가 요청 본문을 보내기 시작하기 전에 응답할 때까지 기다리는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-858">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="14931-859">이렇게 하면 클라이언트는 응답을 검사하고 불필요한 데이터를 보내기 전에 중단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-859">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14931-860">추가 자료</span><span class="sxs-lookup"><span data-stu-id="14931-860">Additional resources</span></span>

* <span data-ttu-id="14931-861">Linux에서 UNIX 소켓을 사용하는 경우 앱을 종료할 때 소켓이 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-861">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="14931-862">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/aspnetcore/issues/14134)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-862">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="14931-863">RFC 7230: 메시지 구문 및 라우팅(섹션 5.4: 호스트)</span><span class="sxs-lookup"><span data-stu-id="14931-863">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="14931-864">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-864">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="14931-865">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-865">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="14931-866">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-866">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="14931-867">HTTPS</span><span class="sxs-lookup"><span data-stu-id="14931-867">HTTPS</span></span>
* <span data-ttu-id="14931-868">[Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="14931-868">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="14931-869">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="14931-869">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="14931-870">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-870">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="14931-871">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14931-871">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="14931-872">Kestrel을 역방향 프록시와 함께 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="14931-872">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="14931-873">Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버* 와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-873">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="14931-874">역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-874">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="14931-875">Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-875">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="14931-877">Kestrel은 역방향 프록시 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-877">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="14931-879">역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-879">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="14931-880">역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-880">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="14931-881">Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-881">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="14931-882">포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-882">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="14931-883">역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-883">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="14931-884">역방향 프록시:</span><span class="sxs-lookup"><span data-stu-id="14931-884">A reverse proxy:</span></span>

* <span data-ttu-id="14931-885">호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-885">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="14931-886">구성 및 방어의 추가 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-886">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="14931-887">기존 인프라와 잘 통합될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-887">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="14931-888">부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-888">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="14931-889">역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-889">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="14931-890">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-890">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="14931-891">ASP.NET Core 앱에서 Kestrel을 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="14931-891">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="14931-892">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-892">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="14931-893">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-893">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="14931-894">*Program.cs* 에서 템플릿 코드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> 숨은 기능을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-894">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="14931-895">`CreateDefaultBuilder`를 호출한 후 추가 구성을 제공하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-895">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="14931-896">`CreateDefaultBuilder`와 호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>의 *호스트 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-896">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="14931-897">Kestrel 옵션</span><span class="sxs-lookup"><span data-stu-id="14931-897">Kestrel options</span></span>

<span data-ttu-id="14931-898">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-898">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="14931-899"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-899">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="14931-900">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-900">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="14931-901">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-901">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="14931-902">다음 예에서 C# 코드로 구성된 Kestrel 옵션도 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-902">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="14931-903">예를 들어 파일 구성 공급자는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-903">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="14931-904">다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-904">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="14931-905">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-905">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="14931-906">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-906">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="14931-907">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-907">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="14931-908">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-908">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="14931-909">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-909">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="14931-910">*Program.cs* 에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-910">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="14931-911">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-911">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="14931-912">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="14931-912">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="14931-913">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-913">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="14931-914">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-914">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="14931-915">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="14931-915">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="14931-916">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-916">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="14931-917">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-917">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="14931-918">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-918">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="14931-919">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-919">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="14931-920">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="14931-920">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="14931-921">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-921">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="14931-922">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-922">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="14931-923">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="14931-923">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="14931-924">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-924">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="14931-925">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-925">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="14931-926">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-926">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="14931-927">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-927">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="14931-928">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="14931-928">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="14931-929">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-929">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="14931-930">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-930">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="14931-931">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-931">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="14931-932">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-932">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="14931-933">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-933">A minimum rate also applies to the response.</span></span> <span data-ttu-id="14931-934">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-934">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="14931-935">*Program.cs* 에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-935">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="14931-936">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="14931-936">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="14931-937">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-937">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="14931-938">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-938">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="14931-939">동기 I/O</span><span class="sxs-lookup"><span data-stu-id="14931-939">Synchronous I/O</span></span>

<span data-ttu-id="14931-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 I/O가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="14931-941">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-941">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="14931-942">차단 동기 I/O 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-942">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="14931-943">비동기 I/O를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 사용하도록 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-943">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="14931-944">다음 예제에서는 동기 I/O를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-944">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="14931-945">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-945">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="14931-946">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="14931-946">Endpoint configuration</span></span>

<span data-ttu-id="14931-947">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-947">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="14931-948">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="14931-948">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="14931-949">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-949">Specify URLs using the:</span></span>

* <span data-ttu-id="14931-950">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="14931-950">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="14931-951">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="14931-951">`--urls` command-line argument.</span></span>
* <span data-ttu-id="14931-952">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="14931-952">`urls` host configuration key.</span></span>
* <span data-ttu-id="14931-953">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="14931-953">`UseUrls` extension method.</span></span>

<span data-ttu-id="14931-954">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-954">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="14931-955">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="14931-955">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="14931-956">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-956">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="14931-957">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="14931-957">A development certificate is created:</span></span>

* <span data-ttu-id="14931-958">[.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.</span><span class="sxs-lookup"><span data-stu-id="14931-958">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="14931-959">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-959">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="14931-960">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-960">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="14931-961">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-961">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="14931-962"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-962">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="14931-963">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="14931-963">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="14931-964">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="14931-964">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="14931-965">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="14931-965">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="14931-966">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-966">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="14931-967">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="14931-967">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="14931-968"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-968">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="14931-969">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="14931-969">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="14931-970">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-970">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="14931-971">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="14931-971">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="14931-972"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-972">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="14931-973">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="14931-973">Configure(IConfiguration)</span></span>

<span data-ttu-id="14931-974">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="14931-974">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="14931-975">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-975">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="14931-976">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="14931-976">ListenOptions.UseHttps</span></span>

<span data-ttu-id="14931-977">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-977">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="14931-978">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="14931-978">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="14931-979">`UseHttps`: 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-979">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="14931-980">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-980">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="14931-981">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="14931-981">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="14931-982">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-982">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="14931-983">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-983">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="14931-984">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-984">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="14931-985">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-985">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="14931-986">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-986">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="14931-987">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-987">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="14931-988">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-988">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="14931-989">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-989">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="14931-990">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-990">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="14931-991">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-991">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="14931-992">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-992">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="14931-993">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-993">Supported configurations described next:</span></span>

* <span data-ttu-id="14931-994">구성 없음</span><span class="sxs-lookup"><span data-stu-id="14931-994">No configuration</span></span>
* <span data-ttu-id="14931-995">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="14931-995">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="14931-996">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="14931-996">Change the defaults in code</span></span>

<span data-ttu-id="14931-997">*구성 없음*</span><span class="sxs-lookup"><span data-stu-id="14931-997">*No configuration*</span></span>

<span data-ttu-id="14931-998">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="14931-998">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="14931-999">*구성에서 기본 인증서를 바꿈*</span><span class="sxs-lookup"><span data-stu-id="14931-999">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="14931-1000">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1000">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="14931-1001">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1001">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="14931-1002">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1002">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="14931-1003">다음 *appsettings.json* 예제에서</span><span class="sxs-lookup"><span data-stu-id="14931-1003">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="14931-1004">잘못된 인증서 사용을 허가하려면 **AllowInvalid** 를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="14931-1004">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="14931-1005">인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본** 에서 정의된 인증서 또는 개발 인증서로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1005">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="14931-1006">모든 인증서 노드에 대해 **경로** 및 **암호** 를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1006">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="14931-1007">예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1007">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="14931-1008">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="14931-1008">Schema notes:</span></span>

* <span data-ttu-id="14931-1009">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1009">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="14931-1010">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1010">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="14931-1011">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1011">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="14931-1012">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1012">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="14931-1013">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1013">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="14931-1014">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1014">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="14931-1015">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1015">The `Certificate` section is optional.</span></span> <span data-ttu-id="14931-1016">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1016">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="14931-1017">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1017">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="14931-1018">`Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1018">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="14931-1019">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1019">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="14931-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="14931-1021">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1021">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="14931-1022">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1022">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="14931-1023">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1023">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="14931-1024">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1024">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="14931-1025">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1025">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="14931-1026">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1026">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="14931-1027">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1027">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="14931-1028">*코드에서 기본값 변경*</span><span class="sxs-lookup"><span data-stu-id="14931-1028">*Change the defaults in code*</span></span>

<span data-ttu-id="14931-1029">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1029">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="14931-1030">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1030">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="14931-1031">*SNI에 대한 Kestrel 지원*</span><span class="sxs-lookup"><span data-stu-id="14931-1031">*Kestrel support for SNI*</span></span>

<span data-ttu-id="14931-1032">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1032">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="14931-1033">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1033">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="14931-1034">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1034">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="14931-1035">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1035">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="14931-1036">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1036">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="14931-1037">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1037">SNI support requires:</span></span>

* <span data-ttu-id="14931-1038">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1038">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="14931-1039">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1039">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="14931-1040">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1040">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="14931-1041">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1041">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="14931-1042">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1042">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="14931-1043">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="14931-1043">Connection logging</span></span>

<span data-ttu-id="14931-1044"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1044">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="14931-1045">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1045">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="14931-1046">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1046">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="14931-1047">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1047">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="14931-1048">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="14931-1048">Bind to a TCP socket</span></span>

<span data-ttu-id="14931-1049"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1049">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="14931-1050">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1050">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="14931-1051">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1051">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="14931-1052">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="14931-1052">Bind to a Unix socket</span></span>

<span data-ttu-id="14931-1053">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1053">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="14931-1054">Nginx 구성 파일에서 `server` > `location` > `proxy_pass` 항목을 `http://unix:/tmp/{KESTREL SOCKET}:/;`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1054">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="14931-1055">`{KESTREL SOCKET}`은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>에 제공된 소켓의 이름입니다(예: 이전 예제의 `kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="14931-1055">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="14931-1056">소켓이 Nginx에서 쓰기 가능한지 확인합니다(예: `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="14931-1056">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="14931-1057">포트 0</span><span class="sxs-lookup"><span data-stu-id="14931-1057">Port 0</span></span>

<span data-ttu-id="14931-1058">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1058">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="14931-1059">다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1059">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="14931-1060">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1060">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="14931-1061">제한 사항</span><span class="sxs-lookup"><span data-stu-id="14931-1061">Limitations</span></span>

<span data-ttu-id="14931-1062">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1062">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="14931-1063">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="14931-1063">`--urls` command-line argument</span></span>
* <span data-ttu-id="14931-1064">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="14931-1064">`urls` host configuration key</span></span>
* <span data-ttu-id="14931-1065">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="14931-1065">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="14931-1066">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1066">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="14931-1067">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-1067">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="14931-1068">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="14931-1068">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="14931-1069">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1069">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="14931-1070">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="14931-1070">IIS endpoint configuration</span></span>

<span data-ttu-id="14931-1071">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1071">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="14931-1072">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-1072">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="14931-1073">Libuv 전송 구성</span><span class="sxs-lookup"><span data-stu-id="14931-1073">Libuv transport configuration</span></span>

<span data-ttu-id="14931-1074">ASP.NET Core 2.1 릴리스에서 Kestrel의 기본 전송은 더 이상 Libuv에 기반하지 않으며 대신 관리 소켓에 기반합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1074">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="14931-1075"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출하고 다음 패키지 중 하나를 사용하는 2.1로 업그레이드된 ASP.NET Core 2.0 앱의 주요 변경 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1075">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="14931-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)(직접 패키지 참조)</span><span class="sxs-lookup"><span data-stu-id="14931-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="14931-1077">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="14931-1077">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="14931-1078">Libuv를 사용해야 하는 프로젝트의 경우:</span><span class="sxs-lookup"><span data-stu-id="14931-1078">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="14931-1079">[Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 패키지에 대한 종속성을 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1079">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="14931-1080"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1080">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="14931-1081">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="14931-1081">URL prefixes</span></span>

<span data-ttu-id="14931-1082">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1082">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="14931-1083">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1083">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="14931-1084">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1084">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="14931-1085">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="14931-1085">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="14931-1086">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1086">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="14931-1087">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="14931-1087">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="14931-1088">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1088">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="14931-1089">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="14931-1089">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="14931-1090">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1090">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="14931-1091">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1091">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="14931-1092">서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1092">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="14931-1093">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1093">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="14931-1094">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="14931-1094">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="14931-1095">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1095">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="14931-1096">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1096">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="14931-1097">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1097">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="14931-1098">호스트 필터링</span><span class="sxs-lookup"><span data-stu-id="14931-1098">Host filtering</span></span>

<span data-ttu-id="14931-1099">Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1099">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="14931-1100">호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1100">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="14931-1101">명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1101">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="14931-1102">`Host` 헤더의 유효성이 검사되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1102">`Host` headers aren't validated.</span></span>

<span data-ttu-id="14931-1103">해결 방법으로 호스트 필터링 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1103">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="14931-1104">호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공되고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 또는 2.2)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1104">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="14931-1105">미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1105">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="14931-1106">호스트 필터링 미들웨어는 기본적으로 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1106">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="14931-1107">미들웨어를 활성화하려면 *appsettings.json* /*appsettings.\<EnvironmentName>.json* 에서 `AllowedHosts` 키를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1107">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="14931-1108">값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1108">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="14931-1109">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="14931-1109">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="14931-1110">[전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1110">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="14931-1111">전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1111">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="14931-1112">전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1112">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="14931-1113">호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1113">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="14931-1114">전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-1114">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="14931-1115">HTTP/1.1 요청 드레이닝</span><span class="sxs-lookup"><span data-stu-id="14931-1115">HTTP/1.1 request draining</span></span>

<span data-ttu-id="14931-1116">HTTP 연결을 여는 데 시간이 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1116">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="14931-1117">HTTPS의 경우 리소스도 많이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1117">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="14931-1118">따라서 Kestrel은 HTTP/1.1 프로토콜에 따라 연결을 다시 사용하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1118">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="14931-1119">연결을 다시 사용하려면 요청 본문이 완전히 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1119">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="14931-1120">서버가 리디렉션 또는 404 응답을 반환하는 경우 앱이 항상 `POST` 요청 같은 요청 본문을 사용하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1120">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="14931-1121">`POST` 리디렉션 사례:</span><span class="sxs-lookup"><span data-stu-id="14931-1121">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="14931-1122">클라이언트가 이미 `POST` 데이터 일부를 전송했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1122">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="14931-1123">서버가 301 응답을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1123">The server writes the 301 response.</span></span>
* <span data-ttu-id="14931-1124">이전 요청 본문의 `POST` 데이터를 완전히 읽을 때까지 새 요청에 연결을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1124">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="14931-1125">Kestrel은 요청 본문을 드레이닝하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1125">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="14931-1126">요청 본문을 드레이닝하면 데이터가 처리되지 않고 읽고 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1126">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="14931-1127">드레이닝 프로세스는 연결이 다시 사용되도록 허용하는 것과 남은 데이터를 드레이닝하는 데 걸리는 시간을 절충합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1127">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="14931-1128">드레이닝의 시간 제한은 5초이며, 이는 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1128">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="14931-1129">`Content-Length` 또는 `Transfer-Encoding` 헤더에 지정된 모든 데이터를 시간 제한 전에 읽지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1129">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="14931-1130">때에 따라 응답을 쓰기 전이나 쓴 후에 즉시 요청을 종료하려고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1130">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="14931-1131">예를 들어 클라이언트는 제한적인 데이터 한도를 포함할 수 있으므로 업로드된 데이터를 제한하는 것이 우선일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1131">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="14931-1132">이 경우 요청을 종료하려면 컨트롤러, Razor 페이지 또는 미들웨어에서 [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1132">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="14931-1133">`Abort` 호출에 관련된 주의 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1133">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="14931-1134">새 연결을 만드는 것은 느리고 비용이 많이 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1134">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="14931-1135">연결이 닫히기 전에 클라이언트가 응답을 읽었다는 보장은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1135">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="14931-1136">`Abort` 호출은 드문 경우이며 일반적인 오류가 아닌 심각한 오류 사례용으로 예약되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1136">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="14931-1137">특정 문제를 해결해야 하는 경우에만 `Abort`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1137">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="14931-1138">예를 들어 악의적인 클라이언트가 데이터를 `POST`하려고 하거나 클라이언트 코드에 크거나 많은 요청을 발생시키는 버그가 있는 경우 `Abort`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1138">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="14931-1139">HTTP 404(찾을 수 없음)와 같은 일반적인 오류 상황에서는 `Abort`를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="14931-1139">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="14931-1140">`Abort`를 호출하기 전에 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A)를 호출하면 서버가 응답 쓰기를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1140">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="14931-1141">그러나 클라이언트 동작은 예측할 수 없으며 연결이 중단되기 전에 응답을 읽지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1141">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="14931-1142">프로토콜이 연결을 닫지 않고 개별 요청 스트림을 중단하는 동작을 지원하기 때문에 HTTP/2의 경우 이 프로세스가 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1142">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="14931-1143">5초 드레이닝 시간 제한이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1143">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="14931-1144">응답을 완료한 후에 읽지 않은 요청 본문 데이터가 있으면 서버는 HTTP/2 RST 프레임을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1144">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="14931-1145">추가 요청 본문 데이터 프레임은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1145">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="14931-1146">가능하면 클라이언트가 [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 요청 헤더를 사용하고 서버가 요청 본문을 보내기 시작하기 전에 응답할 때까지 기다리는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1146">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="14931-1147">이렇게 하면 클라이언트는 응답을 검사하고 불필요한 데이터를 보내기 전에 중단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1147">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14931-1148">추가 자료</span><span class="sxs-lookup"><span data-stu-id="14931-1148">Additional resources</span></span>

* <span data-ttu-id="14931-1149">Linux에서 UNIX 소켓을 사용하는 경우 앱을 종료할 때 소켓이 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14931-1149">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="14931-1150">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/aspnetcore/issues/14134)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14931-1150">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="14931-1151">RFC 7230: 메시지 구문 및 라우팅(섹션 5.4: 호스트)</span><span class="sxs-lookup"><span data-stu-id="14931-1151">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end
