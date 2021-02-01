---
title: ASP.NET Core의 웹 서버 구현
author: rick-anderson
description: ASP.NET Core의 웹 서버 Kestrel 및 HTTP.sys를 알아봅니다. 서버를 선택하는 방법 및 역방향 프록시 서버를 사용하는 시기에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 2acddd212639ac0a82b3c46f2225ff66d0999dd0
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217559"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="28f5b-104">ASP.NET Core의 웹 서버 구현</span><span class="sxs-lookup"><span data-stu-id="28f5b-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="28f5b-105">작성자: [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) 및 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="28f5b-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="28f5b-106">ASP.NET Core 앱은 In-Process HTTP 서버 구현을 사용하여 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="28f5b-107">서버 구현은 HTTP 요청을 수신하고 <xref:Microsoft.AspNetCore.Http.HttpContext>에 구성된 일련의 [요청 기능](xref:fundamentals/request-features)으로 앱에 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="28f5b-108">Windows</span><span class="sxs-lookup"><span data-stu-id="28f5b-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="28f5b-109">ASP.NET Core는 다음과 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="28f5b-110">[Kestrel 서버](xref:fundamentals/servers/kestrel)는 기본 플랫폼 간 HTTP 서버 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="28f5b-111">Kestrel은 최상의 성능과 메모리 사용률을 제공하지만 HTTP.sys의 고급 기능 중 일부를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-111">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="28f5b-112">자세한 내용은 이 문서의 [Kestrel 및 HTTP.sys](#korh)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28f5b-112">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>
* <span data-ttu-id="28f5b-113">IIS HTTP 서버는 IIS의 [In-Process 서버](#hosting-models)입니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-113">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="28f5b-114">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)는 [Http.Sys 커널 드라이버 및 HTTP Server API](/windows/desktop/Http/http-api-start-page)를 기반으로 하는 Windows 전용 HTTP 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="28f5b-115">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 사용하는 경우 앱은 다음 중 하나에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-115">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="28f5b-116">IIS 작업자 프로세스와 동일한 프로세스에서 IIS HTTP 서버를 사용하여 실행됩니다([In-Process 호스팅 모델](#hosting-models)).</span><span class="sxs-lookup"><span data-stu-id="28f5b-116">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="28f5b-117">*In-Process* 가 권장되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-117">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="28f5b-118">IIS 작업자 프로세스와 다른 별도의 프로세스에서 [Kestrel 서버](#kestrel)를 사용하여 실행됩니다([Out-of-Process 호스팅 모델](#hosting-models)).</span><span class="sxs-lookup"><span data-stu-id="28f5b-118">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="28f5b-119">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 IIS와 In-Process IIS HTTP 서버 또는 Kestrel 간의 네이티브 IIS 요청을 처리하는 네이티브 IIS 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-119">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="28f5b-120">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28f5b-120">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<a name="korh"></a>

## <a name="kestrel-vs-httpsys"></a><span data-ttu-id="28f5b-121">Kestrel 및 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="28f5b-121">Kestrel vs. HTTP.sys</span></span>

<span data-ttu-id="28f5b-122">Kestrel은 HTTP.sys에 비해 다음과 같은 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-122">Kestrel has the following advantages over HTTP.sys:</span></span>

  * <span data-ttu-id="28f5b-123">향상된 성능 및 메모리 사용률.</span><span class="sxs-lookup"><span data-stu-id="28f5b-123">Better performance and memory utilization.</span></span>
  * <span data-ttu-id="28f5b-124">플랫폼 간</span><span class="sxs-lookup"><span data-stu-id="28f5b-124">Cross platform</span></span>
  * <span data-ttu-id="28f5b-125">민첩성 - OS와 독립적으로 개발되고 패치됨.</span><span class="sxs-lookup"><span data-stu-id="28f5b-125">Agility, it's developed and patched independent of the OS.</span></span>
  * <span data-ttu-id="28f5b-126">프로그래밍 방식 포트 및 TLS 구성</span><span class="sxs-lookup"><span data-stu-id="28f5b-126">Programmatic port and TLS configuration</span></span>
  * <span data-ttu-id="28f5b-127">[PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs)와 같은 프로토콜 및 대체 전송을 허용하는 확장성.</span><span class="sxs-lookup"><span data-stu-id="28f5b-127">Extensibility that allows for protocols like [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) and alternate transports.</span></span>

<span data-ttu-id="28f5b-128">Http.Sys는 kestrel에서 제공하지 않는 다음과 같은 기능을 사용하여 공유 커널 모드 구성 요소로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-128">Http.Sys operates as a shared kernel mode component with the following features that kestrel does not have:</span></span>

  * <span data-ttu-id="28f5b-129">포트 공유</span><span class="sxs-lookup"><span data-stu-id="28f5b-129">Port sharing</span></span>
  * <span data-ttu-id="28f5b-130">커널 모드 Windows 인증.</span><span class="sxs-lookup"><span data-stu-id="28f5b-130">Kernel mode windows authentication.</span></span> <span data-ttu-id="28f5b-131">[Kestrel은 사용자 모드 인증만 지원](xref:security/authentication/windowsauth#kestrel)합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-131">[Kestrel supports only user-mode authentication](xref:security/authentication/windowsauth#kestrel).</span></span>
  * <span data-ttu-id="28f5b-132">큐 전송을 통한 빠른 프록시 처리</span><span class="sxs-lookup"><span data-stu-id="28f5b-132">Fast proxying via queue transfers</span></span>
  * <span data-ttu-id="28f5b-133">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="28f5b-133">Direct file transmission</span></span>
  * <span data-ttu-id="28f5b-134">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="28f5b-134">Response caching</span></span>

## <a name="hosting-models"></a><span data-ttu-id="28f5b-135">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="28f5b-135">Hosting models</span></span>

<span data-ttu-id="28f5b-136">In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-136">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="28f5b-137">요청이 나가는 네트워크 트래픽을 동일한 머신에 다시 반환하는 네트워크 인터페이스인 루프백 어댑터를 통해 프록시되지 않기 때문에 In-Process 호스팅이 Out-of-Process 호스팅보다 향상된 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-137">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="28f5b-138">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-138">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="28f5b-139">Out-of-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되고, 모듈이 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-139">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="28f5b-140">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-140">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="28f5b-141">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-141">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="28f5b-142">자세한 내용 및 구성 지침은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28f5b-142">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="28f5b-143">macOS</span><span class="sxs-lookup"><span data-stu-id="28f5b-143">macOS</span></span>](#tab/macos)

<span data-ttu-id="28f5b-144">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-144">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="28f5b-145">Linux</span><span class="sxs-lookup"><span data-stu-id="28f5b-145">Linux</span></span>](#tab/linux)

<span data-ttu-id="28f5b-146">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-146">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="28f5b-147">Kestrel</span><span class="sxs-lookup"><span data-stu-id="28f5b-147">Kestrel</span></span>

 <span data-ttu-id="28f5b-148">[Kestrel 서버](xref:fundamentals/servers/kestrel)는 기본 플랫폼 간 HTTP 서버 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-148">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="28f5b-149">Kestrel은 최상의 성능과 메모리 사용률을 제공하지만 HTTP.sys의 고급 기능 중 일부를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-149">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="28f5b-150">자세한 내용은 이 문서의 [Kestrel 및 HTTP.sys](#korh)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28f5b-150">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>

<span data-ttu-id="28f5b-151">다음과 같이 Kestrel을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-151">Use Kestrel:</span></span>

* <span data-ttu-id="28f5b-152">인터넷을 포함한 네트워크의 요청을 직접 처리하는 에지 서버로 단독 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-152">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="28f5b-154">[IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/) 같은 *역방향 프록시 서버* 와 함께 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-154">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="28f5b-155">역방향 프록시 서버는 인터넷에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-155">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="28f5b-157">&mdash;역방향 프록시 서버가 있는 구성과 없는 구성 모두&mdash; 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-157">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="28f5b-158">Kestrel을 역방향 프록시 구성에서 사용하는 경우에 대한 Kestrel 구성 지침 및 정보는 <xref:fundamentals/servers/kestrel>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28f5b-158">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="28f5b-159">Windows</span><span class="sxs-lookup"><span data-stu-id="28f5b-159">Windows</span></span>](#tab/windows)

<span data-ttu-id="28f5b-160">ASP.NET Core는 다음과 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-160">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="28f5b-161">[Kestrel](xref:fundamentals/servers/kestrel) 서버는 기본 플랫폼 간 HTTP 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-161">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="28f5b-162">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)는 [Http.Sys 커널 드라이버 및 HTTP Server API](/windows/desktop/Http/http-api-start-page)를 기반으로 하는 Windows 전용 HTTP 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-162">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="28f5b-163">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 사용하면 앱이 [Kestrel 서버](#kestrel)를 사용하여 IIS 작업자 프로세스와 다른 별도의 프로세스에서(*Out-of-Process*) 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-163">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="28f5b-164">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 이 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-164">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="28f5b-165">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-165">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="28f5b-166">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-166">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="28f5b-167">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-167">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 모듈](_static/ancm-outofprocess.png)

<span data-ttu-id="28f5b-169">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-169">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="28f5b-170">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-170">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="28f5b-171">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-171">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="28f5b-172">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)는 `http://localhost:{port}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-172">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="28f5b-173">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-173">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="28f5b-174">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-174">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="28f5b-175">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-175">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="28f5b-176">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-176">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="28f5b-177">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-177">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="28f5b-178">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-178">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="28f5b-179">IIS 및 ASP.NET Core 모듈 구성 지침은 다음 토픽을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28f5b-179">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="28f5b-180">macOS</span><span class="sxs-lookup"><span data-stu-id="28f5b-180">macOS</span></span>](#tab/macos)

<span data-ttu-id="28f5b-181">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-181">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="28f5b-182">Linux</span><span class="sxs-lookup"><span data-stu-id="28f5b-182">Linux</span></span>](#tab/linux)

<span data-ttu-id="28f5b-183">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-183">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="28f5b-184">Nginx 및 Kestrel</span><span class="sxs-lookup"><span data-stu-id="28f5b-184">Nginx with Kestrel</span></span>

<span data-ttu-id="28f5b-185">Linux에서 Kestrel에 대한 역방향 프록시 서버로 Nginx를 사용하는 방법은 <xref:host-and-deploy/linux-nginx>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28f5b-185">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="28f5b-186">Apache 및 Kestrel</span><span class="sxs-lookup"><span data-stu-id="28f5b-186">Apache with Kestrel</span></span>

<span data-ttu-id="28f5b-187">Linux에서 Kestrel에 대한 역방향 프록시 서버로 Apache를 사용하는 방법은 <xref:host-and-deploy/linux-apache>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28f5b-187">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="28f5b-188">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="28f5b-188">HTTP.sys</span></span>

<span data-ttu-id="28f5b-189">Windows에서 ASP.NET Core 앱을 실행할 경우 Kestrel 대신 HTTP.sys를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-189">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="28f5b-190">Kestrel에서 제공하지 않는 기능이 앱에 필요하지 않는 한 HTTP.sys보다 Kestrel을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-190">Kestrel is recommended over HTTP.sys unless the app requires features not available in Kestrel.</span></span> <span data-ttu-id="28f5b-191">자세한 내용은 <xref:fundamentals/servers/httpsys>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28f5b-191">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="28f5b-193">HTTP.sys는 내부 네트워크에만 노출되는 앱에도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-193">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="28f5b-195">HTTP.sys 구성 지침은 <xref:fundamentals/servers/httpsys>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28f5b-195">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="28f5b-196">ASP.NET Core 서버 인프라</span><span class="sxs-lookup"><span data-stu-id="28f5b-196">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="28f5b-197">`Startup.Configure` 메서드에서 사용 가능한 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>는 <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> 형식의 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-197">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="28f5b-198">Kestrel 및 HTTP.sys는 각각 단일 기능인 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>만을 노출하지만 다른 서버 구현은 추가 기능을 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-198">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="28f5b-199">`IServerAddressesFeature`를 사용하여 런타임 시 서버 구현이 바인딩된 포트를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-199">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="28f5b-200">사용자 지정 서버</span><span class="sxs-lookup"><span data-stu-id="28f5b-200">Custom servers</span></span>

<span data-ttu-id="28f5b-201">기본 제공 서버가 앱의 요구 사항을 충족하지 않으면 사용자 지정 서버 구현을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-201">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="28f5b-202">[OWIN(Open Web Interface for .NET) 가이드](xref:fundamentals/owin)에서는 [Nowin](https://github.com/Bobris/Nowin) 기반 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 구현을 작성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-202">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="28f5b-203">앱이 사용하는 기능 인터페이스에만 구현이 필요하지만, 최소한 <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> 및 <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>는 지원되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-203">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="28f5b-204">서버 시작</span><span class="sxs-lookup"><span data-stu-id="28f5b-204">Server startup</span></span>

<span data-ttu-id="28f5b-205">IDE(통합 개발 환경)이나 편집기가 앱을 시작하는 경우 서버가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-205">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="28f5b-206">[Visual Studio](https://visualstudio.microsoft.com): 실행 프로필을 사용하여 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 또는 콘솔에서 앱 및 서버를 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-206">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="28f5b-207">[Visual Studio Code](https://code.visualstudio.com/): [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode)로 앱 및 서버를 시작합니다. 그러면 CoreCLR 디버거를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-207">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="28f5b-208">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/): 앱과 서버는 [Mono Soft-Mode 디버거](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)에 의해 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-208">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="28f5b-209">프로젝트의 폴더에 있는 명령 프롬프트에서 앱을 시작할 때 [dotnet run](/dotnet/core/tools/dotnet-run)은 서버 및 앱을 시작합니다(Kestrel 및 HTTP.sys만 해당).</span><span class="sxs-lookup"><span data-stu-id="28f5b-209">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="28f5b-210">`Debug`(기본값) 또는 `Release`로 설정되어 있는 `-c|--configuration` 옵션으로 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-210">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="28f5b-211">*launchSettings.json* 파일은 `dotnet run`을 사용하거나 Visual Studio와 같은 도구에 기본 제공되는 디버거를 사용하여 앱을 시작할 때 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-211">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="28f5b-212">시작 프로필이 *launchSettings.json* 파일에 있는 경우 `dotnet run` 명령에 `--launch-profile {PROFILE NAME}` 옵션을 사용하거나 Visual Studio에서 프로필을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-212">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="28f5b-213">자세한 내용은 [dotnet run](/dotnet/core/tools/dotnet-run) 및 [.NET Core 배포 패키징](/dotnet/core/build/distribution-packaging)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28f5b-213">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="28f5b-214">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="28f5b-214">HTTP/2 support</span></span>

<span data-ttu-id="28f5b-215">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 배포 시나리오에서 ASP.NET Core에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-215">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="28f5b-216">Kestrel</span><span class="sxs-lookup"><span data-stu-id="28f5b-216">Kestrel</span></span>](xref:fundamentals/servers/kestrel/http2)
  * <span data-ttu-id="28f5b-217">운영 체제</span><span class="sxs-lookup"><span data-stu-id="28f5b-217">Operating system</span></span>
    * <span data-ttu-id="28f5b-218">Windows Server 2016/Windows 10 이상&dagger;</span><span class="sxs-lookup"><span data-stu-id="28f5b-218">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="28f5b-219">Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)</span><span class="sxs-lookup"><span data-stu-id="28f5b-219">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="28f5b-220">macOS에서는 이후 릴리스에서 HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-220">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="28f5b-221">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="28f5b-221">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="28f5b-222">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="28f5b-222">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="28f5b-223">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="28f5b-223">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="28f5b-224">대상 프레임워크: HTTP.sys 배포에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-224">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="28f5b-225">IIS(In-Process)</span><span class="sxs-lookup"><span data-stu-id="28f5b-225">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="28f5b-226">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="28f5b-226">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="28f5b-227">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="28f5b-227">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="28f5b-228">IIS(Out-of-Process)</span><span class="sxs-lookup"><span data-stu-id="28f5b-228">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="28f5b-229">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="28f5b-229">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="28f5b-230">공개 에지 서버 연결은 HTTP/2를 사용하지만 Kestrel에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-230">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="28f5b-231">대상 프레임워크: IIS Out-of-Process 배포에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-231">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="28f5b-232">&dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-232">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="28f5b-233">이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-233">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="28f5b-234">TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-234">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [<span data-ttu-id="28f5b-235">Kestrel</span><span class="sxs-lookup"><span data-stu-id="28f5b-235">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="28f5b-236">운영 체제</span><span class="sxs-lookup"><span data-stu-id="28f5b-236">Operating system</span></span>
    * <span data-ttu-id="28f5b-237">Windows Server 2016/Windows 10 이상&dagger;</span><span class="sxs-lookup"><span data-stu-id="28f5b-237">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="28f5b-238">Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)</span><span class="sxs-lookup"><span data-stu-id="28f5b-238">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="28f5b-239">macOS에서는 이후 릴리스에서 HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-239">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="28f5b-240">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="28f5b-240">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="28f5b-241">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="28f5b-241">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="28f5b-242">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="28f5b-242">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="28f5b-243">대상 프레임워크: HTTP.sys 배포에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-243">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="28f5b-244">IIS(In-Process)</span><span class="sxs-lookup"><span data-stu-id="28f5b-244">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="28f5b-245">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="28f5b-245">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="28f5b-246">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="28f5b-246">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="28f5b-247">IIS(Out-of-Process)</span><span class="sxs-lookup"><span data-stu-id="28f5b-247">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="28f5b-248">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="28f5b-248">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="28f5b-249">공개 에지 서버 연결은 HTTP/2를 사용하지만 Kestrel에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-249">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="28f5b-250">대상 프레임워크: IIS Out-of-Process 배포에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-250">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="28f5b-251">&dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-251">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="28f5b-252">이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-252">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="28f5b-253">TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-253">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="28f5b-254">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="28f5b-254">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="28f5b-255">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="28f5b-255">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="28f5b-256">대상 프레임워크: HTTP.sys 배포에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-256">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="28f5b-257">IIS(Out-of-Process)</span><span class="sxs-lookup"><span data-stu-id="28f5b-257">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="28f5b-258">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="28f5b-258">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="28f5b-259">공개 에지 서버 연결은 HTTP/2를 사용하지만 Kestrel에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-259">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="28f5b-260">대상 프레임워크: IIS Out-of-Process 배포에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-260">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="28f5b-261">HTTP/2 연결은 [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 및 TLS 1.2 이상을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="28f5b-261">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="28f5b-262">자세한 정보는 서버 배포 시나리오와 관련된 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28f5b-262">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28f5b-263">추가 자료</span><span class="sxs-lookup"><span data-stu-id="28f5b-263">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
