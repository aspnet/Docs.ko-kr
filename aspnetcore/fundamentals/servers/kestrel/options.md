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
ms.openlocfilehash: 48b4af2dfc925c4444c2bd0e43d04f2f0f3ddd17
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587010"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="fc294-103">ASP.NET Core Kestrel 웹 서버의 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="fc294-103">Configure options for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="fc294-104">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-104">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="fc294-105"><xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A>를 호출한 후 추가 구성을 제공하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.ConfigureKestrel%2A>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-105">To provide additional configuration after calling <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A>, use <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.ConfigureKestrel%2A>:</span></span>

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

<span data-ttu-id="fc294-106"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-106">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="fc294-107">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-107">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="fc294-108">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-108">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="fc294-109">이 문서의 뒷부분에 나오는 예제에서 Kestrel 옵션은 C# 코드로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-109">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="fc294-110">Kestrel 옵션은 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-110">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="fc294-111">예를 들어 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider)는 `appsettings.json` 또는 `appsettings.{Environment}.json` 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-111">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an `appsettings.json` or `appsettings.{Environment}.json` file:</span></span>

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
> <span data-ttu-id="fc294-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 및 [엔드포인트 구성](xref:fundamentals/servers/kestrel/endpoints)은 구성 공급자에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) are configurable from configuration providers.</span></span> <span data-ttu-id="fc294-113">나머지 Kestrel 구성은 C# 코드로 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-113">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="fc294-114">다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-114">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="fc294-115">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-115">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="fc294-116">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-116">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="fc294-117">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-117">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="fc294-118">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-118">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="fc294-119">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-119">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="fc294-120">`Program.cs`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-120">In `Program.cs`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="fc294-121">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-121">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

## <a name="general-limits"></a><span data-ttu-id="fc294-122">일반적인 제한</span><span class="sxs-lookup"><span data-stu-id="fc294-122">General limits</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="fc294-123">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="fc294-123">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="fc294-124">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-124">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="fc294-125">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-125">Defaults to 2 minutes.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="fc294-126">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="fc294-126">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections><br>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="fc294-127">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-127">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="fc294-128">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-128">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="fc294-129">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-129">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="fc294-130">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-130">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="fc294-131">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="fc294-131">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="fc294-132">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-132">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="fc294-133">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-133">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="fc294-134">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-134">The following example shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="fc294-135">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-135">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="fc294-136">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-136">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="fc294-137">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-137">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="fc294-138">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우 Kestrel의 요청 본문 크기 제한이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-138">When an app runs [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled.</span></span> <span data-ttu-id="fc294-139">IIS는 이미 제한을 설정했습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-139">IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="fc294-140">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="fc294-140">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate><br>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="fc294-141">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-141">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="fc294-142">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 클라이언트가 전송 속도를 최소로 높일 수 있는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-142">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time Kestrel allows the client to increase its send rate up to the minimum.</span></span> <span data-ttu-id="fc294-143">이 기간에는 속도가 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-143">The rate isn't checked during that time.</span></span> <span data-ttu-id="fc294-144">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-144">The grace period helps avoid dropping connections that are initially sending data at a slow rate because of TCP slow-start.</span></span>

<span data-ttu-id="fc294-145">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-145">The default minimum rate is 240 bytes/second with a 5-second grace period.</span></span>

