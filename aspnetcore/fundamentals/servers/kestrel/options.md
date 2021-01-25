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
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a>ASP.NET Core Kestrel 웹 서버의 옵션 구성

Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.

`ConfigureWebHostDefaults`를 호출한 후 추가 구성을 제공하려면 `ConfigureKestrel`을 사용합니다.

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

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다. `Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.

다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

이 문서의 뒷부분에 나오는 예제에서 Kestrel 옵션은 C# 코드로 구성됩니다. Kestrel 옵션은 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수도 있습니다. 예를 들어 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider)는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 및 [엔드포인트 구성](xref:fundamentals/servers/kestrel/endpoints)은 구성 공급자에서 구성할 수 있습니다. 나머지 Kestrel 구성은 C# 코드로 구성해야 합니다.

다음 방법 중 **하나** 를 사용합니다.

* `Startup.ConfigureServices`에서 Kestrel을 구성합니다.

  1. `IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다. 다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.
  2. `Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.

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

* 호스트를 빌드할 때 Kestrel을 구성합니다.

  *Program.cs* 에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.

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

위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.

## <a name="general-limits"></a>일반적인 제한

### <a name="keep-alive-timeout"></a>Keep-alive 시간 제한

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다. 기본값은 2분입니다.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>최대 클라이언트 연결

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다. 연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

연결의 최대 수는 기본적으로 무제한(null)입니다.

### <a name="maximum-request-body-size"></a>최대 요청 본문 크기

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.

ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

미들웨어에서 특정 요청에 대한 설정을 재정의합니다.

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다. `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.

앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우 Kestrel의 요청 본문 크기 제한이 사용되지 않습니다. IIS는 이미 제한을 설정했습니다.

### <a name="minimum-request-body-data-rate"></a>최소 요청 본문 데이터 속도

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다. 속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 클라이언트가 전송 속도를 최소로 높일 수 있는 시간입니다. 이 기간에는 속도가 확인되지 않습니다. 유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.

기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.

최소 속도는 응답에도 적용됩니다. 요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.

*Program.cs* 에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

미들웨어에서 요청당 최소 속도 제한을 재정의합니다.

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

이전 샘플에서 참조하는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>는 HTTP/2 요청의 `HttpContext.Features`에 없습니다. 프로토콜의 요청 멀티플렉싱 지원 때문에 HTTP/2의 경우 일반적으로 각 요청을 기준으로 속도 제한을 수정할 수 없습니다. 그러나 HTTP/2 요청에 대해서도 `IHttpMinRequestBodyDataRateFeature.MinDataRate`를 `null`로 설정하여 요청별 읽기 속도 제한을 *완전히 비활성화* 할 수 있으므로 HTTP/2 요청에 대한 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>는 여전히 존재합니다`HttpContext.Features`. `IHttpMinRequestBodyDataRateFeature.MinDataRate` 읽기를 시도하거나 `null`이 아닌 값으로 설정하려고 하면 HTTP/2 요청이 있을 때 `NotSupportedException`이 throw됩니다.

`KestrelServerOptions.Limits`를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.

### <a name="request-headers-timeout"></a>요청 헤더 시간 제한

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다. 기본값은 30초입니다.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a>HTTP/2 제한

### <a name="maximum-streams-per-connection"></a>연결당 최대 스트림

`Http2.MaxStreamsPerConnection`은 HTTP/2 연결당 동시 요청 스트림 수를 제한합니다. 초과 스트림은 거부됩니다.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

기본값은 100입니다.

### <a name="header-table-size"></a>헤더 테이블 크기

HPACK 디코더는 HTTP/2 연결에 대한 HTTP 헤더의 압축을 풉니다. `Http2.HeaderTableSize`는 HPACK 디코더가 사용하는 헤더 압축 테이블의 크기를 제한합니다. 값은 8진수로 제공되며 영(0)보다 커야 합니다.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

기본값은 4096입니다.

### <a name="maximum-frame-size"></a>최대 프레임 크기

`Http2.MaxFrameSize`는 서버에서 받거나 보낸 HTTP/2 연결 프레임 페이로드의 최대 허용 크기를 나타냅니다. 값은 8진수로 제공되며 2^14(16,384)와 2^24-1(16,777,215) 사이여야 합니다.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

기본값은 2^14(16,384)입니다.

### <a name="maximum-request-header-size"></a>최대 요청 헤더 크기

`Http2.MaxRequestHeaderFieldSize`는 요청 헤더 값의 8진수로 허용되는 최대 크기를 나타냅니다. 이 한도는 모두 압축된 표현과 압축되지 않은 표현으로 이름과 값 모두에 적용됩니다. 값은 0보다 커야 합니다(0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

기본값은 8,192입니다.

### <a name="initial-connection-window-size"></a>초기 연결 창 크기

`Http2.InitialConnectionWindowSize`는 연결당 모든 요청(스트림)을 통해 한 번에 집계하는 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다. 요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다. 값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

기본값은 128KB(131,072)입니다.

### <a name="initial-stream-window-size"></a>초기 스트림 창 크기

`Http2.InitialStreamWindowSize`는 요청(스트림)당 한 번에 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다. 요청은 `Http2.InitialConnectionWindowSize`에 의해서도 제한됩니다. 값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

기본값은 96KB(98,304)입니다.

### <a name="http2-keep-alive-ping-configuration"></a>HTTP/2 연결 유지 ping 구성

연결된 클라이언트로 HTTP/2 ping을 보내도록 Kestrel을 구성할 수 있습니다. HTTP/2 ping은 여러 가지 용도로 사용됩니다.

* 유휴 연결을 활성 상태로 유지합니다. 일부 클라이언트 및 프록시 서버는 유휴 상태인 연결을 닫습니다. HTTP/2 ping은 연결에 대한 작업으로 간주하며 연결이 유휴 상태로 닫히지 않도록 합니다.
* 비정상 연결을 닫습니다. 클라이언트가 구성된 시간에 연결 유지 ping에 응답하지 않는 연결은 서버에서 닫습니다.

HTTP/2 연결 유지 ping과 관련된 두 가지 구성 옵션이 있습니다.

* `Http2.KeepAlivePingInterval`은 ping 간격을 구성하는 `TimeSpan`입니다. 서버가 이 기간 동안 프레임을 받지 못하면 클라이언트로 연결 유지 ping을 보냅니다. 이 옵션을 `TimeSpan.MaxValue`로 설정하면 연결 유지 ping을 사용할 수 없습니다. 기본값은 `TimeSpan.MaxValue`입니다.
* `Http2.KeepAlivePingTimeout`은 ping 시간 제한을 구성하는 `TimeSpan`입니다. 서버가 이 시간 제한 동안 응답 ping과 같은 프레임을 받지 못하면 연결이 닫힙니다. 이 옵션을 `TimeSpan.MaxValue`로 설정하면 연결 유지 시간 제한을 사용할 수 없습니다. 기본값은 20초입니다.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a>기타 옵션

### <a name="synchronous-io"></a>동기 I/O

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 I/O가 허용되는지 여부를 제어합니다. 기본값은 `false`입니다.

> [!WARNING]
> 차단 동기 I/O 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다. 비동기 I/O를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 사용하도록 설정하세요.

다음 예제에서는 동기 I/O를 사용하도록 설정합니다.

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
