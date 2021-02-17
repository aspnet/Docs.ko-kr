---
title: ASP.NET Core SignalR 프로덕션 호스팅 및 크기 조정
author: bradygaster
description: ASP.NET Core를 사용 하는 앱에서 성능 및 크기 조정 문제를 방지 하는 방법에 대해 알아봅니다 SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: e70f3143159a1817e326a95b30e7369a5c9ab025
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564012"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a><span data-ttu-id="3e978-103">ASP.NET Core SignalR 호스팅 및 크기 조정</span><span class="sxs-lookup"><span data-stu-id="3e978-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="3e978-104">[Andrew Stanton-간호사](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="3e978-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="3e978-105">이 문서에서는 ASP.NET Core를 사용 하는 트래픽이 많은 앱에 대 한 호스팅 및 확장 고려 사항을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="3e978-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="3e978-106">고정 세션</span><span class="sxs-lookup"><span data-stu-id="3e978-106">Sticky Sessions</span></span>

<span data-ttu-id="3e978-107">SignalR 특정 연결에 대 한 모든 HTTP 요청을 동일한 서버 프로세스에서 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="3e978-108">SignalR가 서버 팜 (여러 서버)에서 실행 되는 경우 "고정 세션"을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="3e978-109">"고정 세션"은 일부 부하 분산 장치에서 세션 선호도 라고도 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="3e978-110">Azure App Service는 ARR ( [응용 프로그램 요청 라우팅](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) )을 사용 하 여 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-110">Azure App Service uses [Application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="3e978-111">Azure App Service에서 "ARR 선호도" 설정을 사용 하도록 설정 하면 "고정 세션"을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="3e978-112">고정 세션이 필요 하지 않은 유일한 경우는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="3e978-113">단일 서버에서 호스팅할 때 단일 프로세스에서.</span><span class="sxs-lookup"><span data-stu-id="3e978-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="3e978-114">Azure 서비스를 사용 하는 경우 SignalR .</span><span class="sxs-lookup"><span data-stu-id="3e978-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="3e978-115">모든 클라이언트가 Websocket **만** 사용 하도록 구성 되 **고** [skipnegotiation 설정은](xref:signalr/configuration#configure-additional-options) 클라이언트 구성에서 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="3e978-116">다른 모든 상황 (Redis 후면판을 사용 하는 경우 포함)에서는 고정 세션에 대해 서버 환경을 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="3e978-117">에 대해 Azure App Service를 구성 하는 방법에 대 한 지침은 SignalR 을 참조 하십시오 <xref:signalr/publish-to-azure-web-app> .</span><span class="sxs-lookup"><span data-stu-id="3e978-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="3e978-118">TCP 연결 리소스</span><span class="sxs-lookup"><span data-stu-id="3e978-118">TCP connection resources</span></span>

<span data-ttu-id="3e978-119">웹 서버에서 지원할 수 있는 동시 TCP 연결 수가 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="3e978-120">표준 HTTP 클라이언트는 *임시* 연결을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="3e978-121">클라이언트를 유휴 상태로 전환 하 고 나중에 다시 열 때 이러한 연결을 닫을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="3e978-122">반면에 SignalR 연결은 *영구적* 입니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-122">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="3e978-123">SignalR 클라이언트가 유휴 상태가 되는 경우에도 연결이 열린 상태로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="3e978-124">많은 클라이언트에 서비스를 제공 하는 트래픽이 많은 앱에서 이러한 영구 연결을 사용 하면 서버가 최대 연결 수에 도달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="3e978-125">또한 영구 연결에서는 추가 메모리를 사용 하 여 각 연결을 추적 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="3e978-126">에서 연결 관련 리소스를 많이 사용 하는 SignalR 것은 동일한 서버에서 호스트 되는 다른 웹 앱에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="3e978-127">SignalR가 열리고 사용 가능한 마지막 TCP 연결을 보유 하는 경우 동일한 서버의 다른 웹 앱에도 더 이상 사용할 수 있는 연결이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="3e978-128">서버에 연결 되지 않은 경우 임의의 소켓 오류 및 연결 다시 설정 오류가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="3e978-129">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="3e978-130">SignalR다른 웹 앱에서 리소스를 사용 하 여 오류가 발생 하는 것을 방지 하려면 SignalR 다른 웹 앱과 다른 서버에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="3e978-131">리소스를 사용 하 여 응용 프로그램에서 오류가 발생 하는 것을 방지 하려면 SignalR SignalR 서버에서 처리 해야 하는 연결 수를 제한 하도록 확장 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="3e978-132">확장</span><span class="sxs-lookup"><span data-stu-id="3e978-132">Scale out</span></span>

<span data-ttu-id="3e978-133">를 사용 하는 앱은 SignalR 모든 연결을 추적 해야 하므로 서버 팜에 대 한 문제가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="3e978-134">서버를 추가 하 고 다른 서버에서 알지 못하는 새 연결을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="3e978-135">예를 들어 SignalR 다음 다이어그램의 각 서버에서 다른 서버에 대 한 연결을 인식 하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="3e978-136">SignalR서버 중 하나에서 모든 클라이언트에 게 메시지를 보내려고 하면 해당 서버에 연결 된 클라이언트에만 메시지가 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![크기 조정::: no-loc (SignalR)::: 후면판 없음](scale/_static/scale-no-backplane.png)

<span data-ttu-id="3e978-138">이 문제를 해결 하기 위한 옵션은 [Azure SignalR 서비스](#azure-signalr-service) 및 [Redis 후면판](#redis-backplane)입니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-signalr-service"></a><span data-ttu-id="3e978-139">Azure SignalR 서비스</span><span class="sxs-lookup"><span data-stu-id="3e978-139">Azure SignalR Service</span></span>

<span data-ttu-id="3e978-140">Azure SignalR 서비스는 후면판이 아닌 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="3e978-141">클라이언트는 서버에 대 한 연결을 시작할 때마다 서비스에 연결 하기 위해 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="3e978-142">이 프로세스는 다음 다이어그램에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-142">That process is illustrated in the following diagram:</span></span>

![Azure::: no loc (SignalR)::: Service에 대 한 연결 설정](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="3e978-144">그 결과 서비스는 모든 클라이언트 연결을 관리 하는 반면, 각 서버는 다음 다이어그램에 표시 된 것과 같이 서비스에 대 한 연결 수가 작은 경우에만 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![서비스에 연결 된 클라이언트, 서비스에 연결 된 서버](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="3e978-146">규모 확장에 대 한이 방법은 Redis 후면판 대안에 비해 몇 가지 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="3e978-147">[클라이언트 선호도](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)라고도 하는 고정 세션은 클라이언트에서 연결할 때 Azure 서비스로 즉시 리디렉션되도록 할 필요가 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="3e978-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="3e978-148">SignalR앱은 전송 된 메시지 수를 기준으로 규모를 확장할 수 있으며, Azure SignalR 서비스는 연결 수를 제한 하도록 확장 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service scales to handle any number of connections.</span></span> <span data-ttu-id="3e978-149">예를 들어 수천 개의 클라이언트가 있을 수 있지만, 초당 몇 개의 메시지만 보내는 경우 SignalR 앱은 연결 자체를 처리 하기 위해 여러 서버로 확장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="3e978-150">앱은를 SignalR 사용 하지 않고 웹 앱 보다 훨씬 더 많은 연결 리소스를 사용 하지 않습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="3e978-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="3e978-151">이러한 이유로 SignalR SignalR App Service, vm 및 컨테이너를 포함 하 여 azure에서 호스트 되는 모든 ASP.NET Core 앱에 대 한 azure 서비스를 권장 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="3e978-152">자세한 내용은 [Azure SignalR 서비스 설명서](/azure/azure-signalr/signalr-overview)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="3e978-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="3e978-153">Redis 백플레인</span><span class="sxs-lookup"><span data-stu-id="3e978-153">Redis backplane</span></span>

<span data-ttu-id="3e978-154">[Redis](https://redis.io/) 는 게시/구독 모델을 사용 하 여 메시징 시스템을 지 원하는 메모리 내 키-값 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="3e978-155">SignalRRedis 후면판은 pub/sub 기능을 사용 하 여 메시지를 다른 서버로 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="3e978-156">클라이언트에서 연결 하면 연결 정보가 후면판에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="3e978-157">서버는 모든 클라이언트에 메시지를 보내려고 할 때 후면판으로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="3e978-158">후면판은 연결 된 모든 클라이언트와 해당 클라이언트가 있는 서버를 인식 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="3e978-159">각 서버를 통해 모든 클라이언트에 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="3e978-160">이 프로세스는 다음 다이어그램에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-160">This process is illustrated in the following diagram:</span></span>

![Redis 백플레인, 한 서버에서 모든 클라이언트로 전송 되는 메시지](scale/_static/redis-backplane.png)

<span data-ttu-id="3e978-162">Redis 후면판은 사용자의 인프라에서 호스트 되는 앱에 대해 권장 되는 스케일 아웃 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="3e978-163">데이터 센터와 Azure 데이터 센터 간에 상당한 연결 대기 시간이 있는 경우 Azure SignalR 서비스는 대기 시간이 짧고 처리량이 많은 요구 사항이 있는 온-프레미스 앱에 대 한 실용적인 옵션이 아닐 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="3e978-164">앞에서 SignalR 설명한 Azure 서비스 이점은 Redis 후면판의 단점입니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="3e978-165">다음 두 가지 조건에 **모두** 해당 하는 경우를 제외 하 고, [클라이언트 선호도](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)라고도 하는 고정 세션이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="3e978-166">모든 클라이언트는 Websocket **만** 사용 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="3e978-167">[Skipnegotiation 설정은](xref:signalr/configuration#configure-additional-options) 클라이언트 구성에서 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="3e978-168">서버에서 연결을 시작한 후에는 해당 서버에서 연결을 유지 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="3e978-169">SignalR앱은 전송 되는 메시지가 적은 경우에도 클라이언트 수를 기준으로 규모를 확장 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="3e978-170">앱은를 SignalR 사용 하지 않고 웹 앱 보다 훨씬 더 많은 연결 리소스를 사용 SignalR 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="3e978-171">Windows 클라이언트 OS에 대 한 IIS 제한 사항</span><span class="sxs-lookup"><span data-stu-id="3e978-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="3e978-172">Windows 10 및 Windows 8.x은 클라이언트 운영 체제입니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="3e978-173">클라이언트 운영 체제의 IIS는 동시 연결 수가 10 개로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="3e978-174">SignalR의 연결은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-174">SignalR's connections are:</span></span>

* <span data-ttu-id="3e978-175">일시적 이며 자주 다시 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="3e978-176">더 이상 사용 되지 않는 경우 즉시 삭제 **되지 않습니다** .</span><span class="sxs-lookup"><span data-stu-id="3e978-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="3e978-177">위의 조건은 클라이언트 OS에서 10 개의 연결 제한에 도달할 가능성이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="3e978-178">개발에 클라이언트 OS를 사용 하는 경우 다음을 수행 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="3e978-179">IIS를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-179">Avoid IIS.</span></span>
* <span data-ttu-id="3e978-180">Kestrel 또는 IIS Express을 배포 대상으로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="3e978-181">Nginx를 사용하는 Linux</span><span class="sxs-lookup"><span data-stu-id="3e978-181">Linux with Nginx</span></span>

<span data-ttu-id="3e978-182">다음에는에 대해 Websocket, ServerSentEvents 및 Un폴링을 사용 하도록 설정 하는 데 필요한 최소 설정이 포함 되어 있습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="3e978-182">The following contains the minimum required settings to enable WebSockets, ServerSentEvents, and LongPolling for SignalR:</span></span>

```nginx
http {
  map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

  server {
    listen 80;
    server_name example.com *.example.com;

    # Configure the SignalR Endpoint
    location /hubroute {
      # App server url
      proxy_pass http://localhost:5000;

      # Configuration for WebSockets
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_cache off;

      # Configuration for ServerSentEvents
      proxy_buffering off;

      # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
      proxy_read_timeout 100s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

<span data-ttu-id="3e978-183">여러 백 엔드 서버를 사용 하는 경우 연결 시 연결이 서버를 전환 하지 못하도록 고정 세션을 추가 해야 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="3e978-183">When multiple backend servers are used, sticky sessions must be added to prevent SignalR connections from switching servers when connecting.</span></span> <span data-ttu-id="3e978-184">Nginx에 고정 세션을 추가 하는 방법에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-184">There are multiple ways to add sticky sessions in Nginx.</span></span> <span data-ttu-id="3e978-185">사용 가능한 항목에 따라 다음과 같은 두 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-185">Two approaches are shown below depending on what you have available.</span></span>

<span data-ttu-id="3e978-186">이전 구성 외에도 다음이 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-186">The following is added in addition to the previous configuration.</span></span> <span data-ttu-id="3e978-187">다음 예에서는 `backend` 가 서버 그룹의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-187">In the following examples, `backend` is the name of the group of servers.</span></span>

<span data-ttu-id="3e978-188">[Nginx 오픈 소스](https://nginx.org/en/)를 사용 하 여 `ip_hash` 클라이언트의 IP 주소에 따라 서버에 대 한 연결을 라우팅하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-188">With [Nginx Open Source](https://nginx.org/en/), use `ip_hash` to route connections to a server based on the client's IP address:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    ip_hash;
  }
}
```

<span data-ttu-id="3e978-189">[Nginx Plus](https://www.nginx.com/products/nginx)를 사용 하 여를 `sticky` cookie 요청에 추가 하 고 사용자의 요청을 서버에 고정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-189">With [Nginx Plus](https://www.nginx.com/products/nginx), use `sticky` to add a cookie to requests and pin the user's requests to a server:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    sticky cookie srv_id expires=max domain=.example.com path=/ httponly;
  }
}
```

<span data-ttu-id="3e978-190">마지막으로 `proxy_pass http://localhost:5000` `server` 섹션에서를로 변경 `proxy_pass http://backend` 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e978-190">Finally, change `proxy_pass http://localhost:5000` in the `server` section to `proxy_pass http://backend`.</span></span>

<span data-ttu-id="3e978-191">Nginx를 통한 Websocket에 대 한 자세한 내용은 [Nginx as a Websocket Proxy](https://www.nginx.com/blog/websocket-nginx)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="3e978-191">For more information on WebSockets over Nginx, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx).</span></span>

<span data-ttu-id="3e978-192">부하 분산 및 고정 세션에 대 한 자세한 내용은 [NGINX 부하 분산](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="3e978-192">For more information on load balancing and sticky sessions, see [NGINX load balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).</span></span>

<span data-ttu-id="3e978-193">Nginx와 ASP.NET Core에 대 한 자세한 내용은 다음 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="3e978-193">For more information about ASP.NET Core with Nginx see the following article:</span></span>
* <xref:host-and-deploy/linux-nginx>

## <a name="third-party-signalr-backplane-providers"></a><span data-ttu-id="3e978-194">타사 SignalR 후면판 공급자</span><span class="sxs-lookup"><span data-stu-id="3e978-194">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="3e978-195">NCache</span><span class="sxs-lookup"><span data-stu-id="3e978-195">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="3e978-196">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="3e978-196">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>
* <span data-ttu-id="3e978-197">[Rebus](https://github.com/rebus-org/Rebus.SignalR)</span><span class="sxs-lookup"><span data-stu-id="3e978-197">[Rebus](https://github.com/rebus-org/Rebus.SignalR)</span></span>

## <a name="next-steps"></a><span data-ttu-id="3e978-198">다음 단계</span><span class="sxs-lookup"><span data-stu-id="3e978-198">Next steps</span></span>

<span data-ttu-id="3e978-199">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3e978-199">For more information, see the following resources:</span></span>

* [<span data-ttu-id="3e978-200">Azure SignalR 서비스 설명서</span><span class="sxs-lookup"><span data-stu-id="3e978-200">Azure SignalR Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="3e978-201">Redis 후면판 설정</span><span class="sxs-lookup"><span data-stu-id="3e978-201">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
