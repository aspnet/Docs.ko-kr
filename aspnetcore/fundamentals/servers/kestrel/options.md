---
title: ASP.NET Core Kestrel 웹 서버의 옵션 구성
author: rick-anderson
description: ASP.NET Core의 플랫폼 간 웹 서버인 Kestrel의 옵션을 구성하는 방법에 관해 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
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
uid: fundamentals/servers/kestrel/options
ms.openlocfilehash: 198d509a68224077d3764cc836121b89e96c6853
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253870"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="4f576-103">ASP.NET Core Kestrel 웹 서버의 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="4f576-103">Configure options for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="4f576-104">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-104">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="4f576-105">`ConfigureWebHostDefaults`를 호출한 후 추가 구성을 제공하려면 `ConfigureKestrel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-105">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

<span data-ttu-id="4f576-106"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-106">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="4f576-107">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-107">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="4f576-108">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-108">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="4f576-109">이 문서의 뒷부분에 나오는 예제에서 Kestrel 옵션은 C# 코드로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-109">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="4f576-110">Kestrel 옵션은 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-110">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="4f576-111">예를 들어 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider)는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-111">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="4f576-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 및 [엔드포인트 구성](xref:fundamentals/servers/kestrel/endpoints)은 구성 공급자에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) are configurable from configuration providers.</span></span> <span data-ttu-id="4f576-113">나머지 Kestrel 구성은 C# 코드로 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-113">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="4f576-114">다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-114">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="4f576-115">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-115">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="4f576-116">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-116">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="4f576-117">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-117">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="4f576-118">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-118">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="4f576-119">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-119">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="4f576-120">*Program.cs* 에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-120">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="4f576-121">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-121">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

## <a name="general-limits"></a><span data-ttu-id="4f576-122">일반적인 제한</span><span class="sxs-lookup"><span data-stu-id="4f576-122">General limits</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="4f576-123">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="4f576-123">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="4f576-124">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-124">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="4f576-125">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-125">Defaults to 2 minutes.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="4f576-126">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="4f576-126">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="4f576-127">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-127">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="4f576-128">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-128">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="4f576-129">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-129">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="4f576-130">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-130">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="4f576-131">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="4f576-131">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="4f576-132">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-132">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="4f576-133">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-133">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="4f576-134">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-134">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="4f576-135">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-135">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="4f576-136">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-136">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="4f576-137">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-137">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="4f576-138">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우 Kestrel의 요청 본문 크기 제한이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-138">When an app runs [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled.</span></span> <span data-ttu-id="4f576-139">IIS는 이미 제한을 설정했습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-139">IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="4f576-140">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="4f576-140">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="4f576-141">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-141">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="4f576-142">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 클라이언트가 전송 속도를 최소로 높일 수 있는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-142">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time Kestrel allows the client to increase its send rate up to the minimum.</span></span> <span data-ttu-id="4f576-143">이 기간에는 속도가 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-143">The rate isn't checked during that time.</span></span> <span data-ttu-id="4f576-144">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-144">The grace period helps avoid dropping connections that are initially sending data at a slow rate because of TCP slow-start.</span></span>

<span data-ttu-id="4f576-145">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-145">The default minimum rate is 240 bytes/second with a 5-second grace period.</span></span>

<span data-ttu-id="4f576-146">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-146">A minimum rate also applies to the response.</span></span> <span data-ttu-id="4f576-147">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-147">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="4f576-148">*Program.cs* 에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-148">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="4f576-149">미들웨어에서 요청당 최소 속도 제한을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-149">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="4f576-150">이전 샘플에서 참조하는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>는 HTTP/2 요청의 `HttpContext.Features`에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-150">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample isn't present in `HttpContext.Features` for HTTP/2 requests.</span></span> <span data-ttu-id="4f576-151">프로토콜의 요청 멀티플렉싱 지원 때문에 HTTP/2의 경우 일반적으로 각 요청을 기준으로 속도 제한을 수정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-151">Modifying rate limits on a per-request basis is generally not supported for HTTP/2 because of the protocol's support for request multiplexing.</span></span> <span data-ttu-id="4f576-152">그러나 HTTP/2 요청에 대해서도 `IHttpMinRequestBodyDataRateFeature.MinDataRate`를 `null`로 설정하여 요청별 읽기 속도 제한을 *완전히 비활성화* 할 수 있으므로 HTTP/2 요청에 대한 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>는 여전히 존재합니다`HttpContext.Features`.</span><span class="sxs-lookup"><span data-stu-id="4f576-152">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="4f576-153">`IHttpMinRequestBodyDataRateFeature.MinDataRate` 읽기를 시도하거나 `null`이 아닌 값으로 설정하려고 하면 HTTP/2 요청이 있을 때 `NotSupportedException`이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-153">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="4f576-154">`KestrelServerOptions.Limits`를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-154">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="4f576-155">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="4f576-155">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="4f576-156">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-156">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="4f576-157">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-157">Defaults to 30 seconds.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a><span data-ttu-id="4f576-158">HTTP/2 제한</span><span class="sxs-lookup"><span data-stu-id="4f576-158">HTTP/2 limits</span></span>

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="4f576-159">연결당 최대 스트림</span><span class="sxs-lookup"><span data-stu-id="4f576-159">Maximum streams per connection</span></span>

<span data-ttu-id="4f576-160">`Http2.MaxStreamsPerConnection`은 HTTP/2 연결당 동시 요청 스트림 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-160">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="4f576-161">초과 스트림은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-161">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="4f576-162">기본값은 100입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-162">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="4f576-163">헤더 테이블 크기</span><span class="sxs-lookup"><span data-stu-id="4f576-163">Header table size</span></span>

<span data-ttu-id="4f576-164">HPACK 디코더는 HTTP/2 연결에 대한 HTTP 헤더의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-164">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="4f576-165">`Http2.HeaderTableSize`는 HPACK 디코더가 사용하는 헤더 압축 테이블의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-165">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="4f576-166">값은 8진수로 제공되며 영(0)보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-166">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="4f576-167">기본값은 4096입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-167">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="4f576-168">최대 프레임 크기</span><span class="sxs-lookup"><span data-stu-id="4f576-168">Maximum frame size</span></span>

<span data-ttu-id="4f576-169">`Http2.MaxFrameSize`는 서버에서 받거나 보낸 HTTP/2 연결 프레임 페이로드의 최대 허용 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-169">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="4f576-170">값은 8진수로 제공되며 2^14(16,384)와 2^24-1(16,777,215) 사이여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-170">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="4f576-171">기본값은 2^14(16,384)입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-171">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="4f576-172">최대 요청 헤더 크기</span><span class="sxs-lookup"><span data-stu-id="4f576-172">Maximum request header size</span></span>

<span data-ttu-id="4f576-173">`Http2.MaxRequestHeaderFieldSize`는 요청 헤더 값의 8진수로 허용되는 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-173">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="4f576-174">이 한도는 모두 압축된 표현과 압축되지 않은 표현으로 이름과 값 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-174">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="4f576-175">값은 0보다 커야 합니다(0).</span><span class="sxs-lookup"><span data-stu-id="4f576-175">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="4f576-176">기본값은 8,192입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-176">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="4f576-177">초기 연결 창 크기</span><span class="sxs-lookup"><span data-stu-id="4f576-177">Initial connection window size</span></span>

<span data-ttu-id="4f576-178">`Http2.InitialConnectionWindowSize`는 연결당 모든 요청(스트림)을 통해 한 번에 집계하는 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-178">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="4f576-179">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-179">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="4f576-180">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-180">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="4f576-181">기본값은 128KB(131,072)입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-181">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="4f576-182">초기 스트림 창 크기</span><span class="sxs-lookup"><span data-stu-id="4f576-182">Initial stream window size</span></span>

<span data-ttu-id="4f576-183">`Http2.InitialStreamWindowSize`는 요청(스트림)당 한 번에 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-183">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="4f576-184">요청은 `Http2.InitialConnectionWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-184">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="4f576-185">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-185">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="4f576-186">기본값은 96KB(98,304)입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-186">The default value is 96 KB (98,304).</span></span>

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="4f576-187">HTTP/2 연결 유지 ping 구성</span><span class="sxs-lookup"><span data-stu-id="4f576-187">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="4f576-188">연결된 클라이언트로 HTTP/2 ping을 보내도록 Kestrel을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-188">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="4f576-189">HTTP/2 ping은 여러 가지 용도로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-189">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="4f576-190">유휴 연결을 활성 상태로 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-190">Keep idle connections alive.</span></span> <span data-ttu-id="4f576-191">일부 클라이언트 및 프록시 서버는 유휴 상태인 연결을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-191">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="4f576-192">HTTP/2 ping은 연결에 대한 작업으로 간주하며 연결이 유휴 상태로 닫히지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-192">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="4f576-193">비정상 연결을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-193">Close unhealthy connections.</span></span> <span data-ttu-id="4f576-194">클라이언트가 구성된 시간에 연결 유지 ping에 응답하지 않는 연결은 서버에서 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-194">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="4f576-195">HTTP/2 연결 유지 ping과 관련된 두 가지 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-195">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="4f576-196">`Http2.KeepAlivePingInterval`은 ping 간격을 구성하는 `TimeSpan`입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-196">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping interval.</span></span> <span data-ttu-id="4f576-197">서버가 이 기간 동안 프레임을 받지 못하면 클라이언트로 연결 유지 ping을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-197">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="4f576-198">이 옵션을 `TimeSpan.MaxValue`로 설정하면 연결 유지 ping을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-198">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="4f576-199">기본값은 `TimeSpan.MaxValue`입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-199">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="4f576-200">`Http2.KeepAlivePingTimeout`은 ping 시간 제한을 구성하는 `TimeSpan`입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-200">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="4f576-201">서버가 이 시간 제한 동안 응답 ping과 같은 프레임을 받지 못하면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-201">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="4f576-202">이 옵션을 `TimeSpan.MaxValue`로 설정하면 연결 유지 시간 제한을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-202">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="4f576-203">기본값은 20초입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-203">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a><span data-ttu-id="4f576-204">기타 옵션</span><span class="sxs-lookup"><span data-stu-id="4f576-204">Other options</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="4f576-205">동기 I/O</span><span class="sxs-lookup"><span data-stu-id="4f576-205">Synchronous I/O</span></span>

<span data-ttu-id="4f576-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 I/O가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="4f576-207">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-207">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="4f576-208">차단 동기 I/O 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-208">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="4f576-209">비동기 I/O를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 사용하도록 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="4f576-209">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="4f576-210">다음 예제에서는 동기 I/O를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4f576-210">The following example enables synchronous I/O:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="4f576-211">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4f576-211">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
