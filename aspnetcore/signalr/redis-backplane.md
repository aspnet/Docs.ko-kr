---
title: 'ASP.NET Core 스케일 아웃에 대 한 Redis 백플레인 :::no-loc(SignalR):::'
author: bradygaster
description: 'ASP.NET Core 앱에 대 한 확장을 사용 하도록 Redis 후면판을 설정 하는 방법에 대해 알아봅니다 :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/redis-backplane
ms.openlocfilehash: e92f515b82b8ee76f98eaa1fca51feb9cdd14d5c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059639"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-no-locsignalr-scale-out"></a><span data-ttu-id="8e5e4-103">ASP.NET Core 스케일 아웃에 대 한 Redis 후면판 설정 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="8e5e4-103">Set up a Redis backplane for ASP.NET Core :::no-loc(SignalR)::: scale-out</span></span>

<span data-ttu-id="8e5e4-104">[Andrew Stanton-간호사](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="8e5e4-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="8e5e4-105">이 문서에서는 :::no-loc(SignalR)::: ASP.NET Core 앱을 확장 하는 데 사용할 [Redis](https://redis.io/) 서버 설정의 특정 측면을 설명 합니다 :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="8e5e4-105">This article explains :::no-loc(SignalR):::-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core :::no-loc(SignalR)::: app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="8e5e4-106">Redis 후면판 설정</span><span class="sxs-lookup"><span data-stu-id="8e5e4-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="8e5e4-107">Redis 서버를 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="8e5e4-108">프로덕션 사용을 위해 Redis 후면판은 앱과 동일한 데이터 센터에서 실행 되는 경우에만 권장 됩니다 :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="8e5e4-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the :::no-loc(SignalR)::: app.</span></span> <span data-ttu-id="8e5e4-109">그렇지 않으면 네트워크 대기 시간이 성능을 저하 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="8e5e4-110">:::no-loc(SignalR):::앱이 azure 클라우드에서 실행 되는 경우 :::no-loc(SignalR)::: Redis 후면판 대신 azure 서비스를 권장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-110">If your :::no-loc(SignalR)::: app is running in the Azure cloud, we recommend Azure :::no-loc(SignalR)::: Service instead of a Redis backplane.</span></span> <span data-ttu-id="8e5e4-111">개발 및 테스트 환경에 Azure Redis Cache 서비스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="8e5e4-112">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="8e5e4-113">Redis 설명서</span><span class="sxs-lookup"><span data-stu-id="8e5e4-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="8e5e4-114">Azure Redis Cache 설명서</span><span class="sxs-lookup"><span data-stu-id="8e5e4-114">Azure Redis Cache documentation</span></span>](/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="8e5e4-115">앱에서 :::no-loc(SignalR)::: NuGet 패키지를 설치 합니다 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Redis` .</span><span class="sxs-lookup"><span data-stu-id="8e5e4-115">In the :::no-loc(SignalR)::: app, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Redis` NuGet package.</span></span>
* <span data-ttu-id="8e5e4-116">메서드에서 다음 `Startup.ConfigureServices` 을 호출 합니다 `AddRedis` `Add:::no-loc(SignalR):::` .</span><span class="sxs-lookup"><span data-stu-id="8e5e4-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `Add:::no-loc(SignalR):::`:</span></span>

  ```csharp
  services.Add:::no-loc(SignalR):::().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="8e5e4-117">필요에 따라 옵션을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="8e5e4-118">대부분의 옵션은 연결 문자열 또는 [Configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 개체에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="8e5e4-119">에 지정 된 옵션 `ConfigurationOptions` 은 연결 문자열에 설정 된 옵션을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="8e5e4-120">다음 예제에서는 개체에서 옵션을 설정 하는 방법을 보여 줍니다 `ConfigurationOptions` .</span><span class="sxs-lookup"><span data-stu-id="8e5e4-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="8e5e4-121">이 예제에서는 다음 단계에 설명 된 것 처럼 여러 앱이 동일한 Redis 인스턴스를 공유할 수 있도록 채널 접두사를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.Add:::no-loc(SignalR):::()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="8e5e4-122">위의 코드에서 `options.Configuration` 는 연결 문자열에 지정 된 항목을 사용 하 여 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="8e5e4-123">앱에서 :::no-loc(SignalR)::: 다음 NuGet 패키지 중 하나를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-123">In the :::no-loc(SignalR)::: app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="8e5e4-124">`Microsoft.AspNetCore.:::no-loc(SignalR):::.StackExchangeRedis` -Redis 2. X.X.에 종속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-124">`Microsoft.AspNetCore.:::no-loc(SignalR):::.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="8e5e4-125">ASP.NET Core 2.2 이상에 권장 되는 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="8e5e4-126">`Microsoft.AspNetCore.:::no-loc(SignalR):::.Redis` -Redis 1. X.X.에 종속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-126">`Microsoft.AspNetCore.:::no-loc(SignalR):::.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="8e5e4-127">이 패키지는 ASP.NET Core 3.0 이상에 포함 되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="8e5e4-128">`Startup.ConfigureServices`메서드에서 다음을 호출 합니다 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> .</span><span class="sxs-lookup"><span data-stu-id="8e5e4-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.Add:::no-loc(SignalR):::().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="8e5e4-129">를 사용 하 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Redis` 는 경우를 호출 <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-129">When using `Microsoft.AspNetCore.:::no-loc(SignalR):::.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="8e5e4-130">필요에 따라 옵션을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="8e5e4-131">대부분의 옵션은 연결 문자열 또는 [Configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 개체에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="8e5e4-132">에 지정 된 옵션 `ConfigurationOptions` 은 연결 문자열에 설정 된 옵션을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="8e5e4-133">다음 예제에서는 개체에서 옵션을 설정 하는 방법을 보여 줍니다 `ConfigurationOptions` .</span><span class="sxs-lookup"><span data-stu-id="8e5e4-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="8e5e4-134">이 예제에서는 다음 단계에 설명 된 것 처럼 여러 앱이 동일한 Redis 인스턴스를 공유할 수 있도록 채널 접두사를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.Add:::no-loc(SignalR):::()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="8e5e4-135">를 사용 하 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Redis` 는 경우를 호출 <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-135">When using `Microsoft.AspNetCore.:::no-loc(SignalR):::.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="8e5e4-136">위의 코드에서 `options.Configuration` 는 연결 문자열에 지정 된 항목을 사용 하 여 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="8e5e4-137">Redis 옵션에 대 한 자세한 내용은 [Stackexchange Redis 설명서](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="8e5e4-138">앱에서 :::no-loc(SignalR)::: 다음 NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-138">In the :::no-loc(SignalR)::: app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.:::no-loc(SignalR):::.StackExchangeRedis`
  
* <span data-ttu-id="8e5e4-139">`Startup.ConfigureServices`메서드에서 다음을 호출 합니다 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> .</span><span class="sxs-lookup"><span data-stu-id="8e5e4-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.Add:::no-loc(SignalR):::().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="8e5e4-140">필요에 따라 옵션을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="8e5e4-141">대부분의 옵션은 연결 문자열 또는 [Configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 개체에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="8e5e4-142">에 지정 된 옵션 `ConfigurationOptions` 은 연결 문자열에 설정 된 옵션을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="8e5e4-143">다음 예제에서는 개체에서 옵션을 설정 하는 방법을 보여 줍니다 `ConfigurationOptions` .</span><span class="sxs-lookup"><span data-stu-id="8e5e4-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="8e5e4-144">이 예제에서는 다음 단계에 설명 된 것 처럼 여러 앱이 동일한 Redis 인스턴스를 공유할 수 있도록 채널 접두사를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.Add:::no-loc(SignalR):::()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="8e5e4-145">위의 코드에서 `options.Configuration` 는 연결 문자열에 지정 된 항목을 사용 하 여 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="8e5e4-146">Redis 옵션에 대 한 자세한 내용은 [Stackexchange Redis 설명서](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="8e5e4-147">여러 앱에 대해 하나의 Redis 서버를 사용 하는 경우 :::no-loc(SignalR)::: 각 앱에 대해 다른 채널 접두사를 사용 :::no-loc(SignalR)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-147">If you're using one Redis server for multiple :::no-loc(SignalR)::: apps, use a different channel prefix for each :::no-loc(SignalR)::: app.</span></span>

  <span data-ttu-id="8e5e4-148">채널 접두사를 설정 하면 :::no-loc(SignalR)::: 다른 채널 접두사를 사용 하는 다른 앱에서 하나의 앱을 격리 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-148">Setting a channel prefix isolates one :::no-loc(SignalR)::: app from others that use different channel prefixes.</span></span> <span data-ttu-id="8e5e4-149">서로 다른 접두사를 할당 하지 않으면 한 앱에서 모든 자체 클라이언트로 전송 되는 메시지는 Redis 서버를 후면판으로 사용 하는 모든 앱의 모든 클라이언트로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="8e5e4-150">고정 세션에 대해 서버 팜 부하 분산 소프트웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="8e5e4-151">이 작업을 수행 하는 방법에 대 한 설명서의 몇 가지 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="8e5e4-152">IIS</span><span class="sxs-lookup"><span data-stu-id="8e5e4-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="8e5e4-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="8e5e4-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="8e5e4-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="8e5e4-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="8e5e4-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="8e5e4-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="8e5e4-156">Redis 서버 오류</span><span class="sxs-lookup"><span data-stu-id="8e5e4-156">Redis server errors</span></span>

<span data-ttu-id="8e5e4-157">Redis 서버 작동이 중단 되 면 :::no-loc(SignalR)::: 메시지가 전달 되지 않음을 나타내는 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-157">When a Redis server goes down, :::no-loc(SignalR)::: throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="8e5e4-158">몇 가지 일반적인 예외 메시지:</span><span class="sxs-lookup"><span data-stu-id="8e5e4-158">Some typical exception messages:</span></span>

* <span data-ttu-id="8e5e4-159">*메시지를 쓰지 못했습니다.*</span><span class="sxs-lookup"><span data-stu-id="8e5e4-159">*Failed writing message*</span></span>
* <span data-ttu-id="8e5e4-160">*' MethodName ' 허브 메서드를 호출 하지 못했습니다.*</span><span class="sxs-lookup"><span data-stu-id="8e5e4-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="8e5e4-161">*Redis에 연결 하지 못했습니다.*</span><span class="sxs-lookup"><span data-stu-id="8e5e4-161">*Connection to Redis failed*</span></span>

<span data-ttu-id="8e5e4-162">:::no-loc(SignalR)::: 서버를 백업할 때 메시지를 보내도록 버퍼링 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-162">:::no-loc(SignalR)::: doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="8e5e4-163">Redis 서버를 종료 하는 동안 전송 된 모든 메시지는 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-163">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="8e5e4-164">:::no-loc(SignalR)::: Redis 서버를 다시 사용할 수 있게 되 면 자동으로 다시 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-164">:::no-loc(SignalR)::: automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="8e5e4-165">연결 실패에 대 한 사용자 지정 동작</span><span class="sxs-lookup"><span data-stu-id="8e5e4-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="8e5e4-166">Redis 연결 실패 이벤트를 처리 하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

::: moniker range="= aspnetcore-2.1"

```csharp
services.Add:::no-loc(SignalR):::()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.Add:::no-loc(SignalR):::()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a><span data-ttu-id="8e5e4-167">Redis 클러스터링</span><span class="sxs-lookup"><span data-stu-id="8e5e4-167">Redis Clustering</span></span>

<span data-ttu-id="8e5e4-168">[Redis 클러스터링](https://redis.io/topics/cluster-spec) 은 여러 Redis 서버를 사용 하 여 고가용성을 달성 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="8e5e4-169">클러스터링은 공식적으로 지원 되지 않지만 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8e5e4-170">다음 단계</span><span class="sxs-lookup"><span data-stu-id="8e5e4-170">Next steps</span></span>

<span data-ttu-id="8e5e4-171">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8e5e4-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="8e5e4-172">Redis 설명서</span><span class="sxs-lookup"><span data-stu-id="8e5e4-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="8e5e4-173">StackExchange Redis 설명서</span><span class="sxs-lookup"><span data-stu-id="8e5e4-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="8e5e4-174">Azure Redis Cache 설명서</span><span class="sxs-lookup"><span data-stu-id="8e5e4-174">Azure Redis Cache documentation</span></span>](/azure/redis-cache/)