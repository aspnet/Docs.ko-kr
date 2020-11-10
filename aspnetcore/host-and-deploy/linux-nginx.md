---
title: Nginx를 사용하여 Linux에서 ASP.NET Core 호스트
author: rick-anderson
description: Ubuntu 16.04에서 Nginx를 역방향 프록시로 설정하여 Kestrel에서 실행되는 ASP.NET Core 웹앱에 HTTP 트래픽을 전달하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 916bb1f761ce99b2296c84e1653e55fffa04f83c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057689"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="1713f-103">Nginx를 사용하여 Linux에서 ASP.NET Core 호스트</span><span class="sxs-lookup"><span data-stu-id="1713f-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="1713f-104">작성자: [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="1713f-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="1713f-105">이 가이드에서는 Ubuntu 16.04 Server에서 프로덕션 준비 ASP.NET Core 환경을 설정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="1713f-106">이 지침은 최신 버전의 Ubuntu에서 작동할 수 있지만 최신 버전에서 테스트되지는 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="1713f-107">ASP.NET Core에서 지원하는 다른 Linux 배포에 대한 자세한 내용은 [Linux에서 .NET Core의 필수 구성 요소](/dotnet/core/linux-prerequisites)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="1713f-108">Ubuntu 14.04의 경우 Kestrel 프로세스를 모니터링하기 위한 솔루션으로 *supervisord* 를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="1713f-109">*systemd* 는 Ubuntu 14.04에서 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="1713f-110">Ubuntu 14.04 지침의 경우 [이 항목의 이전 버전](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="1713f-111">이 가이드의 내용:</span><span class="sxs-lookup"><span data-stu-id="1713f-111">This guide:</span></span>

* <span data-ttu-id="1713f-112">기존 ASP.NET Core 앱을 역방향 프록시 서버 뒤에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="1713f-113">역방향 프록시 서버를 설정하여 Kestrel 웹 서버에 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="1713f-114">웹앱이 시작 시 디먼으로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="1713f-115">웹앱 다시 시작을 지원하도록 프로세스 관리 도구를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1713f-116">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="1713f-116">Prerequisites</span></span>

1. <span data-ttu-id="1713f-117">sudo 권한을 가진 표준 사용자 계정으로 Ubuntu 16.04 Server에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="1713f-118">서버에서 .NET Core 런타임을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="1713f-119">[.NET Core 다운로드 페이지](https://dotnet.microsoft.com/download/dotnet-core)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="1713f-120">미리 보기가 아닌 최신 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="1713f-121">**앱 실행 - 런타임** 에 있는 테이블에서 미리 보기가 아닌 최신 런타임을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="1713f-122">Linux **패키지 관리자 지침** 링크를 선택하고 Ubuntu 버전의 Ubuntu 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="1713f-123">기존 ASP.NET Core 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="1713f-124">공유 프레임워크를 업그레이드한 후 나중에 언제든지 서버에서 호스트되는 ASP.NET Core 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="1713f-125">앱 게시 및 복사</span><span class="sxs-lookup"><span data-stu-id="1713f-125">Publish and copy over the app</span></span>

<span data-ttu-id="1713f-126">[프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="1713f-127">앱을 로컬에서 실행하고 보안 연결(HTTPS)을 확인하도록 구성하지 않은 경우 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="1713f-128">보안 로컬 연결을 처리하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="1713f-129">자세한 내용은 [HTTPS 구성](#https-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="1713f-130">*Properties/launchSettings.json* 파일의 `applicationUrl` 속성에서 `https://localhost:5001`(있는 경우)을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="1713f-131">서버에서 실행할 수 있는 디렉터리(예: *bin/Release/&lt;target_framework_moniker&gt;/publish* )로 앱을 패키징하기 위해 개발 환경에서 [dotnet publish](/dotnet/core/tools/dotnet-publish)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish* ) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="1713f-132">.NET Core 런타임을 서버에서 유지 관리하지 않으려는 경우 앱은 [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)로 게시될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="1713f-133">조직의 워크플로에 통합된 도구(예: SCP, SFTP)를 사용하여 ASP.NET Core 앱을 서버에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="1713f-134">*var* 디렉터리(예: *var/www/helloapp* )에서 웹앱을 찾는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-134">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp* ).</span></span>

> [!NOTE]
> <span data-ttu-id="1713f-135">프로덕션 배포 시나리오에서 지속적인 통합 워크플로는 앱을 게시하고 자산을 서버로 복사하는 워크플로를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="1713f-136">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-136">Test the app:</span></span>

1. <span data-ttu-id="1713f-137">명령줄에서 `dotnet <app_assembly>.dll` 앱을 실행하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="1713f-138">브라우저에서 `http://<serveraddress>:<port>`로 이동하여 앱이 Linux에서 로컬로 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="1713f-139">역방향 프록시 서버 구성</span><span class="sxs-lookup"><span data-stu-id="1713f-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="1713f-140">역방향 프록시는 동적 웹앱을 지원하기 위한 일반적인 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="1713f-141">역방향 프록시는 HTTP 요청을 종료하고 이 요청을 ASP.NET Core 앱에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="1713f-142">역방향 프록시 서버를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-142">Use a reverse proxy server</span></span>

<span data-ttu-id="1713f-143">Kestrel은 ASP.NET Core에서 동적 콘텐츠를 제공하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="1713f-144">그러나 웹 지원 기능은 IIS, Apache 또는 Nginx와 같은 서버만큼 기능이 다양하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="1713f-145">역방향 프록시 서버는 정적 콘텐츠 지원, 요청 캐시, 요청 압축 및 HTTP 서버에서 HTTPS 종료 같은 작업을 오프로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="1713f-146">역방향 프록시 서버는 전용 컴퓨터에 있거나 HTTP 서버와 함께 배포될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="1713f-147">이 가이드에서는 Nginx의 단일 인스턴스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="1713f-148">이 인스턴스는 HTTP 서버와 함께 동일한 서버에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="1713f-149">요구 사항에 따라 다른 설정을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="1713f-150">요청이 역방향 프록시를 통해 전달되므로 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 패키지의 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="1713f-151">이 미들웨어는 `X-Forwarded-Proto` 헤더를 사용하여 `Request.Scheme`을 업데이트하므로 리디렉션 URI 및 기타 보안 정책이 제대로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>


[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="1713f-152">다른 미들웨어를 호출하기 전에 `Startup.Configure`의 맨 위에 있는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-152">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="1713f-153">`X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-153">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="1713f-154">미들웨어에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>가 지정되지 않은 경우 전달할 기본 헤더는 `None`입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-154">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="1713f-155">표준 localhost 주소(127.0.0.1)를 포함하여 루프백 주소(127.0.0.0/8, [::1])에서 실행 중인 프록시는 기본적으로 신뢰됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-155">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="1713f-156">조직 내의 다른 신뢰할 수 있는 프록시 또는 네트워크가 인터넷과 웹 서버 간의 요청을 처리하는 경우 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 사용하여 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> 또는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> 목록에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-156">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="1713f-157">다음 예제는 IP 주소 10.0.0.100의 신뢰할 수 있는 프록시 서버를 `Startup.ConfigureServices`의 전달된 헤더 미들웨어 `KnownProxies`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-157">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="1713f-158">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-158">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="1713f-159">Nginx 설치</span><span class="sxs-lookup"><span data-stu-id="1713f-159">Install Nginx</span></span>

<span data-ttu-id="1713f-160">`apt-get`을 사용하여 Nginx를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-160">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="1713f-161">설치 관리자는 시스템 시작 시 Nginx를 디먼으로 실행하는 *systemd* 시작 스크립트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-161">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="1713f-162">Ubuntu의 설치 지침은 [Nginx: 공식 Debian/Ubuntu 패키지](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-162">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="1713f-163">선택적 Nginx 모듈이 필요한 경우 소스에서 Nginx를 빌드해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-163">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="1713f-164">Nginx가 처음 설치되었으므로 다음을 실행하여 명시적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-164">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="1713f-165">브라우저에 Nginx에 대한 기본 방문 페이지가 표시되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-165">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="1713f-166">방문 페이지는 `http://<server_IP_address>/index.nginx-debian.html`에 도달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-166">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="1713f-167">Nginx 구성</span><span class="sxs-lookup"><span data-stu-id="1713f-167">Configure Nginx</span></span>

<span data-ttu-id="1713f-168">Nginx를 역방향 프록시로 구성하여 요청을 ASP.NET Core 앱에 프로그램에 전달하려면 */etc/nginx/sites-available/default* 를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-168">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default*.</span></span> <span data-ttu-id="1713f-169">텍스트 편집기에서 해당 항목을 열고 콘텐츠를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-169">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="1713f-170">앱이 :::no-loc(SignalR)::: 또는 :::no-loc(Blazor Server)::: 앱인 경우, 자세한 내용은 각각 <xref:signalr/scale#linux-with-nginx> 및 <xref:blazor/host-and-deploy/server#linux-with-nginx>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-170">If the app is a :::no-loc(SignalR)::: or :::no-loc(Blazor Server)::: app see <xref:signalr/scale#linux-with-nginx> and <xref:blazor/host-and-deploy/server#linux-with-nginx> respectively for more information.</span></span>

<span data-ttu-id="1713f-171">`server_name`이 일치하지 않으면 Nginx는 기본 서버를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-171">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="1713f-172">기본 서버가 정의되지 않은 경우 구성 파일의 첫 번째 서버는 기본 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-172">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="1713f-173">구성 파일에 있는 444 상태 코드를 반환하는 특정 기본 서버를 추가하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-173">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="1713f-174">기본 서버 구성 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-174">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="1713f-175">이전 구성 파일과 기본 서버를 사용하여 Nginx는 포트 80에서 호스트 헤더 `example.com` 또는 `*.example.com`가 포함된 공용 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-175">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="1713f-176">이러한 호스트와 일치하지 않는 요청은 Kestrel로 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-176">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="1713f-177">Nginx는 일치하는 요청을 `http://localhost:5000`의 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-177">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="1713f-178">자세한 내용은 [How nginx processes a request](https://nginx.org/docs/http/request_processing.html)(nginx가 요청을 처리하는 방법)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-178">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="1713f-179">Kestrel의 IP/포트를 변경하려면 [Kestrel: 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-179">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="1713f-180">적절한 [server_name 지시문](https://nginx.org/docs/http/server_names.html)을 지정하지 않으면 앱이 보안 취약성에 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-180">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="1713f-181">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.example.com`)에는 이러한 보안 위험이 발생하지 않습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="1713f-181">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="1713f-182">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-182">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="1713f-183">Nginx 구성이 설정되면 `sudo nginx -t`를 실행하여 구성 파일의 구문을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-183">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="1713f-184">구성 파일 테스트에 성공하면 `sudo nginx -s reload`를 실행하여 Nginx가 변경 내용을 선택하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-184">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="1713f-185">앱을 서버에서 직접 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="1713f-185">To directly run the app on the server:</span></span>

1. <span data-ttu-id="1713f-186">앱의 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-186">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="1713f-187">`dotnet <app_assembly.dll>` 앱을 실행합니다. 여기서 `app_assembly.dll`은 앱의 어셈블리 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-187">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="1713f-188">앱이 서버에서 실행되지만 인터넷을 통해 응답하지 않는 경우 서버의 방화벽을 확인하고 포트 80이 열려 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-188">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="1713f-189">Ubuntu Azure VM을 사용하는 경우 인바운드 포트 80 트래픽을 사용하는 NSG(네트워크 보안 그룹) 규칙을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-189">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="1713f-190">인바운드 규칙을 사용할 때 아웃바운드 트래픽이 자동으로 부여되므로 아웃바운드 포트 80 규칙을 사용하도록 설정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-190">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="1713f-191">앱 테스트를 완료한 후에 명령 프롬프트에서 `Ctrl+C`를 사용하여 앱을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-191">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="1713f-192">앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="1713f-192">Monitor the app</span></span>

<span data-ttu-id="1713f-193">서버는 `http://<serveraddress>:80`에 대해 실행된 요청을 `http://127.0.0.1:5000`의 Kestrel에서 실행되는 ASP.NET Core 앱에 전달하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-193">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="1713f-194">그러나 Nginx는 Kestrel 프로세스를 관리하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-194">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="1713f-195">*systemd* 를 사용하여 기본 웹앱을 시작 및 모니터링하기 위한 서비스 파일을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-195">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="1713f-196">*systemd* 는 프로세스를 시작, 중지 및 관리하기 위한 다양하고 강력한 기능을 제공하는 init 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-196">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="1713f-197">서비스 파일 만들기</span><span class="sxs-lookup"><span data-stu-id="1713f-197">Create the service file</span></span>

<span data-ttu-id="1713f-198">서비스 정의 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-198">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="1713f-199">앱에 대한 예제 서비스 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-199">The following is an example service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="1713f-200">앞의 예제에서 서비스를 관리하는 사용자는 `User` 옵션으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-200">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="1713f-201">사용자(`www-data`)가 존재해야 하며 앱 파일에 대한 적절한 소유권이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-201">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="1713f-202">`TimeoutStopSec`를 사용하여 초기 인터럽트 신호를 받은 후 앱이 종료되기를 기다리는 기간을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-202">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="1713f-203">이 기간 내에 앱이 종료되지 않으면 앱을 종료하기 위해 SIGKILL이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-203">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="1713f-204">단위 없는 초로 된 값(예: `150`) 또는 시간 범위 값(예: `2min 30s`)으로 값을 입력하거나, 시간 제한을 사용하지 않으려면 `infinity`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-204">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="1713f-205">`TimeoutStopSec`의 기본값은 관리자 구성 파일( *systemd-system.conf* , *system.conf.d* , *systemd-user.conf* , *user.conf.d* )의 `DefaultTimeoutStopSec` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-205">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file ( *systemd-system.conf* , *system.conf.d* , *systemd-user.conf* , *user.conf.d* ).</span></span> <span data-ttu-id="1713f-206">대부분의 배포에서 기본 시간 제한은 90초입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-206">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="1713f-207">Linux에는 대/소문자를 구분하는 파일 시스템이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-207">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="1713f-208">ASPNETCORE_ENVIRONMENT를 “프로덕션”으로 설정하면 *appsettings.production.json* 대신 구성 파일 *appsettings.Production.json* 을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-208">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json* , not *appsettings.production.json*.</span></span>

<span data-ttu-id="1713f-209">일부 값(예: SQL 연결 문자열)은 환경 변수를 읽기 위해 구성 공급자에 대해 이스케이프되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-209">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="1713f-210">다음 명령을 사용하여 구성 파일에서 사용할 제대로 이스케이프된 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-210">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1713f-211">콜론(`:`) 구분 기호는 환경 변수 이름에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-211">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="1713f-212">콜론 대신 이중 밑줄(`__`)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-212">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="1713f-213">[환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables)는 환경 변수를 구성으로 읽을 때 이중 밑줄을 콜론으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-213">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="1713f-214">다음 예제에서 연결 문자열 키 `ConnectionStrings:DefaultConnection`은 서비스 정의 파일에 `ConnectionStrings__DefaultConnection`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-214">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1713f-215">콜론(`:`) 구분 기호는 환경 변수 이름에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-215">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="1713f-216">콜론 대신 이중 밑줄(`__`)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-216">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="1713f-217">[환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)는 환경 변수를 구성으로 읽을 때 이중 밑줄을 콜론으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-217">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="1713f-218">다음 예제에서 연결 문자열 키 `ConnectionStrings:DefaultConnection`은 서비스 정의 파일에 `ConnectionStrings__DefaultConnection`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-218">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="1713f-219">파일을 저장하고 서비스를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-219">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="1713f-220">서비스를 시작하고 실행 중인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-220">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="1713f-221">역방향 프록시를 구성하고 systemd를 통해 Kestrel을 관리하면 웹앱이 완전히 구성되고 로컬 컴퓨터(`http://localhost`)의 브라우저에서 웹앱에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-221">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="1713f-222">차단 중인 방화벽이 없다면 원격 컴퓨터에서 액세스할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-222">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="1713f-223">응답 헤더를 검사하는 `Server` 헤더는 Kestrel에서 지원하는 ASP.NET Core 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-223">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="1713f-224">로그 보기</span><span class="sxs-lookup"><span data-stu-id="1713f-224">View logs</span></span>

<span data-ttu-id="1713f-225">Kestrel을 사용하는 웹앱은 `systemd`를 사용하여 관리되므로 모든 이벤트 및 프로세스가 중앙형 저널에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-225">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="1713f-226">그러나 이 저널에는 `systemd`를 통해 관리하는 모든 서비스 및 프로세스에 대한 모든 항목이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-226">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="1713f-227">`kestrel-helloapp.service` 관련 항목을 보려면 다음 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-227">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="1713f-228">추가 필터링을 위해 `--since today`, `--until 1 hour ago` 같은 시간 옵션이나 이러한 옵션의 조합을 사용하여 반환되는 항목 수를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-228">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="1713f-229">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="1713f-229">Data protection</span></span>

<span data-ttu-id="1713f-230">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증 미들웨어(예: :::no-loc(cookie)::: 미들웨어) 및 CSRF(교차 사이트 요청 위조) 보호를 비롯한 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-230">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, :::no-loc(cookie)::: middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="1713f-231">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-231">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="1713f-232">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-232">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="1713f-233">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-233">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="1713f-234">모든 :::no-loc(cookie)::: 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-234">All :::no-loc(cookie):::-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="1713f-235">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-235">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="1713f-236">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-236">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="1713f-237">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData :::no-loc(cookie):::](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-237">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData :::no-loc(cookie):::s](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="1713f-238">키 링을 유지하고 암호화하도록 데이터 보호를 구성하려면 다음을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="1713f-238">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="1713f-239">긴 요청 헤더 필드</span><span class="sxs-lookup"><span data-stu-id="1713f-239">Long request header fields</span></span>

<span data-ttu-id="1713f-240">프록시 서버 기본 설정은 일반적으로 플랫폼에 따라 요청 헤더 필드를 4K 또는 8K로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-240">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="1713f-241">앱에 기본값보다 긴 필드가 필요할 수 있습니다(예: [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)를 사용하는 앱).</span><span class="sxs-lookup"><span data-stu-id="1713f-241">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="1713f-242">더 긴 필드가 필요한 경우 프록시 서버의 기본 설정을 조정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-242">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="1713f-243">적용할 값은 시나리오에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-243">The values to apply depend on the scenario.</span></span> <span data-ttu-id="1713f-244">자세한 내용은 서버의 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-244">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="1713f-245">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="1713f-245">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="1713f-246">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="1713f-246">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="1713f-247">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="1713f-247">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="1713f-248">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="1713f-248">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="1713f-249">필요한 경우가 아니면 프록시 버퍼의 기본값을 늘리지 마세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-249">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="1713f-250">이러한 값을 늘리면 악의적인 사용자의 버퍼 오버런(오버플로) 및 DoS(서비스 거부) 공격의 위험이 증가됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-250">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="1713f-251">앱 보안 유지</span><span class="sxs-lookup"><span data-stu-id="1713f-251">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="1713f-252">AppArmor 사용</span><span class="sxs-lookup"><span data-stu-id="1713f-252">Enable AppArmor</span></span>

<span data-ttu-id="1713f-253">LSM(Linux Security Modules)은 Linux 2.6 이후 Linux 커널에 포함된 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-253">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="1713f-254">LSM은 보안 모듈의 다양한 구현을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-254">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="1713f-255">[AppArmor](https://wiki.ubuntu.com/AppArmor)는 프로그램을 제한된 리소스 집합으로 한정할 수 있는 필수 Access Control 시스템을 구현하는 LSM입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="1713f-256">AppArmor가 사용하도록 설정되고 제대로 구성되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-256">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="1713f-257">방화벽 구성</span><span class="sxs-lookup"><span data-stu-id="1713f-257">Configure the firewall</span></span>

<span data-ttu-id="1713f-258">사용되지 않는 모든 외부 포트를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-258">Close off all external ports that are not in use.</span></span> <span data-ttu-id="1713f-259">복잡하지 않은 방화벽(ufw)은 방화벽을 구성하기 위한 CLI를 제공하여 `iptables`에 대한 프런트 엔드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-259">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="1713f-260">방화벽이 올바르게 구성되지 않으면 전체 시스템에 대한 액세스가 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-260">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="1713f-261">올바른 SSH 포트를 지정하지 못하면 SSH를 사용하여 시스템에 연결하는 경우 실직적으로 시스템에 액세스할 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-261">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="1713f-262">기본 포트는 22입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-262">The default port is 22.</span></span> <span data-ttu-id="1713f-263">자세한 내용은 [ufw 소개](https://help.ubuntu.com/community/UFW) 및 [매뉴얼](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-263">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="1713f-264">`ufw`를 설치하고 필요한 모든 포트에서 트래픽을 허용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-264">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="1713f-265">Nginx 보안 유지</span><span class="sxs-lookup"><span data-stu-id="1713f-265">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="1713f-266">Nginx 응답 이름 변경</span><span class="sxs-lookup"><span data-stu-id="1713f-266">Change the Nginx response name</span></span>

<span data-ttu-id="1713f-267">*src/http/ngx_http_header_filter_module.c* 를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-267">Edit *src/http/ngx_http_header_filter_module.c* :</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="1713f-268">옵션 구성</span><span class="sxs-lookup"><span data-stu-id="1713f-268">Configure options</span></span>

<span data-ttu-id="1713f-269">추가 필수 모듈을 사용하여 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-269">Configure the server with additional required modules.</span></span> <span data-ttu-id="1713f-270">[ModSecurity](https://www.modsecurity.org/)와 같은 웹앱 방화벽을 사용하여 앱을 강화해 보세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-270">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="1713f-271">HTTPS 구성</span><span class="sxs-lookup"><span data-stu-id="1713f-271">HTTPS configuration</span></span>

<span data-ttu-id="1713f-272">**보안(HTTPS) 로컬 연결을 위해 앱 구성**</span><span class="sxs-lookup"><span data-stu-id="1713f-272">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="1713f-273">[dotnet 실행](/dotnet/core/tools/dotnet-run) 명령은 `applicationUrl` 속성(예: `https://localhost:5001;http://localhost:5000`)이 제공하는 URL에서 수신 대기하도록 앱을 구성하는 앱의 *Properties/launchSettings.json* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-273">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="1713f-274">다음 방법 중 하나를 사용하여 `dotnet run` 명령 또는 개발 환경(Visual Studio Code의 F5 또는 Ctrl+F5)에 대해 개발 중인 인증서를 사용하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-274">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="1713f-275">[구성에서 기본 인증서를 바꿈](xref:fundamentals/servers/kestrel#configuration)( *권장* )</span><span class="sxs-lookup"><span data-stu-id="1713f-275">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) ( *Recommended* )</span></span>
* [<span data-ttu-id="1713f-276">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="1713f-276">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="1713f-277">**보안 (HTTPS) 클라이언트 연결을 위해 역방향 프록시 구성**</span><span class="sxs-lookup"><span data-stu-id="1713f-277">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="1713f-278">신뢰할 수 있는 CA(인증 기관)에서 발급된 유효한 인증서를 지정하여 포트 `443`에서 HTTPS 트래픽을 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-278">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="1713f-279">다음 */etc/nginx/nginx.conf* 파일에 설명된 일부 사례를 채택하여 보안을 강화합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-279">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="1713f-280">예를 들어 더 강력한 암호화를 선택하고 HTTP를 사용한 모든 트래픽을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-280">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="1713f-281">HSTS(`HTTP Strict-Transport-Security`) 헤더를 추가하면 클라이언트에서 만든 모든 후속 요청이 HTTPS를 통해 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-281">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="1713f-282">추후 HTTPS가 사용하지 않도록 설정될 경우 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-282">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="1713f-283">HSTS 헤더를 추가하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-283">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="1713f-284">짧은 `max-age` 값을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-284">Choose a short `max-age` value.</span></span>

<span data-ttu-id="1713f-285">*/etc/nginx/proxy.conf* 구성 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-285">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="1713f-286">*/etc/nginx/nginx.conf* 구성 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-286">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="1713f-287">예제에서는 `http` 및 `server` 섹션이 하나의 구성 파일에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-287">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> <span data-ttu-id="1713f-288">:::no-loc(Blazor WebAssembly)::: 앱에서 수행하는 많은 수의 요청을 수용하려면 앱에 더 큰 `burst` 매개 변수 값이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-288">:::no-loc(Blazor WebAssembly)::: apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="1713f-289">자세한 내용은 <xref:blazor/host-and-deploy/webassembly#nginx>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1713f-289">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="1713f-290">클릭재킹(clickjacking)으로부터 Nginx 보호</span><span class="sxs-lookup"><span data-stu-id="1713f-290">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="1713f-291">또한 ‘UI 교정 공격’이라고도 하는[클릭재킹(Clickjacking)](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)은 웹 사이트 방문자를 속여서 현재 방문 중인 것과 다른 페이지에서 링크 또는 단추를 클릭하게 하는 악의적인 공격입니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-291">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack* , is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="1713f-292">`X-FRAME-OPTIONS`를 사용하여 사이트를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-292">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="1713f-293">클릭재킹 공격을 완화하려면:</span><span class="sxs-lookup"><span data-stu-id="1713f-293">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="1713f-294">*nginx.conf* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-294">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="1713f-295">`add_header X-Frame-Options "SAMEORIGIN";` 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-295">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="1713f-296">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-296">Save the file.</span></span>
1. <span data-ttu-id="1713f-297">Nginx를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-297">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="1713f-298">MIME 형식 검색</span><span class="sxs-lookup"><span data-stu-id="1713f-298">MIME-type sniffing</span></span>

<span data-ttu-id="1713f-299">이 헤더는 응답 콘텐츠 형식을 재정의하지 않도록 브라우저에 지시하므로 대부분의 브라우저에서 선언된 콘텐츠 형식이 아닌 응답에 대한 MIME 검색을 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-299">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="1713f-300">`nosniff` 옵션을 사용하면 서버에 콘텐츠가 “text/html”이라고 표시될 경우 브라우저는 콘텐츠를 “text/html”으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-300">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="1713f-301">*nginx.conf* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-301">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="1713f-302">줄 `add_header X-Content-Type-Options "nosniff";`를 추가하고 파일을 저장한 다음 Nginx를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-302">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="1713f-303">추가 Nginx 제안</span><span class="sxs-lookup"><span data-stu-id="1713f-303">Additional Nginx suggestions</span></span>

<span data-ttu-id="1713f-304">서버에서 공유 프레임워크를 업그레이드한 후 서버에서 호스트되는 ASP.NET Core 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="1713f-304">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1713f-305">추가 자료</span><span class="sxs-lookup"><span data-stu-id="1713f-305">Additional resources</span></span>

* [<span data-ttu-id="1713f-306">Linux에서 .NET Core의 필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="1713f-306">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="1713f-307">Nginx: 이진 릴리스: 공식 Debian/Ubuntu 패키지</span><span class="sxs-lookup"><span data-stu-id="1713f-307">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="1713f-308">NGINX: 전달된 헤더 사용</span><span class="sxs-lookup"><span data-stu-id="1713f-308">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