<span data-ttu-id="fc294-146">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-146">A minimum rate also applies to the response.</span></span> <span data-ttu-id="fc294-147">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-147">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="fc294-148">`Program.cs`에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-148">Here's an example that shows how to configure the minimum data rates in `Program.cs`:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="fc294-149">미들웨어에서 요청당 최소 속도 제한을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-149">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="fc294-150">이전 샘플에서 참조하는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>는 HTTP/2 요청의 <xref:Microsoft.AspNetCore.Http.HttpContext.Features?displayProperty=nameWithType>에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-150">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample isn't present in <xref:Microsoft.AspNetCore.Http.HttpContext.Features?displayProperty=nameWithType> for HTTP/2 requests.</span></span> <span data-ttu-id="fc294-151">프로토콜의 요청 멀티플렉싱 지원 때문에 HTTP/2의 경우 일반적으로 각 요청을 기준으로 속도 제한을 수정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-151">Modifying rate limits on a per-request basis isn't generally supported for HTTP/2 because of the protocol's support for request multiplexing.</span></span> <span data-ttu-id="fc294-152">그러나 HTTP/2 요청에 대해서도 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature.MinDataRate?displayProperty=nameWithType>를 `null`로 설정하여 요청별 읽기 속도 제한을 *완전히 비활성화* 할 수 있으므로 HTTP/2 요청에 대한 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>는 여전히 존재합니다`HttpContext.Features`.</span><span class="sxs-lookup"><span data-stu-id="fc294-152">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature.MinDataRate?displayProperty=nameWithType> to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="fc294-153">`IHttpMinRequestBodyDataRateFeature.MinDataRate` 읽기를 시도하거나 `null`이 아닌 값으로 설정하려고 하면 HTTP/2 요청이 있을 때 <xref:System.NotSupportedException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-153">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a <xref:System.NotSupportedException> being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="fc294-154"><xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.Limits?displayProperty=nameWithType>를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-154">Server-wide rate limits configured via <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.Limits?displayProperty=nameWithType> still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="fc294-155">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="fc294-155">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="fc294-156">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-156">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="fc294-157">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-157">Defaults to 30 seconds.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a><span data-ttu-id="fc294-158">HTTP/2 제한</span><span class="sxs-lookup"><span data-stu-id="fc294-158">HTTP/2 limits</span></span>

<span data-ttu-id="fc294-159">이 섹션의 제한은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.Http2?displayProperty=nameWithType>에 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-159">The limits in this section are set on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.Http2?displayProperty=nameWithType>.</span></span>

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="fc294-160">연결당 최대 스트림</span><span class="sxs-lookup"><span data-stu-id="fc294-160">Maximum streams per connection</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>

<span data-ttu-id="fc294-161">HTTP/2 연결당 동시 요청 스트림 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-161">Limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="fc294-162">초과 스트림은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-162">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="fc294-163">기본값은 100입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-163">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="fc294-164">헤더 테이블 크기</span><span class="sxs-lookup"><span data-stu-id="fc294-164">Header table size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.HeaderTableSize>

