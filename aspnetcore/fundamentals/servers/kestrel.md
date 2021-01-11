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
ms.openlocfilehash: 5c9e1717ad603687343f015826a113e6945e4a41
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854615"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="3700c-103">ASP.NET Core에서 Kestrel 웹 서버 구현</span><span class="sxs-lookup"><span data-stu-id="3700c-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="3700c-104">작성자: [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) 및 [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="3700c-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3700c-105">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="3700c-106">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="3700c-107">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="3700c-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3700c-108">HTTPS</span></span>
* <span data-ttu-id="3700c-109">[Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="3700c-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="3700c-110">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="3700c-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="3700c-111">HTTP/2(macOS&dagger; 제외)</span><span class="sxs-lookup"><span data-stu-id="3700c-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="3700c-112">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="3700c-113">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="3700c-114">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3700c-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="3700c-115">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="3700c-115">HTTP/2 support</span></span>

<span data-ttu-id="3700c-116">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해 [HTTP/2](https://httpwg.org/specs/rfc7540.html)를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="3700c-117">운영 체제&dagger;</span><span class="sxs-lookup"><span data-stu-id="3700c-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="3700c-118">Windows Server 2016/Windows 10 이상&Dagger;</span><span class="sxs-lookup"><span data-stu-id="3700c-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="3700c-119">Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)</span><span class="sxs-lookup"><span data-stu-id="3700c-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="3700c-120">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="3700c-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="3700c-121">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="3700c-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="3700c-122">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="3700c-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="3700c-123">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="3700c-124">&Dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="3700c-125">이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="3700c-126">TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="3700c-127">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="3700c-128">HTTP/2는 기본적으로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="3700c-129">구성에 대한 자세한 내용은 [Kestrel 옵션](#kestrel-options) 및 [ListenOptions.Protocols](#listenoptionsprotocols) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="3700c-130">Kestrel을 역방향 프록시와 함께 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="3700c-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="3700c-131">Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버* 와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="3700c-132">역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="3700c-133">Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="3700c-135">Kestrel은 역방향 프록시 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="3700c-137">역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="3700c-138">역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="3700c-139">Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="3700c-140">포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="3700c-141">역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="3700c-142">역방향 프록시:</span><span class="sxs-lookup"><span data-stu-id="3700c-142">A reverse proxy:</span></span>

* <span data-ttu-id="3700c-143">호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="3700c-144">구성 및 방어의 추가 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="3700c-145">기존 인프라와 잘 통합될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="3700c-146">부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="3700c-147">역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="3700c-148">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="3700c-149">ASP.NET Core 앱의 Kestrel</span><span class="sxs-lookup"><span data-stu-id="3700c-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="3700c-150">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="3700c-151">*Program.cs* 에서 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="3700c-152">호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#set-up-a-host>의 *호스트 설정* 및 *기본 작성기 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="3700c-153">`ConfigureWebHostDefaults`를 호출한 후 추가 구성을 제공하려면 `ConfigureKestrel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="3700c-154">Kestrel 옵션</span><span class="sxs-lookup"><span data-stu-id="3700c-154">Kestrel options</span></span>

<span data-ttu-id="3700c-155">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="3700c-156"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="3700c-157">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="3700c-158">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="3700c-159">이 문서의 뒷부분에 나오는 예제에서 Kestrel 옵션은 C# 코드로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="3700c-160">Kestrel 옵션은 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="3700c-161">예를 들어 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider)는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="3700c-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 및 [엔드포인트 구성](#endpoint-configuration)은 구성 공급자에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="3700c-163">나머지 Kestrel 구성은 C# 코드로 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="3700c-164">다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="3700c-165">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="3700c-166">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="3700c-167">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="3700c-168">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="3700c-169">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="3700c-170">*Program.cs* 에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="3700c-171">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="3700c-172">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="3700c-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="3700c-173">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="3700c-174">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="3700c-175">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="3700c-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="3700c-176">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="3700c-177">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="3700c-178">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="3700c-179">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="3700c-180">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="3700c-181">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="3700c-182">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="3700c-183">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="3700c-184">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="3700c-185">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="3700c-186">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="3700c-187">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="3700c-188">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="3700c-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="3700c-189">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="3700c-190">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="3700c-191">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="3700c-192">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="3700c-193">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="3700c-194">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="3700c-195">*Program.cs* 에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="3700c-196">미들웨어에서 요청당 최소 속도 제한을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="3700c-197">요청 멀티플렉싱에 대한 프로토콜의 지원으로 인해 요청별 속도 제한을 수정하는 것이 HTTP/2에 대해 일반적으로 지원되지 않으므로, 이전 샘플에서 참조된 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>는 HTTP/2 요청에 대해 `HttpContext.Features`에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="3700c-198">그러나 HTTP/2 요청에 대해서도 `IHttpMinRequestBodyDataRateFeature.MinDataRate`를 `null`로 설정하여 요청별 읽기 속도 제한을 *완전히 비활성화* 할 수 있으므로 HTTP/2 요청에 대한 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>는 여전히 존재합니다`HttpContext.Features`.</span><span class="sxs-lookup"><span data-stu-id="3700c-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="3700c-199">`IHttpMinRequestBodyDataRateFeature.MinDataRate` 읽기를 시도하거나 `null`이 아닌 값으로 설정하려고 하면 HTTP/2 요청이 있을 때 `NotSupportedException`이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="3700c-200">`KestrelServerOptions.Limits`를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="3700c-201">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="3700c-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="3700c-202">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="3700c-203">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="3700c-204">연결당 최대 스트림</span><span class="sxs-lookup"><span data-stu-id="3700c-204">Maximum streams per connection</span></span>

<span data-ttu-id="3700c-205">`Http2.MaxStreamsPerConnection`은 HTTP/2 연결당 동시 요청 스트림 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="3700c-206">초과 스트림은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="3700c-207">기본값은 100입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="3700c-208">헤더 테이블 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-208">Header table size</span></span>

<span data-ttu-id="3700c-209">HPACK 디코더는 HTTP/2 연결에 대한 HTTP 헤더의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="3700c-210">`Http2.HeaderTableSize`는 HPACK 디코더가 사용하는 헤더 압축 테이블의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="3700c-211">값은 8진수로 제공되며 영(0)보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="3700c-212">기본값은 4096입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="3700c-213">최대 프레임 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-213">Maximum frame size</span></span>

<span data-ttu-id="3700c-214">`Http2.MaxFrameSize`는 서버에서 받거나 보낸 HTTP/2 연결 프레임 페이로드의 최대 허용 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="3700c-215">값은 8진수로 제공되며 2^14(16,384)와 2^24-1(16,777,215) 사이여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="3700c-216">기본값은 2^14(16,384)입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="3700c-217">최대 요청 헤더 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-217">Maximum request header size</span></span>

<span data-ttu-id="3700c-218">`Http2.MaxRequestHeaderFieldSize`는 요청 헤더 값의 8진수로 허용되는 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="3700c-219">이 한도는 모두 압축된 표현과 압축되지 않은 표현으로 이름과 값 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="3700c-220">값은 0보다 커야 합니다(0).</span><span class="sxs-lookup"><span data-stu-id="3700c-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="3700c-221">기본값은 8,192입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="3700c-222">초기 연결 창 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-222">Initial connection window size</span></span>

<span data-ttu-id="3700c-223">`Http2.InitialConnectionWindowSize`는 연결당 모든 요청(스트림)을 통해 한 번에 집계하는 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="3700c-224">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="3700c-225">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="3700c-226">기본값은 128KB(131,072)입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="3700c-227">초기 스트림 창 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-227">Initial stream window size</span></span>

<span data-ttu-id="3700c-228">`Http2.InitialStreamWindowSize`는 요청(스트림)당 한 번에 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="3700c-229">요청은 `Http2.InitialConnectionWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="3700c-230">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="3700c-231">기본값은 96KB(98,304)입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-231">The default value is 96 KB (98,304).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="3700c-232">HTTP/2 연결 유지 ping 구성</span><span class="sxs-lookup"><span data-stu-id="3700c-232">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="3700c-233">연결된 클라이언트로 HTTP/2 ping을 보내도록 Kestrel을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-233">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="3700c-234">HTTP/2 ping은 여러 가지 용도로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-234">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="3700c-235">유휴 연결을 활성 상태로 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-235">Keep idle connections alive.</span></span> <span data-ttu-id="3700c-236">일부 클라이언트 및 프록시 서버는 유휴 상태인 연결을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-236">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="3700c-237">HTTP/2 ping은 연결에 대한 작업으로 간주하며 연결이 유휴 상태로 닫히지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-237">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="3700c-238">비정상 연결을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-238">Close unhealthy connections.</span></span> <span data-ttu-id="3700c-239">클라이언트가 구성된 시간에 연결 유지 ping에 응답하지 않는 연결은 서버에서 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-239">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="3700c-240">HTTP/2 연결 유지 ping과 관련된 두 가지 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-240">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="3700c-241">`Http2.KeepAlivePingInterval`은 ping 내부를 구성하는 `TimeSpan`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-241">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping internal.</span></span> <span data-ttu-id="3700c-242">서버가 이 기간 동안 프레임을 받지 못하면 클라이언트로 연결 유지 ping을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-242">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="3700c-243">이 옵션을 `TimeSpan.MaxValue`로 설정하면 연결 유지 ping을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-243">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="3700c-244">기본값은 `TimeSpan.MaxValue`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-244">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="3700c-245">`Http2.KeepAlivePingTimeout`은 ping 시간 제한을 구성하는 `TimeSpan`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-245">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="3700c-246">서버가 이 시간 제한 동안 응답 ping과 같은 프레임을 받지 못하면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-246">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="3700c-247">이 옵션을 `TimeSpan.MaxValue`로 설정하면 연결 유지 시간 제한을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-247">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="3700c-248">기본값은 20초입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-248">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="trailers"></a><span data-ttu-id="3700c-249">트레일러</span><span class="sxs-lookup"><span data-stu-id="3700c-249">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="3700c-250">다시 설정</span><span class="sxs-lookup"><span data-stu-id="3700c-250">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="3700c-251">동기 I/O</span><span class="sxs-lookup"><span data-stu-id="3700c-251">Synchronous I/O</span></span>

<span data-ttu-id="3700c-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 I/O가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="3700c-253">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-253">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="3700c-254">차단 동기 I/O 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-254">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="3700c-255">비동기 I/O를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 사용하도록 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-255">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="3700c-256">다음 예제에서는 동기 I/O를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-256">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="3700c-257">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-257">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="3700c-258">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="3700c-258">Endpoint configuration</span></span>

<span data-ttu-id="3700c-259">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-259">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="3700c-260">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="3700c-260">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="3700c-261">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-261">Specify URLs using the:</span></span>

* <span data-ttu-id="3700c-262">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="3700c-262">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="3700c-263">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="3700c-263">`--urls` command-line argument.</span></span>
* <span data-ttu-id="3700c-264">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="3700c-264">`urls` host configuration key.</span></span>
* <span data-ttu-id="3700c-265">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="3700c-265">`UseUrls` extension method.</span></span>

<span data-ttu-id="3700c-266">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-266">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="3700c-267">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="3700c-267">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="3700c-268">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-268">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="3700c-269">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-269">A development certificate is created:</span></span>

* <span data-ttu-id="3700c-270">[.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.</span><span class="sxs-lookup"><span data-stu-id="3700c-270">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="3700c-271">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-271">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="3700c-272">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-272">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="3700c-273">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-273">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3700c-274"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-274">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="3700c-275">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="3700c-275">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="3700c-276">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="3700c-276">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="3700c-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="3700c-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="3700c-278">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-278">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="3700c-279">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-279">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="3700c-280"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-280">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="3700c-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="3700c-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="3700c-282">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-282">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="3700c-283">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-283">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="3700c-284"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="3700c-285">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="3700c-285">Configure(IConfiguration)</span></span>

<span data-ttu-id="3700c-286">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-286">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="3700c-287">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-287">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="3700c-288">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="3700c-288">ListenOptions.UseHttps</span></span>

<span data-ttu-id="3700c-289">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-289">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="3700c-290">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="3700c-290">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="3700c-291">`UseHttps`: 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-291">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="3700c-292">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-292">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="3700c-293">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="3700c-293">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="3700c-294">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-294">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="3700c-295">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-295">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="3700c-296">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-296">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="3700c-297">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-297">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="3700c-298">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-298">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="3700c-299">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-299">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="3700c-300">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-300">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="3700c-301">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-301">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="3700c-302">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-302">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="3700c-303">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-303">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="3700c-304">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-304">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="3700c-305">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-305">Supported configurations described next:</span></span>

* <span data-ttu-id="3700c-306">구성 없음</span><span class="sxs-lookup"><span data-stu-id="3700c-306">No configuration</span></span>
* <span data-ttu-id="3700c-307">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="3700c-307">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="3700c-308">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="3700c-308">Change the defaults in code</span></span>

<span data-ttu-id="3700c-309">*구성 없음*</span><span class="sxs-lookup"><span data-stu-id="3700c-309">*No configuration*</span></span>

<span data-ttu-id="3700c-310">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="3700c-310">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="3700c-311">*구성에서 기본 인증서를 바꿈*</span><span class="sxs-lookup"><span data-stu-id="3700c-311">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="3700c-312">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-312">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="3700c-313">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-313">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="3700c-314">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-314">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="3700c-315">다음 *appsettings.json* 예제에서</span><span class="sxs-lookup"><span data-stu-id="3700c-315">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="3700c-316">잘못된 인증서 사용을 허가하려면 **AllowInvalid** 를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="3700c-316">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="3700c-317">인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본** 에서 정의된 인증서 또는 개발 인증서로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-317">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="3700c-318">모든 인증서 노드에 대해 **경로** 및 **암호** 를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-318">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="3700c-319">예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-319">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="3700c-320">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="3700c-320">Schema notes:</span></span>

* <span data-ttu-id="3700c-321">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-321">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="3700c-322">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-322">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="3700c-323">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-323">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="3700c-324">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-324">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="3700c-325">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-325">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="3700c-326">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-326">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="3700c-327">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-327">The `Certificate` section is optional.</span></span> <span data-ttu-id="3700c-328">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-328">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="3700c-329">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-329">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="3700c-330">`Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-330">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="3700c-331">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-331">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="3700c-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="3700c-333">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-333">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="3700c-334">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-334">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="3700c-335">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-335">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="3700c-336">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-336">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="3700c-337">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-337">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="3700c-338">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-338">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="3700c-339">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-339">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="3700c-340">*코드에서 기본값 변경*</span><span class="sxs-lookup"><span data-stu-id="3700c-340">*Change the defaults in code*</span></span>

<span data-ttu-id="3700c-341">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-341">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="3700c-342">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-342">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="3700c-343">*SNI에 대한 Kestrel 지원*</span><span class="sxs-lookup"><span data-stu-id="3700c-343">*Kestrel support for SNI*</span></span>

<span data-ttu-id="3700c-344">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="3700c-345">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-345">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="3700c-346">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-346">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="3700c-347">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-347">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="3700c-348">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-348">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="3700c-349">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-349">SNI support requires:</span></span>

* <span data-ttu-id="3700c-350">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-350">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="3700c-351">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-351">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="3700c-352">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-352">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="3700c-353">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-353">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="3700c-354">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-354">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="3700c-355">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="3700c-355">Connection logging</span></span>

<span data-ttu-id="3700c-356"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-356">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="3700c-357">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-357">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="3700c-358">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-358">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="3700c-359">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-359">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="3700c-360">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="3700c-360">Bind to a TCP socket</span></span>

<span data-ttu-id="3700c-361"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-361">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="3700c-362">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-362">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="3700c-363">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-363">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="3700c-364">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="3700c-364">Bind to a Unix socket</span></span>

<span data-ttu-id="3700c-365">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-365">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="3700c-366">Nginx 구성 파일에서 `server` > `location` > `proxy_pass` 항목을 `http://unix:/tmp/{KESTREL SOCKET}:/;`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-366">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="3700c-367">`{KESTREL SOCKET}`은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>에 제공된 소켓의 이름입니다(예: 이전 예제의 `kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="3700c-367">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="3700c-368">소켓이 Nginx에서 쓰기 가능한지 확인합니다(예: `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="3700c-368">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="3700c-369">포트 0</span><span class="sxs-lookup"><span data-stu-id="3700c-369">Port 0</span></span>

<span data-ttu-id="3700c-370">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-370">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="3700c-371">다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-371">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="3700c-372">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-372">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="3700c-373">제한 사항</span><span class="sxs-lookup"><span data-stu-id="3700c-373">Limitations</span></span>

<span data-ttu-id="3700c-374">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-374">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="3700c-375">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="3700c-375">`--urls` command-line argument</span></span>
* <span data-ttu-id="3700c-376">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="3700c-376">`urls` host configuration key</span></span>
* <span data-ttu-id="3700c-377">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="3700c-377">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="3700c-378">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-378">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="3700c-379">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-379">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="3700c-380">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="3700c-380">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="3700c-381">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-381">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="3700c-382">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="3700c-382">IIS endpoint configuration</span></span>

<span data-ttu-id="3700c-383">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-383">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="3700c-384">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-384">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="3700c-385">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="3700c-385">ListenOptions.Protocols</span></span>

<span data-ttu-id="3700c-386">`Protocols` 속성은 연결 엔드포인트 또는 서버에 대해 사용할 수 있는 HTTP 프로토콜(`HttpProtocols`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-386">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="3700c-387">`HttpProtocols` 열거형의 `Protocols` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-387">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="3700c-388">`HttpProtocols` 열거형 값</span><span class="sxs-lookup"><span data-stu-id="3700c-388">`HttpProtocols` enum value</span></span> | <span data-ttu-id="3700c-389">허용되는 연결 프로토콜</span><span class="sxs-lookup"><span data-stu-id="3700c-389">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="3700c-390">HTTP/1.1 전용.</span><span class="sxs-lookup"><span data-stu-id="3700c-390">HTTP/1.1 only.</span></span> <span data-ttu-id="3700c-391">TLS와 함께 또는 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-391">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="3700c-392">HTTP/2 전용.</span><span class="sxs-lookup"><span data-stu-id="3700c-392">HTTP/2 only.</span></span> <span data-ttu-id="3700c-393">클라이언트가 [이전 기술 모드](https://tools.ietf.org/html/rfc7540#section-3.4)를 지원하는 경우에만 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-393">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="3700c-394">HTTP/1.1 및 HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="3700c-394">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="3700c-395">HTTP/2를 사용하려면 클라이언트가 TLS [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 핸드셰이크에서 HTTP/2를 선택해야 합니다. 그렇지 않으면 연결 기본값은 HTTP/1.1입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-395">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="3700c-396">모든 엔드포인트의 기본 `ListenOptions.Protocols` 값은 `HttpProtocols.Http1AndHttp2`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-396">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="3700c-397">HTTP/2에 대한 TLS 제한 사항:</span><span class="sxs-lookup"><span data-stu-id="3700c-397">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="3700c-398">TLS 버전 1.2 이상</span><span class="sxs-lookup"><span data-stu-id="3700c-398">TLS version 1.2 or later</span></span>
* <span data-ttu-id="3700c-399">재협상 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="3700c-399">Renegotiation disabled</span></span>
* <span data-ttu-id="3700c-400">압축 사용 안함</span><span class="sxs-lookup"><span data-stu-id="3700c-400">Compression disabled</span></span>
* <span data-ttu-id="3700c-401">최소 임시 키 교환 크기:</span><span class="sxs-lookup"><span data-stu-id="3700c-401">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="3700c-402">ECDHE(타원 곡선 Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 최소 224비트</span><span class="sxs-lookup"><span data-stu-id="3700c-402">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="3700c-403">DHE(유한체 Diffie-Hellman) &lbrack;`TLS12`&rbrack;: 최소 2048비트</span><span class="sxs-lookup"><span data-stu-id="3700c-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="3700c-404">암호 도구 모음은 금지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-404">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="3700c-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;(P-256 타원 곡선 &lbrack;`FIPS186`&rbrack; 포함)는 기본적으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="3700c-406">다음 예제는 포트 8000에서 HTTP/1.1 및 HTTP/2 연결을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-406">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="3700c-407">연결은 제공된 인증서를 사용하여 TLS로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-407">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="3700c-408">필요할 경우 연결 미들웨어를 사용하여 각 연결을 기준으로 특정 암호에 대한 TLS 핸드셰이크를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-408">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="3700c-409">다음 예는 앱에서 지원하지 않는 모든 암호화 알고리즘에 대해 <xref:System.NotSupportedException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-409">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="3700c-410">또는 [ITlsHandshakeFeature CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm)을 정의하고 허용되는 암호 그룹 목록과 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-410">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="3700c-411">[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 암호화 알고리즘과 함께 사용되는 암호화는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-411">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="3700c-412"><xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> 람다를 통해 연결 필터링을 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-412">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="3700c-413">Linux에서 <xref:System.Net.Security.CipherSuitesPolicy>를 사용하여 각 연결을 기준으로 TLS 핸드셰이크를 필터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-413">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="3700c-414">구성에서 프로토콜 설정</span><span class="sxs-lookup"><span data-stu-id="3700c-414">*Set the protocol from configuration*</span></span>

<span data-ttu-id="3700c-415">`CreateDefaultBuilder`는 기본적으로 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-415">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="3700c-416">다음 *appsettings.json* 예제에서는 HTTP/1.1을 모든 엔드포인트의 기본 연결 프로토콜로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-416">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="3700c-417">다음 *appsettings.json* 예제에서는 특정 엔드포인트의 HTTP/1.1 연결 프로토콜을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-417">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="3700c-418">코드에서 지정한 프로토콜이 구성에서 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-418">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="3700c-419">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="3700c-419">URL prefixes</span></span>

<span data-ttu-id="3700c-420">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-420">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="3700c-421">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-421">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="3700c-422">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-422">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="3700c-423">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="3700c-423">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="3700c-424">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-424">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="3700c-425">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="3700c-425">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="3700c-426">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-426">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="3700c-427">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="3700c-427">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="3700c-428">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-428">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="3700c-429">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-429">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="3700c-430">서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-430">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="3700c-431">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-431">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="3700c-432">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="3700c-432">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="3700c-433">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-433">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="3700c-434">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-434">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="3700c-435">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-435">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="3700c-436">호스트 필터링</span><span class="sxs-lookup"><span data-stu-id="3700c-436">Host filtering</span></span>

<span data-ttu-id="3700c-437">Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-437">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="3700c-438">호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-438">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="3700c-439">명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-439">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="3700c-440">`Host` 헤더의 유효성이 검사되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-440">`Host` headers aren't validated.</span></span>

<span data-ttu-id="3700c-441">해결 방법으로 호스트 필터링 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-441">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="3700c-442">호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공하며, 이 패키지는 ASP.NET Core 앱을 위해 암시적으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-442">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="3700c-443">미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-443">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="3700c-444">호스트 필터링 미들웨어는 기본적으로 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-444">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="3700c-445">미들웨어를 활성화하려면 *appsettings.json* /*appsettings.\<EnvironmentName>.json* 에서 `AllowedHosts` 키를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-445">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="3700c-446">값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-446">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="3700c-447">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3700c-447">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="3700c-448">[전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-448">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="3700c-449">전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-449">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="3700c-450">전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-450">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="3700c-451">호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-451">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="3700c-452">전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-452">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="libuv-transport-configuration"></a><span data-ttu-id="3700c-453">Libuv 전송 구성</span><span class="sxs-lookup"><span data-stu-id="3700c-453">Libuv transport configuration</span></span>

<span data-ttu-id="3700c-454">ASP.NET Core 5.0 기준으로 Kestrel의 Libuv 전송은 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-454">As of ASP.NET Core 5.0, Kestrel's Libuv transport is obsolete.</span></span> <span data-ttu-id="3700c-455">Libuv 전송은 Windows ARM64와 같은 새로운 OS 플랫폼을 지원하는 업데이트를 수신하지 않으며, 이후 릴리스에서 제거될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-455">Libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="3700c-456">사용되지 않는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> 메서드에 대한 모든 호출을 제거하고 대신 Kestrel의 기본 소켓 전송을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-456">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="libuv-transport-configuration"></a><span data-ttu-id="3700c-457">Libuv 전송 구성</span><span class="sxs-lookup"><span data-stu-id="3700c-457">Libuv transport configuration</span></span>

<span data-ttu-id="3700c-458">Libuv를 사용해야 하는 프로젝트의 경우(<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span><span class="sxs-lookup"><span data-stu-id="3700c-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="3700c-459">앱의 프로젝트 파일에 [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) 패키지에 대한 종속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="3700c-460">`IWebHostBuilder`에서 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> 호출:</span><span class="sxs-lookup"><span data-stu-id="3700c-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

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

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="3700c-461">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-461">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="3700c-462">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-462">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="3700c-463">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-463">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="3700c-464">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3700c-464">HTTPS</span></span>
* <span data-ttu-id="3700c-465">[Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="3700c-465">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="3700c-466">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="3700c-466">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="3700c-467">HTTP/2(macOS&dagger; 제외)</span><span class="sxs-lookup"><span data-stu-id="3700c-467">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="3700c-468">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-468">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="3700c-469">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-469">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="3700c-470">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3700c-470">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="3700c-471">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="3700c-471">HTTP/2 support</span></span>

<span data-ttu-id="3700c-472">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해 [HTTP/2](https://httpwg.org/specs/rfc7540.html)를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-472">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="3700c-473">운영 체제&dagger;</span><span class="sxs-lookup"><span data-stu-id="3700c-473">Operating system&dagger;</span></span>
  * <span data-ttu-id="3700c-474">Windows Server 2016/Windows 10 이상&Dagger;</span><span class="sxs-lookup"><span data-stu-id="3700c-474">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="3700c-475">Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)</span><span class="sxs-lookup"><span data-stu-id="3700c-475">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="3700c-476">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="3700c-476">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="3700c-477">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="3700c-477">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="3700c-478">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="3700c-478">TLS 1.2 or later connection</span></span>

<span data-ttu-id="3700c-479">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-479">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="3700c-480">&Dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-480">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="3700c-481">이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-481">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="3700c-482">TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-482">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="3700c-483">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-483">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="3700c-484">HTTP/2는 기본적으로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-484">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="3700c-485">구성에 대한 자세한 내용은 [Kestrel 옵션](#kestrel-options) 및 [ListenOptions.Protocols](#listenoptionsprotocols) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-485">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="3700c-486">Kestrel을 역방향 프록시와 함께 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="3700c-486">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="3700c-487">Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버* 와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-487">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="3700c-488">역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-488">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="3700c-489">Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-489">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="3700c-491">Kestrel은 역방향 프록시 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-491">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="3700c-493">역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-493">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="3700c-494">역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-494">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="3700c-495">Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-495">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="3700c-496">포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-496">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="3700c-497">역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-497">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="3700c-498">역방향 프록시:</span><span class="sxs-lookup"><span data-stu-id="3700c-498">A reverse proxy:</span></span>

* <span data-ttu-id="3700c-499">호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-499">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="3700c-500">구성 및 방어의 추가 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-500">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="3700c-501">기존 인프라와 잘 통합될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-501">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="3700c-502">부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-502">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="3700c-503">역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-503">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="3700c-504">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-504">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="3700c-505">ASP.NET Core 앱에서 Kestrel을 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="3700c-505">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="3700c-506">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-506">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3700c-507">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-507">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="3700c-508">*Program.cs* 에서 템플릿 코드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> 숨은 기능을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-508">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="3700c-509">`CreateDefaultBuilder`와 호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>의 *호스트 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-509">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="3700c-510">`CreateDefaultBuilder`를 호출한 후 추가 구성을 제공하려면 `ConfigureKestrel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-510">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="3700c-511">앱이 호스트를 설정하기 위해 `CreateDefaultBuilder`를 호출하지 않는 경우 `ConfigureKestrel`을 호출하기 **전에** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-511">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="3700c-512">Kestrel 옵션</span><span class="sxs-lookup"><span data-stu-id="3700c-512">Kestrel options</span></span>

<span data-ttu-id="3700c-513">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-513">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="3700c-514"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-514">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="3700c-515">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-515">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="3700c-516">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-516">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="3700c-517">다음 예에서 C# 코드로 구성된 Kestrel 옵션도 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-517">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="3700c-518">예를 들어 파일 구성 공급자는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-518">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="3700c-519">다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-519">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="3700c-520">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-520">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="3700c-521">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-521">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="3700c-522">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-522">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="3700c-523">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-523">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="3700c-524">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-524">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="3700c-525">*Program.cs* 에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-525">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="3700c-526">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-526">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="3700c-527">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="3700c-527">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="3700c-528">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-528">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="3700c-529">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-529">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="3700c-530">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="3700c-530">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="3700c-531">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-531">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="3700c-532">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-532">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="3700c-533">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-533">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="3700c-534">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-534">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="3700c-535">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-535">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="3700c-536">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-536">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="3700c-537">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-537">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="3700c-538">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-538">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="3700c-539">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-539">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="3700c-540">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-540">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="3700c-541">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-541">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="3700c-542">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-542">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="3700c-543">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="3700c-543">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="3700c-544">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-544">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="3700c-545">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-545">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="3700c-546">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-546">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="3700c-547">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-547">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="3700c-548">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-548">A minimum rate also applies to the response.</span></span> <span data-ttu-id="3700c-549">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-549">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="3700c-550">*Program.cs* 에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-550">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="3700c-551">미들웨어에서 요청당 최소 속도 제한을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-551">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="3700c-552">요청 멀티플렉싱에 대한 프로토콜의 지원으로 인해 요청별 속도 제한을 수정하는 것이 HTTP/2에 대해 지원되지 않으므로, 이전 샘플에서 참조된 속도 기능은 HTTP/2 요청에 대해 `HttpContext.Features`에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-552">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="3700c-553">`KestrelServerOptions.Limits`를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-553">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="3700c-554">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="3700c-554">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="3700c-555">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-555">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="3700c-556">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-556">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="3700c-557">연결당 최대 스트림</span><span class="sxs-lookup"><span data-stu-id="3700c-557">Maximum streams per connection</span></span>

<span data-ttu-id="3700c-558">`Http2.MaxStreamsPerConnection`은 HTTP/2 연결당 동시 요청 스트림 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-558">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="3700c-559">초과 스트림은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-559">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="3700c-560">기본값은 100입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-560">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="3700c-561">헤더 테이블 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-561">Header table size</span></span>

<span data-ttu-id="3700c-562">HPACK 디코더는 HTTP/2 연결에 대한 HTTP 헤더의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-562">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="3700c-563">`Http2.HeaderTableSize`는 HPACK 디코더가 사용하는 헤더 압축 테이블의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-563">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="3700c-564">값은 8진수로 제공되며 영(0)보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-564">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="3700c-565">기본값은 4096입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-565">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="3700c-566">최대 프레임 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-566">Maximum frame size</span></span>

<span data-ttu-id="3700c-567">`Http2.MaxFrameSize`는 수신할 HTTP/2 연결 프레임 페이로드의 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-567">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="3700c-568">값은 8진수로 제공되며 2^14(16,384)와 2^24-1(16,777,215) 사이여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-568">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="3700c-569">기본값은 2^14(16,384)입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-569">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="3700c-570">최대 요청 헤더 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-570">Maximum request header size</span></span>

<span data-ttu-id="3700c-571">`Http2.MaxRequestHeaderFieldSize`는 요청 헤더 값의 8진수로 허용되는 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-571">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="3700c-572">이 한도는 이름과 값이 모두 압축된 표현과 압축되지 않은 표현으로 함께 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-572">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="3700c-573">값은 0보다 커야 합니다(0).</span><span class="sxs-lookup"><span data-stu-id="3700c-573">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="3700c-574">기본값은 8,192입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-574">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="3700c-575">초기 연결 창 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-575">Initial connection window size</span></span>

<span data-ttu-id="3700c-576">`Http2.InitialConnectionWindowSize`는 연결당 모든 요청(스트림)을 통해 한 번에 집계하는 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-576">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="3700c-577">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-577">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="3700c-578">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-578">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="3700c-579">기본값은 128KB(131,072)입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-579">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="3700c-580">초기 스트림 창 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-580">Initial stream window size</span></span>

<span data-ttu-id="3700c-581">`Http2.InitialStreamWindowSize`는 요청(스트림)당 한 번에 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-581">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="3700c-582">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-582">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="3700c-583">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-583">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="3700c-584">기본값은 96KB(98,304)입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-584">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="3700c-585">동기 I/O</span><span class="sxs-lookup"><span data-stu-id="3700c-585">Synchronous I/O</span></span>

<span data-ttu-id="3700c-586"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 I/O가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-586"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="3700c-587">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-587">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="3700c-588">차단 동기 I/O 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-588">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="3700c-589">비동기 I/O를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 사용하도록 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-589">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="3700c-590">다음 예제에서는 동기 I/O를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-590">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="3700c-591">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-591">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="3700c-592">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="3700c-592">Endpoint configuration</span></span>

<span data-ttu-id="3700c-593">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-593">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="3700c-594">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="3700c-594">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="3700c-595">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-595">Specify URLs using the:</span></span>

* <span data-ttu-id="3700c-596">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="3700c-596">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="3700c-597">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="3700c-597">`--urls` command-line argument.</span></span>
* <span data-ttu-id="3700c-598">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="3700c-598">`urls` host configuration key.</span></span>
* <span data-ttu-id="3700c-599">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="3700c-599">`UseUrls` extension method.</span></span>

<span data-ttu-id="3700c-600">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-600">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="3700c-601">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="3700c-601">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="3700c-602">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-602">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="3700c-603">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-603">A development certificate is created:</span></span>

* <span data-ttu-id="3700c-604">[.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.</span><span class="sxs-lookup"><span data-stu-id="3700c-604">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="3700c-605">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-605">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="3700c-606">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-606">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="3700c-607">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-607">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3700c-608"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-608">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="3700c-609">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="3700c-609">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="3700c-610">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="3700c-610">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="3700c-611">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="3700c-611">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="3700c-612">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-612">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="3700c-613">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-613">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="3700c-614"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-614">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="3700c-615">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="3700c-615">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="3700c-616">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-616">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="3700c-617">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-617">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="3700c-618"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-618">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="3700c-619">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="3700c-619">Configure(IConfiguration)</span></span>

<span data-ttu-id="3700c-620">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-620">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="3700c-621">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-621">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="3700c-622">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="3700c-622">ListenOptions.UseHttps</span></span>

<span data-ttu-id="3700c-623">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-623">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="3700c-624">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="3700c-624">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="3700c-625">`UseHttps`: 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-625">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="3700c-626">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-626">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="3700c-627">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="3700c-627">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="3700c-628">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-628">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="3700c-629">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-629">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="3700c-630">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-630">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="3700c-631">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-631">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="3700c-632">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-632">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="3700c-633">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-633">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="3700c-634">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-634">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="3700c-635">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-635">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="3700c-636">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-636">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="3700c-637">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-637">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="3700c-638">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-638">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="3700c-639">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-639">Supported configurations described next:</span></span>

* <span data-ttu-id="3700c-640">구성 없음</span><span class="sxs-lookup"><span data-stu-id="3700c-640">No configuration</span></span>
* <span data-ttu-id="3700c-641">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="3700c-641">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="3700c-642">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="3700c-642">Change the defaults in code</span></span>

<span data-ttu-id="3700c-643">*구성 없음*</span><span class="sxs-lookup"><span data-stu-id="3700c-643">*No configuration*</span></span>

<span data-ttu-id="3700c-644">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="3700c-644">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="3700c-645">*구성에서 기본 인증서를 바꿈*</span><span class="sxs-lookup"><span data-stu-id="3700c-645">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="3700c-646">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-646">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="3700c-647">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-647">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="3700c-648">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-648">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="3700c-649">다음 *appsettings.json* 예제에서</span><span class="sxs-lookup"><span data-stu-id="3700c-649">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="3700c-650">잘못된 인증서 사용을 허가하려면 **AllowInvalid** 를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="3700c-650">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="3700c-651">인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본** 에서 정의된 인증서 또는 개발 인증서로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-651">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="3700c-652">모든 인증서 노드에 대해 **경로** 및 **암호** 를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-652">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="3700c-653">예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-653">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="3700c-654">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="3700c-654">Schema notes:</span></span>

* <span data-ttu-id="3700c-655">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-655">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="3700c-656">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-656">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="3700c-657">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-657">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="3700c-658">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-658">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="3700c-659">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-659">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="3700c-660">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-660">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="3700c-661">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-661">The `Certificate` section is optional.</span></span> <span data-ttu-id="3700c-662">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-662">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="3700c-663">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-663">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="3700c-664">`Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-664">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="3700c-665">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-665">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="3700c-666">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-666">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="3700c-667">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-667">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="3700c-668">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-668">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="3700c-669">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-669">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="3700c-670">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-670">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="3700c-671">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-671">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="3700c-672">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-672">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="3700c-673">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-673">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="3700c-674">*코드에서 기본값 변경*</span><span class="sxs-lookup"><span data-stu-id="3700c-674">*Change the defaults in code*</span></span>

<span data-ttu-id="3700c-675">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-675">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="3700c-676">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-676">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="3700c-677">*SNI에 대한 Kestrel 지원*</span><span class="sxs-lookup"><span data-stu-id="3700c-677">*Kestrel support for SNI*</span></span>

<span data-ttu-id="3700c-678">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-678">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="3700c-679">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-679">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="3700c-680">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-680">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="3700c-681">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-681">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="3700c-682">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-682">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="3700c-683">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-683">SNI support requires:</span></span>

* <span data-ttu-id="3700c-684">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-684">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="3700c-685">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-685">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="3700c-686">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-686">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="3700c-687">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-687">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="3700c-688">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-688">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="3700c-689">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="3700c-689">Connection logging</span></span>

<span data-ttu-id="3700c-690"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-690">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="3700c-691">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-691">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="3700c-692">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-692">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="3700c-693">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-693">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="3700c-694">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="3700c-694">Bind to a TCP socket</span></span>

<span data-ttu-id="3700c-695"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-695">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="3700c-696">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-696">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="3700c-697">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-697">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="3700c-698">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="3700c-698">Bind to a Unix socket</span></span>

<span data-ttu-id="3700c-699">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-699">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="3700c-700">Nginx 구성 파일에서 `server` > `location` > `proxy_pass` 항목을 `http://unix:/tmp/{KESTREL SOCKET}:/;`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-700">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="3700c-701">`{KESTREL SOCKET}`은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>에 제공된 소켓의 이름입니다(예: 이전 예제의 `kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="3700c-701">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="3700c-702">소켓이 Nginx에서 쓰기 가능한지 확인합니다(예: `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="3700c-702">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="3700c-703">포트 0</span><span class="sxs-lookup"><span data-stu-id="3700c-703">Port 0</span></span>

<span data-ttu-id="3700c-704">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-704">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="3700c-705">다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-705">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="3700c-706">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-706">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="3700c-707">제한 사항</span><span class="sxs-lookup"><span data-stu-id="3700c-707">Limitations</span></span>

<span data-ttu-id="3700c-708">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-708">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="3700c-709">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="3700c-709">`--urls` command-line argument</span></span>
* <span data-ttu-id="3700c-710">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="3700c-710">`urls` host configuration key</span></span>
* <span data-ttu-id="3700c-711">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="3700c-711">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="3700c-712">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-712">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="3700c-713">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-713">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="3700c-714">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="3700c-714">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="3700c-715">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-715">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="3700c-716">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="3700c-716">IIS endpoint configuration</span></span>

<span data-ttu-id="3700c-717">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-717">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="3700c-718">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-718">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="3700c-719">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="3700c-719">ListenOptions.Protocols</span></span>

<span data-ttu-id="3700c-720">`Protocols` 속성은 연결 엔드포인트 또는 서버에 대해 사용할 수 있는 HTTP 프로토콜(`HttpProtocols`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-720">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="3700c-721">`HttpProtocols` 열거형의 `Protocols` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-721">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="3700c-722">`HttpProtocols` 열거형 값</span><span class="sxs-lookup"><span data-stu-id="3700c-722">`HttpProtocols` enum value</span></span> | <span data-ttu-id="3700c-723">허용되는 연결 프로토콜</span><span class="sxs-lookup"><span data-stu-id="3700c-723">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="3700c-724">HTTP/1.1 전용.</span><span class="sxs-lookup"><span data-stu-id="3700c-724">HTTP/1.1 only.</span></span> <span data-ttu-id="3700c-725">TLS와 함께 또는 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-725">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="3700c-726">HTTP/2 전용.</span><span class="sxs-lookup"><span data-stu-id="3700c-726">HTTP/2 only.</span></span> <span data-ttu-id="3700c-727">클라이언트가 [이전 기술 모드](https://tools.ietf.org/html/rfc7540#section-3.4)를 지원하는 경우에만 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-727">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="3700c-728">HTTP/1.1 및 HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="3700c-728">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="3700c-729">HTTP/2를 사용하려면 TLS 및 [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결이 필요합니다. 그렇지 않은 경우 연결은 기본적으로 HTTP/1.1로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-729">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="3700c-730">기본 프로토콜은 HTTP/1.1입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-730">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="3700c-731">HTTP/2에 대한 TLS 제한 사항:</span><span class="sxs-lookup"><span data-stu-id="3700c-731">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="3700c-732">TLS 버전 1.2 이상</span><span class="sxs-lookup"><span data-stu-id="3700c-732">TLS version 1.2 or later</span></span>
* <span data-ttu-id="3700c-733">재협상 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="3700c-733">Renegotiation disabled</span></span>
* <span data-ttu-id="3700c-734">압축 사용 안함</span><span class="sxs-lookup"><span data-stu-id="3700c-734">Compression disabled</span></span>
* <span data-ttu-id="3700c-735">최소 임시 키 교환 크기:</span><span class="sxs-lookup"><span data-stu-id="3700c-735">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="3700c-736">ECDHE(타원 곡선 Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 최소 224비트</span><span class="sxs-lookup"><span data-stu-id="3700c-736">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="3700c-737">DHE(유한체 Diffie-Hellman) &lbrack;`TLS12`&rbrack;: 최소 2048비트</span><span class="sxs-lookup"><span data-stu-id="3700c-737">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="3700c-738">암호 그룹 차단 안 됨</span><span class="sxs-lookup"><span data-stu-id="3700c-738">Cipher suite not blocked</span></span>

<span data-ttu-id="3700c-739">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;(P-256 타원 곡선 &lbrack;`FIPS186`&rbrack; 포함)는 기본적으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-739">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="3700c-740">다음 예제는 포트 8000에서 HTTP/1.1 및 HTTP/2 연결을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-740">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="3700c-741">연결은 제공된 인증서를 사용하여 TLS로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-741">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="3700c-742">선택적으로, `IConnectionAdapter` 구현을 만들어 각 연결 단위로 특정 암호에 대한 TLS 핸드셰이크를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-742">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="3700c-743">구성에서 프로토콜 설정</span><span class="sxs-lookup"><span data-stu-id="3700c-743">*Set the protocol from configuration*</span></span>

<span data-ttu-id="3700c-744"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>는 기본적으로 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-744"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="3700c-745">다음 *appsettings.json* 예제에서는 모든 Kestrel의 엔드포인트에 기본 연결 프로토콜(HTTP/1.1 및 HTTP/2)이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-745">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="3700c-746">다음 구성 파일 예제에서는 특정 엔드포인트에 대해 연결 프로토콜을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-746">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="3700c-747">코드에서 지정한 프로토콜이 구성에서 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-747">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="3700c-748">Libuv 전송 구성</span><span class="sxs-lookup"><span data-stu-id="3700c-748">Libuv transport configuration</span></span>

<span data-ttu-id="3700c-749">ASP.NET Core 2.1 릴리스에서 Kestrel의 기본 전송은 더 이상 Libuv에 기반하지 않으며 대신 관리 소켓에 기반합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-749">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="3700c-750"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출하고 다음 패키지 중 하나를 사용하는 2.1로 업그레이드된 ASP.NET Core 2.0 앱의 주요 변경 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-750">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="3700c-751">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)(직접 패키지 참조)</span><span class="sxs-lookup"><span data-stu-id="3700c-751">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="3700c-752">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="3700c-752">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="3700c-753">Libuv를 사용해야 하는 프로젝트의 경우:</span><span class="sxs-lookup"><span data-stu-id="3700c-753">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="3700c-754">[Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 패키지에 대한 종속성을 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-754">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="3700c-755"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-755">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="3700c-756">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="3700c-756">URL prefixes</span></span>

<span data-ttu-id="3700c-757">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-757">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="3700c-758">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-758">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="3700c-759">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-759">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="3700c-760">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="3700c-760">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="3700c-761">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-761">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="3700c-762">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="3700c-762">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="3700c-763">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-763">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="3700c-764">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="3700c-764">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="3700c-765">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-765">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="3700c-766">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-766">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="3700c-767">서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-767">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="3700c-768">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-768">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="3700c-769">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="3700c-769">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="3700c-770">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-770">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="3700c-771">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-771">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="3700c-772">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-772">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="3700c-773">호스트 필터링</span><span class="sxs-lookup"><span data-stu-id="3700c-773">Host filtering</span></span>

<span data-ttu-id="3700c-774">Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-774">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="3700c-775">호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-775">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="3700c-776">명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-776">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="3700c-777">`Host` 헤더의 유효성이 검사되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-777">`Host` headers aren't validated.</span></span>

<span data-ttu-id="3700c-778">해결 방법으로 호스트 필터링 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-778">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="3700c-779">호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공되고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 또는 2.2)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-779">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="3700c-780">미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-780">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="3700c-781">호스트 필터링 미들웨어는 기본적으로 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-781">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="3700c-782">미들웨어를 활성화하려면 *appsettings.json* /*appsettings.\<EnvironmentName>.json* 에서 `AllowedHosts` 키를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-782">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="3700c-783">값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-783">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="3700c-784">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3700c-784">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="3700c-785">[전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-785">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="3700c-786">전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-786">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="3700c-787">전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-787">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="3700c-788">호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-788">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="3700c-789">전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-789">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="3700c-790">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-790">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="3700c-791">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-791">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="3700c-792">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-792">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="3700c-793">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3700c-793">HTTPS</span></span>
* <span data-ttu-id="3700c-794">[Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="3700c-794">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="3700c-795">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="3700c-795">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="3700c-796">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-796">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="3700c-797">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3700c-797">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="3700c-798">Kestrel을 역방향 프록시와 함께 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="3700c-798">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="3700c-799">Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버* 와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-799">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="3700c-800">역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-800">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="3700c-801">Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-801">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="3700c-803">Kestrel은 역방향 프록시 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-803">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="3700c-805">역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-805">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="3700c-806">역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-806">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="3700c-807">Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-807">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="3700c-808">포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-808">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="3700c-809">역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-809">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="3700c-810">역방향 프록시:</span><span class="sxs-lookup"><span data-stu-id="3700c-810">A reverse proxy:</span></span>

* <span data-ttu-id="3700c-811">호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-811">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="3700c-812">구성 및 방어의 추가 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-812">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="3700c-813">기존 인프라와 잘 통합될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-813">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="3700c-814">부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-814">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="3700c-815">역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-815">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="3700c-816">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-816">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="3700c-817">ASP.NET Core 앱에서 Kestrel을 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="3700c-817">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="3700c-818">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-818">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3700c-819">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-819">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="3700c-820">*Program.cs* 에서 템플릿 코드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> 숨은 기능을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-820">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="3700c-821">`CreateDefaultBuilder`를 호출한 후 추가 구성을 제공하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-821">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="3700c-822">`CreateDefaultBuilder`와 호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>의 *호스트 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-822">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="3700c-823">Kestrel 옵션</span><span class="sxs-lookup"><span data-stu-id="3700c-823">Kestrel options</span></span>

<span data-ttu-id="3700c-824">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-824">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="3700c-825"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-825">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="3700c-826">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-826">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="3700c-827">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-827">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="3700c-828">다음 예에서 C# 코드로 구성된 Kestrel 옵션도 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-828">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="3700c-829">예를 들어 파일 구성 공급자는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-829">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="3700c-830">다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-830">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="3700c-831">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-831">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="3700c-832">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-832">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="3700c-833">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-833">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="3700c-834">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-834">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="3700c-835">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-835">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="3700c-836">*Program.cs* 에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-836">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="3700c-837">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-837">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="3700c-838">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="3700c-838">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="3700c-839">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-839">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="3700c-840">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-840">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="3700c-841">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="3700c-841">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="3700c-842">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-842">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="3700c-843">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-843">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="3700c-844">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-844">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="3700c-845">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-845">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="3700c-846">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="3700c-846">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="3700c-847">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-847">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="3700c-848">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-848">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="3700c-849">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-849">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="3700c-850">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-850">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="3700c-851">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-851">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="3700c-852">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-852">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="3700c-853">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-853">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="3700c-854">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="3700c-854">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="3700c-855">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-855">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="3700c-856">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-856">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="3700c-857">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-857">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="3700c-858">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-858">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="3700c-859">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-859">A minimum rate also applies to the response.</span></span> <span data-ttu-id="3700c-860">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-860">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="3700c-861">*Program.cs* 에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-861">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="3700c-862">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="3700c-862">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="3700c-863">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-863">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="3700c-864">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-864">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="3700c-865">동기 I/O</span><span class="sxs-lookup"><span data-stu-id="3700c-865">Synchronous I/O</span></span>

<span data-ttu-id="3700c-866"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 I/O가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-866"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="3700c-867">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-867">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="3700c-868">차단 동기 I/O 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-868">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="3700c-869">비동기 I/O를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 사용하도록 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-869">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="3700c-870">다음 예제에서는 동기 I/O를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-870">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="3700c-871">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-871">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="3700c-872">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="3700c-872">Endpoint configuration</span></span>

<span data-ttu-id="3700c-873">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-873">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="3700c-874">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="3700c-874">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="3700c-875">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-875">Specify URLs using the:</span></span>

* <span data-ttu-id="3700c-876">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="3700c-876">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="3700c-877">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="3700c-877">`--urls` command-line argument.</span></span>
* <span data-ttu-id="3700c-878">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="3700c-878">`urls` host configuration key.</span></span>
* <span data-ttu-id="3700c-879">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="3700c-879">`UseUrls` extension method.</span></span>

<span data-ttu-id="3700c-880">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-880">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="3700c-881">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="3700c-881">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="3700c-882">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-882">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="3700c-883">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-883">A development certificate is created:</span></span>

* <span data-ttu-id="3700c-884">[.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.</span><span class="sxs-lookup"><span data-stu-id="3700c-884">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="3700c-885">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-885">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="3700c-886">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-886">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="3700c-887">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-887">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3700c-888"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-888">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="3700c-889">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="3700c-889">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="3700c-890">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="3700c-890">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="3700c-891">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="3700c-891">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="3700c-892">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-892">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="3700c-893">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-893">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="3700c-894"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-894">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="3700c-895">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="3700c-895">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="3700c-896">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-896">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="3700c-897">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-897">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="3700c-898"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-898">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="3700c-899">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="3700c-899">Configure(IConfiguration)</span></span>

<span data-ttu-id="3700c-900">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-900">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="3700c-901">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-901">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="3700c-902">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="3700c-902">ListenOptions.UseHttps</span></span>

<span data-ttu-id="3700c-903">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-903">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="3700c-904">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="3700c-904">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="3700c-905">`UseHttps`: 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-905">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="3700c-906">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-906">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="3700c-907">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="3700c-907">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="3700c-908">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-908">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="3700c-909">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-909">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="3700c-910">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-910">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="3700c-911">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-911">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="3700c-912">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-912">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="3700c-913">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-913">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="3700c-914">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-914">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="3700c-915">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-915">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="3700c-916">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-916">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="3700c-917">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-917">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="3700c-918">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-918">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="3700c-919">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-919">Supported configurations described next:</span></span>

* <span data-ttu-id="3700c-920">구성 없음</span><span class="sxs-lookup"><span data-stu-id="3700c-920">No configuration</span></span>
* <span data-ttu-id="3700c-921">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="3700c-921">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="3700c-922">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="3700c-922">Change the defaults in code</span></span>

<span data-ttu-id="3700c-923">*구성 없음*</span><span class="sxs-lookup"><span data-stu-id="3700c-923">*No configuration*</span></span>

<span data-ttu-id="3700c-924">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="3700c-924">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="3700c-925">*구성에서 기본 인증서를 바꿈*</span><span class="sxs-lookup"><span data-stu-id="3700c-925">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="3700c-926">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-926">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="3700c-927">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-927">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="3700c-928">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-928">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="3700c-929">다음 *appsettings.json* 예제에서</span><span class="sxs-lookup"><span data-stu-id="3700c-929">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="3700c-930">잘못된 인증서 사용을 허가하려면 **AllowInvalid** 를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="3700c-930">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="3700c-931">인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본** 에서 정의된 인증서 또는 개발 인증서로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-931">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="3700c-932">모든 인증서 노드에 대해 **경로** 및 **암호** 를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-932">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="3700c-933">예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-933">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="3700c-934">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="3700c-934">Schema notes:</span></span>

* <span data-ttu-id="3700c-935">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-935">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="3700c-936">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-936">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="3700c-937">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-937">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="3700c-938">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-938">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="3700c-939">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-939">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="3700c-940">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-940">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="3700c-941">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-941">The `Certificate` section is optional.</span></span> <span data-ttu-id="3700c-942">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-942">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="3700c-943">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-943">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="3700c-944">`Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-944">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="3700c-945">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-945">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="3700c-946">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-946">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="3700c-947">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-947">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="3700c-948">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-948">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="3700c-949">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-949">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="3700c-950">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-950">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="3700c-951">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-951">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="3700c-952">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-952">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="3700c-953">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-953">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="3700c-954">*코드에서 기본값 변경*</span><span class="sxs-lookup"><span data-stu-id="3700c-954">*Change the defaults in code*</span></span>

<span data-ttu-id="3700c-955">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-955">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="3700c-956">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-956">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="3700c-957">*SNI에 대한 Kestrel 지원*</span><span class="sxs-lookup"><span data-stu-id="3700c-957">*Kestrel support for SNI*</span></span>

<span data-ttu-id="3700c-958">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-958">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="3700c-959">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-959">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="3700c-960">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-960">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="3700c-961">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-961">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="3700c-962">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-962">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="3700c-963">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-963">SNI support requires:</span></span>

* <span data-ttu-id="3700c-964">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-964">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="3700c-965">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-965">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="3700c-966">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-966">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="3700c-967">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-967">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="3700c-968">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-968">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="3700c-969">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="3700c-969">Connection logging</span></span>

<span data-ttu-id="3700c-970"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-970">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="3700c-971">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-971">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="3700c-972">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-972">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="3700c-973">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-973">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="3700c-974">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="3700c-974">Bind to a TCP socket</span></span>

<span data-ttu-id="3700c-975"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-975">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="3700c-976">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-976">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="3700c-977">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-977">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="3700c-978">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="3700c-978">Bind to a Unix socket</span></span>

<span data-ttu-id="3700c-979">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-979">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="3700c-980">Nginx 구성 파일에서 `server` > `location` > `proxy_pass` 항목을 `http://unix:/tmp/{KESTREL SOCKET}:/;`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-980">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="3700c-981">`{KESTREL SOCKET}`은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>에 제공된 소켓의 이름입니다(예: 이전 예제의 `kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="3700c-981">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="3700c-982">소켓이 Nginx에서 쓰기 가능한지 확인합니다(예: `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="3700c-982">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="3700c-983">포트 0</span><span class="sxs-lookup"><span data-stu-id="3700c-983">Port 0</span></span>

<span data-ttu-id="3700c-984">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-984">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="3700c-985">다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-985">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="3700c-986">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-986">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="3700c-987">제한 사항</span><span class="sxs-lookup"><span data-stu-id="3700c-987">Limitations</span></span>

<span data-ttu-id="3700c-988">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-988">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="3700c-989">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="3700c-989">`--urls` command-line argument</span></span>
* <span data-ttu-id="3700c-990">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="3700c-990">`urls` host configuration key</span></span>
* <span data-ttu-id="3700c-991">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="3700c-991">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="3700c-992">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-992">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="3700c-993">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-993">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="3700c-994">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="3700c-994">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="3700c-995">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-995">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="3700c-996">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="3700c-996">IIS endpoint configuration</span></span>

<span data-ttu-id="3700c-997">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-997">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="3700c-998">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-998">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="3700c-999">Libuv 전송 구성</span><span class="sxs-lookup"><span data-stu-id="3700c-999">Libuv transport configuration</span></span>

<span data-ttu-id="3700c-1000">ASP.NET Core 2.1 릴리스에서 Kestrel의 기본 전송은 더 이상 Libuv에 기반하지 않으며 대신 관리 소켓에 기반합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1000">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="3700c-1001"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출하고 다음 패키지 중 하나를 사용하는 2.1로 업그레이드된 ASP.NET Core 2.0 앱의 주요 변경 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1001">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="3700c-1002">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)(직접 패키지 참조)</span><span class="sxs-lookup"><span data-stu-id="3700c-1002">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="3700c-1003">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="3700c-1003">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="3700c-1004">Libuv를 사용해야 하는 프로젝트의 경우:</span><span class="sxs-lookup"><span data-stu-id="3700c-1004">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="3700c-1005">[Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 패키지에 대한 종속성을 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1005">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="3700c-1006"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1006">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="3700c-1007">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="3700c-1007">URL prefixes</span></span>

<span data-ttu-id="3700c-1008">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1008">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="3700c-1009">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1009">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="3700c-1010">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1010">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="3700c-1011">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="3700c-1011">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="3700c-1012">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1012">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="3700c-1013">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="3700c-1013">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="3700c-1014">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1014">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="3700c-1015">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="3700c-1015">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="3700c-1016">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1016">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="3700c-1017">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1017">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="3700c-1018">서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1018">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="3700c-1019">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1019">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="3700c-1020">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="3700c-1020">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="3700c-1021">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1021">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="3700c-1022">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1022">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="3700c-1023">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1023">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="3700c-1024">호스트 필터링</span><span class="sxs-lookup"><span data-stu-id="3700c-1024">Host filtering</span></span>

<span data-ttu-id="3700c-1025">Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1025">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="3700c-1026">호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1026">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="3700c-1027">명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1027">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="3700c-1028">`Host` 헤더의 유효성이 검사되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1028">`Host` headers aren't validated.</span></span>

<span data-ttu-id="3700c-1029">해결 방법으로 호스트 필터링 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1029">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="3700c-1030">호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공되고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 또는 2.2)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1030">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="3700c-1031">미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1031">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="3700c-1032">호스트 필터링 미들웨어는 기본적으로 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1032">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="3700c-1033">미들웨어를 활성화하려면 *appsettings.json* /*appsettings.\<EnvironmentName>.json* 에서 `AllowedHosts` 키를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1033">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="3700c-1034">값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1034">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="3700c-1035">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3700c-1035">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="3700c-1036">[전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1036">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="3700c-1037">전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1037">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="3700c-1038">전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1038">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="3700c-1039">호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1039">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="3700c-1040">전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-1040">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="3700c-1041">HTTP/1.1 요청 드레이닝</span><span class="sxs-lookup"><span data-stu-id="3700c-1041">HTTP/1.1 request draining</span></span>

<span data-ttu-id="3700c-1042">HTTP 연결을 여는 데 시간이 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1042">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="3700c-1043">HTTPS의 경우 리소스도 많이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1043">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="3700c-1044">따라서 Kestrel은 HTTP/1.1 프로토콜에 따라 연결을 다시 사용하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1044">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="3700c-1045">연결을 다시 사용하려면 요청 본문이 완전히 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1045">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="3700c-1046">서버가 리디렉션 또는 404 응답을 반환하는 경우 앱이 항상 `POST` 요청 같은 요청 본문을 사용하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1046">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="3700c-1047">`POST` 리디렉션 사례:</span><span class="sxs-lookup"><span data-stu-id="3700c-1047">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="3700c-1048">클라이언트가 이미 `POST` 데이터 일부를 전송했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1048">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="3700c-1049">서버가 301 응답을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1049">The server writes the 301 response.</span></span>
* <span data-ttu-id="3700c-1050">이전 요청 본문의 `POST` 데이터를 완전히 읽을 때까지 새 요청에 연결을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1050">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="3700c-1051">Kestrel은 요청 본문을 드레이닝하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1051">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="3700c-1052">요청 본문을 드레이닝하면 데이터가 처리되지 않고 읽고 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1052">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="3700c-1053">드레이닝 프로세스는 연결이 다시 사용되도록 허용하는 것과 남은 데이터를 드레이닝하는 데 걸리는 시간을 절충합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1053">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="3700c-1054">드레이닝의 시간 제한은 5초이며, 이는 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1054">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="3700c-1055">`Content-Length` 또는 `Transfer-Encoding` 헤더에 지정된 모든 데이터를 시간 제한 전에 읽지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1055">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="3700c-1056">때에 따라 응답을 쓰기 전이나 쓴 후에 즉시 요청을 종료하려고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1056">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="3700c-1057">예를 들어 클라이언트는 제한적인 데이터 한도를 포함할 수 있으므로 업로드된 데이터를 제한하는 것이 우선일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1057">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="3700c-1058">이 경우 요청을 종료하려면 컨트롤러, Razor 페이지 또는 미들웨어에서 [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1058">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="3700c-1059">`Abort` 호출에 관련된 주의 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1059">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="3700c-1060">새 연결을 만드는 것은 느리고 비용이 많이 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1060">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="3700c-1061">연결이 닫히기 전에 클라이언트가 응답을 읽었다는 보장은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1061">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="3700c-1062">`Abort` 호출은 드문 경우이며 일반적인 오류가 아닌 심각한 오류 사례용으로 예약되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1062">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="3700c-1063">특정 문제를 해결해야 하는 경우에만 `Abort`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1063">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="3700c-1064">예를 들어 악의적인 클라이언트가 데이터를 `POST`하려고 하거나 클라이언트 코드에 크거나 많은 요청을 발생시키는 버그가 있는 경우 `Abort`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1064">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="3700c-1065">HTTP 404(찾을 수 없음)와 같은 일반적인 오류 상황에서는 `Abort`를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-1065">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="3700c-1066">`Abort`를 호출하기 전에 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A)를 호출하면 서버가 응답 쓰기를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1066">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="3700c-1067">그러나 클라이언트 동작은 예측할 수 없으며 연결이 중단되기 전에 응답을 읽지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1067">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="3700c-1068">프로토콜이 연결을 닫지 않고 개별 요청 스트림을 중단하는 동작을 지원하기 때문에 HTTP/2의 경우 이 프로세스가 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1068">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="3700c-1069">5초 드레이닝 시간 제한이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1069">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="3700c-1070">응답을 완료한 후에 읽지 않은 요청 본문 데이터가 있으면 서버는 HTTP/2 RST 프레임을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1070">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="3700c-1071">추가 요청 본문 데이터 프레임은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1071">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="3700c-1072">가능하면 클라이언트가 [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 요청 헤더를 사용하고 서버가 요청 본문을 보내기 시작하기 전에 응답할 때까지 기다리는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1072">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="3700c-1073">이렇게 하면 클라이언트는 응답을 검사하고 불필요한 데이터를 보내기 전에 중단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1073">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3700c-1074">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3700c-1074">Additional resources</span></span>

* <span data-ttu-id="3700c-1075">Linux에서 UNIX 소켓을 사용하는 경우 앱을 종료할 때 소켓이 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3700c-1075">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="3700c-1076">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/aspnetcore/issues/14134)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3700c-1076">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="3700c-1077">RFC 7230: 메시지 구문 및 라우팅(섹션 5.4: 호스트)</span><span class="sxs-lookup"><span data-stu-id="3700c-1077">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
