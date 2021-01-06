---
title: ASP.NET Core 호스트 및 배포 Blazor Server
author: guardrex
description: ASP.NET Core를 사용하여 Blazor Server 앱을 호스트 및 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: a209109210ef5e335734a974ceb0c2af7cb8e1a1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94595443"
---
# <a name="host-and-deploy-no-locblazor-server"></a><span data-ttu-id="e025f-103">Blazor Server 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="e025f-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="e025f-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e025f-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="e025f-105">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="e025f-105">Host configuration values</span></span>

<span data-ttu-id="e025f-106">[Blazor Server 앱](xref:blazor/hosting-models#blazor-server)은 [일반 호스트 구성 값](xref:fundamentals/host/generic-host#host-configuration)을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="e025f-107">배포</span><span class="sxs-lookup"><span data-stu-id="e025f-107">Deployment</span></span>

<span data-ttu-id="e025f-108">[Blazor Server 호스팅 모델](xref:blazor/hosting-models#blazor-server)을 사용하면 Blazor가 서버의 ASP.NET Core 앱 내에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="e025f-109">UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="e025f-110">ASP.NET Core 앱을 호스팅할 수 있는 웹 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="e025f-111">Visual Studio는 **Blazor Server 앱** 프로젝트 템플릿([`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorserverside` 템플릿)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="e025f-112">확장성</span><span class="sxs-lookup"><span data-stu-id="e025f-112">Scalability</span></span>

<span data-ttu-id="e025f-113">Blazor Server 앱에 사용 가능한 인프라를 최대한 활용하도록 배포를 계획합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="e025f-114">Blazor Server 앱 확장성에 대해서는 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e025f-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="e025f-115">Blazor Server 앱의 기본 사항</span><span class="sxs-lookup"><span data-stu-id="e025f-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="e025f-116">배포 서버</span><span class="sxs-lookup"><span data-stu-id="e025f-116">Deployment server</span></span>

<span data-ttu-id="e025f-117">단일 서버의 확장성(규모 확대)을 고려할 때 앱에 사용할 수 있는 메모리는 사용자 요구가 늘어남에 따라 앱이 소진하는 첫 번째 리소스일 가능성이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="e025f-118">서버에서 사용 가능한 메모리는 다음 사항에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="e025f-119">서버가 지원할 수 있는 활성 회로 수</span><span class="sxs-lookup"><span data-stu-id="e025f-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="e025f-120">클라이언트의 UI 대기 시간</span><span class="sxs-lookup"><span data-stu-id="e025f-120">UI latency on the client.</span></span>

<span data-ttu-id="e025f-121">안전하고 확장 가능한 Blazor 서버 앱을 빌드하는 방법에 대한 지침은 <xref:blazor/security/server/threat-mitigation>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e025f-121">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="e025f-122">각 회로는 최소 *Hello World* 와 같은 앱에 약 250KB의 메모리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="e025f-123">회로의 크기는 앱의 코드 및 각 구성 요소와 연결된 상태 유지 관리 요구 사항에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="e025f-124">애플리케이션 및 인프라를 개발하는 도중에 리소스 요구를 측정하는 것이 좋지만, 다음 기준은 배포 대상을 계획할 때 출발점으로 삼을 수 있습니다. 앱에서 5,000명의 동시 사용자를 지원할 것으로 예상되는 경우 앱에 최소 1.3GB(또는 사용자당 273KB)의 서버 메모리를 예산하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="no-locsignalr-configuration"></a><span data-ttu-id="e025f-125">SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="e025f-125">SignalR configuration</span></span>

<span data-ttu-id="e025f-126">Blazor Server 앱은 ASP.NET Core SignalR을 사용하여 브라우저와 통신합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="e025f-127">[SignalR의 호스팅 및 크기 조정 조건](xref:signalr/publish-to-azure-web-app)이 Blazor Server 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="e025f-128">Blazor는 짧은 대기 시간, 안정성 및 [보안](xref:signalr/security) 덕분에 WebSocket을 SignalR 전송으로 사용하는 경우에 가장 효과적입니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="e025f-129">WebSocket을 사용할 수 없거나 앱이 긴 폴링을 사용하도록 명시적으로 구성된 경우 SignalR에서 긴 폴링을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="e025f-130">Azure App Service에 배포하는 경우 서비스에 대한 Azure Portal 설정에서 WebSocket을 사용하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="e025f-131">Azure App Service에 대해 앱을 구성하는 방법에 대한 자세한 내용은 [SignalR 게시 지침](xref:signalr/publish-to-azure-web-app)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e025f-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-no-locsignalr-service"></a><span data-ttu-id="e025f-132">Azure SignalR 서비스</span><span class="sxs-lookup"><span data-stu-id="e025f-132">Azure SignalR Service</span></span>

<span data-ttu-id="e025f-133">Blazor Server 앱에 [Azure SignalR Service](xref:signalr/scale#azure-signalr-service)를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-133">We recommend using the [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) for Blazor Server apps.</span></span> <span data-ttu-id="e025f-134">이 서비스를 사용하면 Blazor Server 앱을 다수의 동시 SignalR 연결로 스케일 업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="e025f-135">또한 SignalR 서비스의 글로벌 및 고성능 데이터 센터는 지리적 위치로 인한 대기 시간을 줄이는 데 큰 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e025f-136">[WebSocket](https://wikipedia.org/wiki/WebSocket)이 사용되지 않는 경우 Azure App Service는 HTTP 긴 폴링을 사용하여 실시간 연결을 시뮬레이트합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-136">When [WebSockets](https://wikipedia.org/wiki/WebSocket) are disabled, Azure App Service simulates a real-time connection using HTTP Long Polling.</span></span> <span data-ttu-id="e025f-137">HTTP 긴 폴링은 WebSocket을 사용하여 실행하는 것보다 훨씬 더 느리므로 클라이언트-서버 연결을 시뮬레이트하는 데는 폴링을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-137">HTTP Long Polling is noticeably slower than running with WebSockets enabled, which doesn't use polling to simulate a client-server connection.</span></span>
>
> <span data-ttu-id="e025f-138">Azure App Service에 배포된 Blazor Server 앱에 WebSocket을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-138">We recommend using WebSockets for Blazor Server apps deployed to Azure App Service.</span></span> <span data-ttu-id="e025f-139">[Azure SignalR Service](xref:signalr/scale#azure-signalr-service)는 기본적으로 WebSocket을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-139">The [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) uses WebSockets by default.</span></span> <span data-ttu-id="e025f-140">앱이 Azure SignalR Service를 사용하지 않는 경우 <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e025f-140">If the app doesn't use the Azure SignalR Service, see <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>.</span></span>
>
> <span data-ttu-id="e025f-141">자세한 내용은 다음을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="e025f-141">For more information, see:</span></span>
>
> * [<span data-ttu-id="e025f-142">Azure SignalR Service란?</span><span class="sxs-lookup"><span data-stu-id="e025f-142">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
> * [<span data-ttu-id="e025f-143">Azure SignalR Service를 위한 성능 가이드</span><span class="sxs-lookup"><span data-stu-id="e025f-143">Performance guide for Azure SignalR Service</span></span>](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a><span data-ttu-id="e025f-144">구성</span><span class="sxs-lookup"><span data-stu-id="e025f-144">Configuration</span></span>

<span data-ttu-id="e025f-145">Azure SignalR Service에 대해 앱을 구성하려면 앱은 [사전 렌더링하는 경우 클라이언트를 동일한 서버로 다시 리디렉션](xref:blazor/hosting-models#connection-to-the-server)하는 ‘고정 세션’을 지원해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-145">To configure an app for the Azure SignalR Service, the app must support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="e025f-146">`ServerStickyMode` 옵션 또는 구성 값을 `Required`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-146">The `ServerStickyMode` option or configuration value is set to `Required`.</span></span> <span data-ttu-id="e025f-147">일반적으로 앱은 다음 방법 중 **‘하나’** 를 사용하여 구성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-147">Typically, an app creates the configuration using **_ONE_** of the following approaches:</span></span>

   * <span data-ttu-id="e025f-148">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e025f-148">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="e025f-149">구성(다음 방법 중 **‘하나’** 사용):</span><span class="sxs-lookup"><span data-stu-id="e025f-149">Configuration (use **_ONE_** of the following approaches):</span></span>
  
     * <span data-ttu-id="e025f-150">`appsettings.json`의 경우</span><span class="sxs-lookup"><span data-stu-id="e025f-150">In `appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * <span data-ttu-id="e025f-151">Azure Portal에서 앱 서비스의 **구성** > **애플리케이션 설정**(**이름**: `Azure__SignalR__StickyServerMode`, **값**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="e025f-151">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure__SignalR__StickyServerMode`, **Value**: `Required`).</span></span> <span data-ttu-id="e025f-152">이 방법은 [Azure SignalR Service를 프로비저닝](#provision-the-azure-signalr-service)하는 경우 자동으로 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-152">This approach is adopted for the app automatically if you [provision the Azure SignalR Service](#provision-the-azure-signalr-service).</span></span>

### <a name="provision-the-azure-no-locsignalr-service"></a><span data-ttu-id="e025f-153">Azure SignalR Service 프로비저닝</span><span class="sxs-lookup"><span data-stu-id="e025f-153">Provision the Azure SignalR Service</span></span>

<span data-ttu-id="e025f-154">Visual Studio에서 앱에 대해 Azure SignalR Service를 프로비저닝하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-154">To provision the Azure SignalR Service for an app in Visual Studio:</span></span>

1. <span data-ttu-id="e025f-155">Visual Studio에 Blazor Server 앱을 위한 Azure 앱 게시 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-155">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="e025f-156">**Azure SignalR Service** 종속성을 프로필에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-156">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="e025f-157">앱에 할당할 기존 Azure SignalR Service 인스턴스가 Azure 구독에 없는 경우 **새 Azure SignalR Service 인스턴스 만들기** 를 선택하여 새 서비스 인스턴스를 프로비전합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-157">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="e025f-158">Azure에 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-158">Publish the app to Azure.</span></span>

<span data-ttu-id="e025f-159">Visual Studio에서 Azure SignalR Service를 프로비저닝하면 자동으로 [‘고정 세션’이 사용](#configuration)되고 SignalR 연결 문자열이 앱 서비스 구성에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-159">Provisioning the Azure SignalR Service in Visual Studio automatically [enables *sticky sessions*](#configuration) and adds the SignalR connection string to the app service's configuration.</span></span>

#### <a name="iis"></a><span data-ttu-id="e025f-160">IIS</span><span class="sxs-lookup"><span data-stu-id="e025f-160">IIS</span></span>

<span data-ttu-id="e025f-161">IIS를 사용하는 경우 다음을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-161">When using IIS, enable:</span></span>

* <span data-ttu-id="e025f-162">[IIS WebSocket](xref:fundamentals/websockets#enabling-websockets-on-iis)</span><span class="sxs-lookup"><span data-stu-id="e025f-162">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="e025f-163">[애플리케이션 요청 라우팅에 대한 고정 세션](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)</span><span class="sxs-lookup"><span data-stu-id="e025f-163">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="e025f-164">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="e025f-164">Kubernetes</span></span>

<span data-ttu-id="e025f-165">[Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)(고정 세션용 Kubernetes 주석)을 사용하여 다음과 같은 수신 정의를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-165">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="e025f-166">Nginx를 사용하는 Linux</span><span class="sxs-lookup"><span data-stu-id="e025f-166">Linux with Nginx</span></span>

<span data-ttu-id="e025f-167">SignalR WebSocket이 제대로 작동하려면 프록시의 `Upgrade` 및 `Connection` 헤더가 다음 값으로 설정되고 다음 중 하나에 `$connection_upgrade`가 매핑되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-167">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="e025f-168">기본적으로 업그레이드 헤더 값입니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-168">The Upgrade header value by default.</span></span>
* <span data-ttu-id="e025f-169">`close` 업그레이드 헤더가 없거나 비어 있는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-169">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="e025f-170">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e025f-170">For more information, see the following articles:</span></span>

* [<span data-ttu-id="e025f-171">WebSocket Proxy로 활용하는 NGINX</span><span class="sxs-lookup"><span data-stu-id="e025f-171">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="e025f-172">WebSocket 프록시</span><span class="sxs-lookup"><span data-stu-id="e025f-172">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="e025f-173">Apache를 사용하는 Linux</span><span class="sxs-lookup"><span data-stu-id="e025f-173">Linux with Apache</span></span>

<span data-ttu-id="e025f-174">Linux에서 Apache 뒤에 Blazor 앱을 호스트하려면 HTTP 및 WebSockets 트래픽에 대해 `ProxyPass`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-174">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="e025f-175">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="e025f-175">In the following example:</span></span>

* <span data-ttu-id="e025f-176">Kestrel 서버가 호스트 컴퓨터에서 실행되고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-176">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="e025f-177">앱은 포트 5000에서 트래픽을 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-177">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="e025f-178">다음 모듈을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-178">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="e025f-179">브라우저 콘솔에서 WebSockets 오류를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-179">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="e025f-180">오류의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-180">Example errors:</span></span>

* <span data-ttu-id="e025f-181">Firefox가 ws://the-domain-name.tld/_blazor?id=XXX에서 서버에 대한 연결을 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-181">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="e025f-182">오류: ‘WebSockets’ 전송을 시작하지 못했습니다. 오류: 전송에 오류가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-182">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="e025f-183">오류: ‘LongPolling’ 전송을 시작하지 못했습니다. TypeError: this.transport가 정의되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-183">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="e025f-184">오류: 사용 가능한 전송 중 하나를 사용하여 서버에 연결할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-184">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="e025f-185">WebSockets 실패</span><span class="sxs-lookup"><span data-stu-id="e025f-185">WebSockets failed</span></span>
* <span data-ttu-id="e025f-186">오류: 연결 상태가 ‘연결됨’이 아닌 경우 데이터를 보낼 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-186">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="e025f-187">자세한 내용은 [Apache 설명서](https://httpd.apache.org/docs/current/mod/mod_proxy.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e025f-187">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="e025f-188">네트워크 대기 시간 측정</span><span class="sxs-lookup"><span data-stu-id="e025f-188">Measure network latency</span></span>

<span data-ttu-id="e025f-189">[JS interop](xref:blazor/call-javascript-from-dotnet)은 다음 예제와 같이 네트워크 대기 시간을 측정하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-189">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="e025f-190">적절한 UI 환경을 위해 UI 대기 시간을 250ms 이하로 유지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e025f-190">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