<span data-ttu-id="fc294-165">HPACK 디코더는 HTTP/2 연결에 대한 HTTP 헤더의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-165">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="fc294-166">`HeaderTableSize`는 HPACK 디코더가 사용하는 헤더 압축 테이블의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-166">`HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="fc294-167">값은 8진수로 제공되며 영(0)보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-167">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="fc294-168">기본값은 4096입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-168">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="fc294-169">최대 프레임 크기</span><span class="sxs-lookup"><span data-stu-id="fc294-169">Maximum frame size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxFrameSize>

<span data-ttu-id="fc294-170">서버에서 받거나 보낸 HTTP/2 연결 프레임 페이로드의 최대 허용 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-170">Indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="fc294-171">값은 8진수로 제공되며 2^14(16,384)와 2^24-1(16,777,215) 사이여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-171">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="fc294-172">기본값은 2^14(16,384)입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-172">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="fc294-173">최대 요청 헤더 크기</span><span class="sxs-lookup"><span data-stu-id="fc294-173">Maximum request header size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxRequestHeaderFieldSize>

<span data-ttu-id="fc294-174">요청 헤더 값의 8진수로 허용되는 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-174">Indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="fc294-175">이 한도는 모두 압축된 표현과 압축되지 않은 표현으로 이름과 값 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-175">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="fc294-176">값은 0보다 커야 합니다(0).</span><span class="sxs-lookup"><span data-stu-id="fc294-176">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="fc294-177">기본값은 8,192입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-177">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="fc294-178">초기 연결 창 크기</span><span class="sxs-lookup"><span data-stu-id="fc294-178">Initial connection window size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.InitialConnectionWindowSize>

<span data-ttu-id="fc294-179">연결당 모든 요청(스트림)을 통해 한 번에 집계하는 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-179">Indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="fc294-180">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-180">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="fc294-181">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-181">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="fc294-182">기본값은 128KB(131,072)입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-182">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="fc294-183">초기 스트림 창 크기</span><span class="sxs-lookup"><span data-stu-id="fc294-183">Initial stream window size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.InitialStreamWindowSize>

<span data-ttu-id="fc294-184">요청(스트림)당 한 번에 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-184">Indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="fc294-185">요청은 [`InitialConnectionWindowSize`](#initial-connection-window-size)에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-185">Requests are also limited by [`InitialConnectionWindowSize`](#initial-connection-window-size).</span></span> <span data-ttu-id="fc294-186">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-186">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="fc294-187">기본값은 96KB(98,304)입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-187">The default value is 96 KB (98,304).</span></span>

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="fc294-188">HTTP/2 연결 유지 ping 구성</span><span class="sxs-lookup"><span data-stu-id="fc294-188">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="fc294-189">연결된 클라이언트로 HTTP/2 ping을 보내도록 Kestrel을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-189">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="fc294-190">HTTP/2 ping은 여러 가지 용도로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-190">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="fc294-191">유휴 연결을 활성 상태로 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-191">Keep idle connections alive.</span></span> <span data-ttu-id="fc294-192">일부 클라이언트 및 프록시 서버는 유휴 상태인 연결을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-192">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="fc294-193">HTTP/2 ping은 연결에 대한 작업으로 간주하며 연결이 유휴 상태로 닫히지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-193">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="fc294-194">비정상 연결을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-194">Close unhealthy connections.</span></span> <span data-ttu-id="fc294-195">클라이언트가 구성된 시간에 연결 유지 ping에 응답하지 않는 연결은 서버에서 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-195">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="fc294-196">HTTP/2 연결 유지 ping과 관련된 두 가지 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-196">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="fc294-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingDelay>은 ping 간격을 구성하는 <xref:System.TimeSpan>입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingDelay> is a <xref:System.TimeSpan> that configures the ping interval.</span></span> <span data-ttu-id="fc294-198">서버가 이 기간 동안 프레임을 받지 못하면 클라이언트로 연결 유지 ping을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-198">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="fc294-199">이 옵션을 <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>로 설정하면 연결 유지 ping을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-199">Keep alive pings are disabled when this option is set to <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span></span> <span data-ttu-id="fc294-200">기본값은 <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-200">The default value is <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="fc294-201"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingTimeout>은 ping 시간 제한을 구성하는 <xref:System.TimeSpan>입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-201"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingTimeout> is a <xref:System.TimeSpan> that configures the ping timeout.</span></span> <span data-ttu-id="fc294-202">서버가 이 시간 제한 동안 응답 ping과 같은 프레임을 받지 못하면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-202">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="fc294-203">이 옵션을 <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>로 설정하면 연결 유지 시간 제한을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-203">Keep alive timeout is disabled when this option is set to <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span></span> <span data-ttu-id="fc294-204">기본값은 20초입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-204">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingDelay = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a><span data-ttu-id="fc294-205">기타 옵션</span><span class="sxs-lookup"><span data-stu-id="fc294-205">Other options</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="fc294-206">동기 I/O</span><span class="sxs-lookup"><span data-stu-id="fc294-206">Synchronous I/O</span></span>

<span data-ttu-id="fc294-207"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 I/O가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-207"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="fc294-208">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-208">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="fc294-209">차단 동기 I/O 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-209">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="fc294-210">비동기 I/O를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 사용하도록 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="fc294-210">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="fc294-211">다음 예제에서는 동기 I/O를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc294-211">The following example enables synchronous I/O:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="fc294-212">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc294-212">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
