---
title: 'SignalR와 ASP.NET Core 간의 차이점SignalR'
author: bradygaster
description: 'SignalR와 ASP.NET Core 간의 차이점SignalR'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
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
uid: signalr/version-differences
ms.openlocfilehash: c4c0ff83cb789e9aa35085496daa461404615726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061212"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="e36c5-103">ASP.NET SignalR 와 ASP.NET Core 간의 차이점 SignalR</span><span class="sxs-lookup"><span data-stu-id="e36c5-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="e36c5-104">ASP.NET Core SignalR ASP.NET의 클라이언트 또는 서버와 호환 되지 않습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="e36c5-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="e36c5-105">이 문서에서는 ASP.NET Core에서 제거 되거나 변경 된 기능에 대해 자세히 설명 SignalR 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="e36c5-106">버전을 확인 하는 방법 SignalR</span><span class="sxs-lookup"><span data-stu-id="e36c5-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="e36c5-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="e36c5-107">ASP.NET SignalR</span></span> | <span data-ttu-id="e36c5-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="e36c5-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="e36c5-109">**서버 NuGet 패키지**</span><span class="sxs-lookup"><span data-stu-id="e36c5-109">**Server NuGet package**</span></span> | <span data-ttu-id="e36c5-110">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="e36c5-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="e36c5-111">없음</span><span class="sxs-lookup"><span data-stu-id="e36c5-111">None.</span></span> <span data-ttu-id="e36c5-112">[AspNetCore](xref:fundamentals/metapackage-app) 공유 프레임 워크에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="e36c5-113">**클라이언트 NuGet 패키지**</span><span class="sxs-lookup"><span data-stu-id="e36c5-113">**Client NuGet packages**</span></span> | <span data-ttu-id="e36c5-114">[Microsoft SignalR . AspNet. 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="e36c5-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="e36c5-115">[Microsoft SignalR . AspNet. .JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="e36c5-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="e36c5-116">[AspNetCore SignalR . 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="e36c5-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="e36c5-117">**JavaScript client npm 패키지**</span><span class="sxs-lookup"><span data-stu-id="e36c5-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="e36c5-118">signalr</span><span class="sxs-lookup"><span data-stu-id="e36c5-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="e36c5-119">**Java 클라이언트**</span><span class="sxs-lookup"><span data-stu-id="e36c5-119">**Java client**</span></span> | <span data-ttu-id="e36c5-120">[GitHub 리포지토리](https://github.com/SignalR/java-client) (사용 되지 않음)</span><span class="sxs-lookup"><span data-stu-id="e36c5-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="e36c5-121">Maven package [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="e36c5-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="e36c5-122">**서버 앱 유형**</span><span class="sxs-lookup"><span data-stu-id="e36c5-122">**Server app type**</span></span> | <span data-ttu-id="e36c5-123">ASP.NET (System.web) 또는 OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="e36c5-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="e36c5-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e36c5-124">ASP.NET Core</span></span> |
| <span data-ttu-id="e36c5-125">**지원 되는 서버 플랫폼**</span><span class="sxs-lookup"><span data-stu-id="e36c5-125">**Supported server platforms**</span></span> | <span data-ttu-id="e36c5-126">.NET Framework 4.5 이상</span><span class="sxs-lookup"><span data-stu-id="e36c5-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="e36c5-127">.NET Core 3.0 이상</span><span class="sxs-lookup"><span data-stu-id="e36c5-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="e36c5-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="e36c5-128">ASP.NET SignalR</span></span> | <span data-ttu-id="e36c5-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="e36c5-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="e36c5-130">**서버 NuGet 패키지**</span><span class="sxs-lookup"><span data-stu-id="e36c5-130">**Server NuGet package**</span></span> | <span data-ttu-id="e36c5-131">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="e36c5-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="e36c5-132">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)</span><span class="sxs-lookup"><span data-stu-id="e36c5-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="e36c5-133">[AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="e36c5-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="e36c5-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="e36c5-134">(.NET Framework)</span></span> |
| <span data-ttu-id="e36c5-135">**클라이언트 NuGet 패키지**</span><span class="sxs-lookup"><span data-stu-id="e36c5-135">**Client NuGet packages**</span></span> | <span data-ttu-id="e36c5-136">[Microsoft SignalR . AspNet. 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="e36c5-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="e36c5-137">[Microsoft SignalR . AspNet. .JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="e36c5-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="e36c5-138">[AspNetCore SignalR . 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="e36c5-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="e36c5-139">**JavaScript client npm 패키지**</span><span class="sxs-lookup"><span data-stu-id="e36c5-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="e36c5-140">signalr</span><span class="sxs-lookup"><span data-stu-id="e36c5-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="e36c5-141">**Java 클라이언트**</span><span class="sxs-lookup"><span data-stu-id="e36c5-141">**Java client**</span></span> | <span data-ttu-id="e36c5-142">[GitHub 리포지토리](https://github.com/SignalR/java-client) (사용 되지 않음)</span><span class="sxs-lookup"><span data-stu-id="e36c5-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="e36c5-143">Maven package [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="e36c5-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="e36c5-144">**서버 앱 유형**</span><span class="sxs-lookup"><span data-stu-id="e36c5-144">**Server app type**</span></span> | <span data-ttu-id="e36c5-145">ASP.NET (System.web) 또는 OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="e36c5-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="e36c5-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e36c5-146">ASP.NET Core</span></span> |
| <span data-ttu-id="e36c5-147">**지원 되는 서버 플랫폼**</span><span class="sxs-lookup"><span data-stu-id="e36c5-147">**Supported server platforms**</span></span> | <span data-ttu-id="e36c5-148">.NET Framework 4.5 이상</span><span class="sxs-lookup"><span data-stu-id="e36c5-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="e36c5-149">.NET Framework 4.6.1 이상</span><span class="sxs-lookup"><span data-stu-id="e36c5-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="e36c5-150">.NET Core 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="e36c5-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="e36c5-151">기능 차이점</span><span class="sxs-lookup"><span data-stu-id="e36c5-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="e36c5-152">자동 다시 연결</span><span class="sxs-lookup"><span data-stu-id="e36c5-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e36c5-153">ASP.NET SignalR :</span><span class="sxs-lookup"><span data-stu-id="e36c5-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="e36c5-154">기본적으로는 SignalR 연결을 끊을 때 서버에 다시 연결을 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="e36c5-155">ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="e36c5-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="e36c5-156">자동 다시 연결은 [.net 클라이언트](xref:signalr/dotnet-client#automatically-reconnect) 와 [JavaScript 클라이언트](xref:signalr/javascript-client#automatically-reconnect)모두에서 옵트인 (opt in) 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e36c5-157">ASP.NET Core 3.0 이전에는가 자동 다시 연결을 SignalR 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="e36c5-158">클라이언트의 연결이 끊어지면 사용자는 새 연결을 명시적으로 시작 하 여 다시 연결 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="e36c5-159">ASP.NET에서 SignalR SignalR 연결을 삭제 하면 서버에 다시 연결을 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="e36c5-160">프로토콜 지원</span><span class="sxs-lookup"><span data-stu-id="e36c5-160">Protocol support</span></span>

<span data-ttu-id="e36c5-161">ASP.NET Core는 SignalR JSON을 지원 하 고 [MessagePack](xref:signalr/messagepackhubprotocol)을 기반으로 하는 새 이진 프로토콜도 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="e36c5-162">또한 사용자 지정 프로토콜을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="e36c5-163">전송</span><span class="sxs-lookup"><span data-stu-id="e36c5-163">Transports</span></span>

<span data-ttu-id="e36c5-164">ASP.NET Core에서 무한 프레임 전송은 지원 되지 않습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="e36c5-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="e36c5-165">서버의 차이점</span><span class="sxs-lookup"><span data-stu-id="e36c5-165">Differences on the server</span></span>

<span data-ttu-id="e36c5-166">ASP.NET Core SignalR 서버 쪽 라이브러리는 및 MVC 프로젝트의 **ASP.NET Core 웹 응용 프로그램** 템플릿에 사용 되는 [AspNetCore](xref:fundamentals/metapackage-app)에 포함 되어 있습니다. Razor</span><span class="sxs-lookup"><span data-stu-id="e36c5-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="e36c5-167">ASP.NET Core SignalR 은 ASP.NET Core 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="e36c5-168">에서를 호출 하 여 구성 해야 합니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e36c5-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e36c5-169">라우팅을 구성 하려면 메서드의 메서드 호출 내에서 허브에 경로를 매핑합니다 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="e36c5-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e36c5-170">라우팅을 구성 하려면 메서드의 메서드 호출 내에서 허브에 경로를 매핑합니다 <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="e36c5-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="e36c5-171">고정 세션</span><span class="sxs-lookup"><span data-stu-id="e36c5-171">Sticky sessions</span></span>

<span data-ttu-id="e36c5-172">ASP.NET에 대 한 확장 모델을 SignalR 사용 하면 클라이언트가 팜의 모든 서버에 다시 연결 하 여 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="e36c5-173">ASP.NET Core SignalR 클라이언트는 연결 기간 동안 동일한 서버와 상호 작용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="e36c5-174">Redis를 사용 하는 확장의 경우에는 고정 세션이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="e36c5-175">[Azure SignalR 서비스](/azure/azure-signalr/)를 사용 하는 확장의 경우 서비스에서 클라이언트에 대 한 연결을 처리 하기 때문에 고정 세션이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="e36c5-176">연결당 단일 허브</span><span class="sxs-lookup"><span data-stu-id="e36c5-176">Single hub per connection</span></span>

<span data-ttu-id="e36c5-177">ASP.NET Core에서는 SignalR 연결 모델이 간소화 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="e36c5-178">여러 허브에 대 한 액세스를 공유 하는 데 사용 되는 단일 연결이 아닌 단일 허브로 직접 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="e36c5-179">스트리밍</span><span class="sxs-lookup"><span data-stu-id="e36c5-179">Streaming</span></span>

<span data-ttu-id="e36c5-180">ASP.NET Core는 SignalR 이제 허브에서 클라이언트로의 [스트리밍 데이터](xref:signalr/streaming) 를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="e36c5-181">시스템 상태</span><span class="sxs-lookup"><span data-stu-id="e36c5-181">State</span></span>

<span data-ttu-id="e36c5-182">클라이언트와 허브 (라고도 함) 간에 임의의 상태를 전달 하는 기능이 `HubState` 제거 되었으며 진행 메시지를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="e36c5-183">현재 허브 프록시의 대응 항목이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="e36c5-184">PersistentConnection 제거</span><span class="sxs-lookup"><span data-stu-id="e36c5-184">PersistentConnection removal</span></span>

<span data-ttu-id="e36c5-185">ASP.NET Core SignalR [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) 클래스가 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-185">In ASP.NET Core SignalR, the [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="e36c5-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="e36c5-186">GlobalHost</span></span>

<span data-ttu-id="e36c5-187">ASP.NET Core에는 프레임 워크에 기본 제공 되는 DI (종속성 주입)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="e36c5-188">서비스는 DI를 사용 하 여 [HubContext](xref:signalr/hubcontext)에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="e36c5-189">ASP.NET에를 `GlobalHost` 가져오는 데 사용 되는 개체가 SignalR `HubContext` ASP.NET Core에 없는 SignalR 경우</span><span class="sxs-lookup"><span data-stu-id="e36c5-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="e36c5-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="e36c5-190">HubPipeline</span></span>

<span data-ttu-id="e36c5-191">ASP.NET Core SignalR 모듈을 지원 하지 않습니다 `HubPipeline` .</span><span class="sxs-lookup"><span data-stu-id="e36c5-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="e36c5-192">클라이언트의 차이점</span><span class="sxs-lookup"><span data-stu-id="e36c5-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="e36c5-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="e36c5-193">TypeScript</span></span>

<span data-ttu-id="e36c5-194">ASP.NET Core SignalR 클라이언트는 [TypeScript](https://www.typescriptlang.org/)로 작성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="e36c5-195">[Javascript 클라이언트](xref:signalr/javascript-client)를 사용 하는 경우 Javascript 또는 TypeScript로 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="e36c5-196">JavaScript 클라이언트는 npm에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e36c5-197">ASP.NET 버전에서는 Visual Studio의 NuGet 패키지를 통해 JavaScript 클라이언트를 가져왔습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="e36c5-198">ASP.NET Core 버전에서 [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm 패키지는 JavaScript 라이브러리를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="e36c5-199">이 패키지는 **ASP.NET Core 웹 응용 프로그램** 템플릿에 포함 되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="e36c5-200">Npm를 사용 하 여 npm 패키지를 가져오고 설치 `@microsoft/signalr` 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e36c5-201">ASP.NET 버전에서는 Visual Studio의 NuGet 패키지를 통해 JavaScript 클라이언트를 가져왔습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="e36c5-202">ASP.NET Core 버전에서 [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm 패키지는 JavaScript 라이브러리를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="e36c5-203">이 패키지는 **ASP.NET Core 웹 응용 프로그램** 템플릿에 포함 되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="e36c5-204">Npm를 사용 하 여 npm 패키지를 가져오고 설치 `@aspnet/signalr` 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="e36c5-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="e36c5-205">jQuery</span></span>

<span data-ttu-id="e36c5-206">JQuery에 대 한 종속성이 제거 되었지만 프로젝트에서 jQuery를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="e36c5-207">Internet Explorer 지원</span><span class="sxs-lookup"><span data-stu-id="e36c5-207">Internet Explorer support</span></span>

<span data-ttu-id="e36c5-208">ASP.NET Core SignalR 는 Microsoft internet explorer 11 이상을 지원 하지만 ASP.NET는 SignalR Microsoft internet explorer 8 이상을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-208">ASP.NET Core SignalR supports Microsoft Internet Explorer 11 or later, whereas ASP.NET SignalR supports Microsoft Internet Explorer 8 or later.</span></span>
<span data-ttu-id="e36c5-209">브라우저 지원에 대 한 자세한 내용은 [지원 되는 플랫폼](xref:signalr/supported-platforms#javascript-client)에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-209">More info on browser support can be found at [supported platforms](xref:signalr/supported-platforms#javascript-client).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="e36c5-210">JavaScript 클라이언트 메서드 구문</span><span class="sxs-lookup"><span data-stu-id="e36c5-210">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e36c5-211">JavaScript 구문이 ASP.NET 버전의에서 변경 되었습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="e36c5-211">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="e36c5-212">개체를 사용 하는 대신 `$connection` [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API를 사용 하 여 연결을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-212">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="e36c5-213">[On](/javascript/api/@microsoft/signalr/HubConnection#on) 메서드를 사용 하 여 허브에서 호출할 수 있는 클라이언트 메서드를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-213">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e36c5-214">JavaScript 구문이 ASP.NET 버전의에서 변경 되었습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="e36c5-214">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="e36c5-215">개체를 사용 하는 대신 `$connection` [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API를 사용 하 여 연결을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-215">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="e36c5-216">[On](/javascript/api/@aspnet/signalr/HubConnection#on) 메서드를 사용 하 여 허브에서 호출할 수 있는 클라이언트 메서드를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-216">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="e36c5-217">클라이언트 메서드를 만든 후 허브 연결을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-217">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="e36c5-218">[Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 메서드를 연결 하 여 오류를 기록 하거나 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-218">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="e36c5-219">허브 프록시</span><span class="sxs-lookup"><span data-stu-id="e36c5-219">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e36c5-220">허브 프록시는 더 이상 자동으로 생성 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-220">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="e36c5-221">대신 메서드 이름이 [호출](/javascript/api/@microsoft/signalr/hubconnection#invoke) API에 문자열로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-221">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e36c5-222">허브 프록시는 더 이상 자동으로 생성 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-222">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="e36c5-223">대신 메서드 이름이 [호출](/javascript/api/@aspnet/signalr/hubconnection#invoke) API에 문자열로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-223">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="e36c5-224">.NET 및 기타 클라이언트</span><span class="sxs-lookup"><span data-stu-id="e36c5-224">.NET and other clients</span></span>

<span data-ttu-id="e36c5-225">[AspNetCore. SignalR 클라이언트](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet 패키지에는 ASP.NET Core에 대 한 .net 클라이언트 라이브러리가 포함 되어 있습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="e36c5-225">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="e36c5-226">을 사용 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 하 여 허브에 대 한 연결의 인스턴스를 만들고 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-226">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="e36c5-227">확장 차이점</span><span class="sxs-lookup"><span data-stu-id="e36c5-227">Scaleout differences</span></span>

<span data-ttu-id="e36c5-228">ASP.NET는 SignalR SQL Server 및 Redis를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-228">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="e36c5-229">ASP.NET Core는 SignalR Azure SignalR 서비스 및 Redis을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="e36c5-229">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="e36c5-230">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e36c5-230">ASP.NET</span></span>

* [<span data-ttu-id="e36c5-231">SignalR Azure Service Bus 확장</span><span class="sxs-lookup"><span data-stu-id="e36c5-231">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="e36c5-232">SignalR Redis를 사용 하는 확장</span><span class="sxs-lookup"><span data-stu-id="e36c5-232">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="e36c5-233">SignalR SQL Server 확장</span><span class="sxs-lookup"><span data-stu-id="e36c5-233">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="e36c5-234">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e36c5-234">ASP.NET Core</span></span>

* [<span data-ttu-id="e36c5-235">Azure SignalR 서비스</span><span class="sxs-lookup"><span data-stu-id="e36c5-235">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="e36c5-236">Redis 백플레인</span><span class="sxs-lookup"><span data-stu-id="e36c5-236">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="e36c5-237">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e36c5-237">Additional resources</span></span>

* [<span data-ttu-id="e36c5-238">허브</span><span class="sxs-lookup"><span data-stu-id="e36c5-238">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="e36c5-239">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="e36c5-239">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="e36c5-240">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="e36c5-240">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="e36c5-241">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="e36c5-241">Supported platforms</span></span>](xref:signalr/supported-platforms)
