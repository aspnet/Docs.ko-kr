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
ms.openlocfilehash: 54d7304430c2a77347b6e3279f638cf6d7add77e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589047"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a>ASP.NET Core에서 Kestrel 웹 서버 구현

작성자: [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) 및 [Stephen Halter](https://twitter.com/halter73)

::: moniker range=">= aspnetcore-5.0"

Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다. Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버이며 사용하도록 설정됩니다.

Kestrel에서는 다음 시나리오를 지원합니다.

* HTTPS
* [HTTP/2](xref:fundamentals/servers/kestrel/http2)(macOS&dagger; 제외)
* [Websocket](xref:fundamentals/websockets)을 활성화하는 데 사용되는 불투명 업그레이드
* Nginx 뒤의 고성능을 위한 Unix 소켓

이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.

Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="get-started"></a>시작하기

ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다. *Program.cs* 에서 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>를 호출합니다.

[!code-csharp[](kestrel/samples/5.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#set-up-a-host>의 *호스트 설정* 및 *기본 작성기 설정* 섹션을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

<a name="endpoint-configuration"></a>
* <xref:fundamentals/servers/kestrel/endpoints>
<a name="kestrel-options"></a>
* <xref:fundamentals/servers/kestrel/options>
<a name="http2-support"></a>
* <xref:fundamentals/servers/kestrel/http2>
<a name="when-to-use-kestrel-with-a-reverse-proxy"></a>
* <xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy>
<a name="host-filtering"></a>
* <xref:fundamentals/servers/kestrel/host-filtering>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [RFC 7230: 메시지 구문 및 라우팅(섹션 5.4: 호스트)](https://tools.ietf.org/html/rfc7230#section-5.4)
* Linux에서 UNIX 소켓을 사용하는 경우 앱을 종료할 때 소켓이 자동으로 삭제되지 않습니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/aspnetcore/issues/14134)를 참조하세요.

> [!NOTE]
> ASP.NET Core 5.0을 기준으로 Kestrel의 libuv 전송은 사용되지 않습니다. Libuv 전송은 Windows ARM64와 같은 새로운 OS 플랫폼을 지원하는 업데이트를 수신하지 않으며 이후 릴리스에서 제거될 예정입니다. 사용되지 않는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> 메서드에 대한 모든 호출을 제거하고 대신 Kestrel의 기본 소켓 전송을 사용하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다. Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.

Kestrel에서는 다음 시나리오를 지원합니다.

* HTTPS
* [Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드
* Nginx 뒤의 고성능을 위한 Unix 소켓
* HTTP/2(macOS&dagger; 제외)

이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.

Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>HTTP/2 지원

다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해 [HTTP/2](https://httpwg.org/specs/rfc7540.html)를 사용할 수 있습니다.

* 운영 체제&dagger;
  * Windows Server 2016/Windows 10 이상&Dagger;
  * Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)
* 대상 프레임워크: .NET Core 2.2 이상
* [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결
* TLS 1.2 이상 연결

이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.
&Dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다. 이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다. TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.

HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.

.NET Core 3.0부터 HTTP/2는 기본적으로 사용됩니다. 구성에 대한 자세한 내용은 [Kestrel 옵션](#kestrel-options) 및 [ListenOptions.Protocols](#listenoptionsprotocols) 섹션을 참조하세요.

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kestrel을 역방향 프록시와 함께 사용하는 경우

Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버* 와 함께 사용할 수 있습니다. 역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.

Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

Kestrel은 역방향 프록시 구성에 사용됩니다.

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.

역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다. Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다. 포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.

역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.

역방향 프록시:

* 호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.
* 구성 및 방어의 추가 계층을 제공합니다.
* 기존 인프라와 잘 통합될 수 있습니다.
* 부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다. 역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.

> [!WARNING]
> 역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.

## <a name="kestrel-in-aspnet-core-apps"></a>ASP.NET Core 앱의 Kestrel

ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다. *Program.cs* 에서 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>를 호출합니다.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#set-up-a-host>의 *호스트 설정* 및 *기본 작성기 설정* 섹션을 참조하세요.

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

## <a name="kestrel-options"></a>Kestrel 옵션

Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 및 [엔드포인트 구성](#endpoint-configuration)은 구성 공급자에서 구성할 수 있습니다. 나머지 Kestrel 구성은 C# 코드로 구성해야 합니다.

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

### <a name="keep-alive-timeout"></a>Keep-alive 시간 제한

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다. 기본값은 2분입니다.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>최대 클라이언트 연결

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다. 연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

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

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

미들웨어에서 특정 요청에 대한 설정을 재정의합니다.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다. `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.

앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.

### <a name="minimum-request-body-data-rate"></a>최소 요청 본문 데이터 속도

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다. 속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다. 유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.

기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.

최소 속도는 응답에도 적용됩니다. 요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.

*Program.cs* 에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

미들웨어에서 요청당 최소 속도 제한을 재정의합니다.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

요청 멀티플렉싱에 대한 프로토콜의 지원으로 인해 요청별 속도 제한을 수정하는 것이 HTTP/2에 대해 일반적으로 지원되지 않으므로, 이전 샘플에서 참조된 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>는 HTTP/2 요청에 대해 `HttpContext.Features`에 존재하지 않습니다. 그러나 HTTP/2 요청에 대해서도 `IHttpMinRequestBodyDataRateFeature.MinDataRate`를 `null`로 설정하여 요청별 읽기 속도 제한을 *완전히 비활성화* 할 수 있으므로 HTTP/2 요청에 대한 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>는 여전히 존재합니다`HttpContext.Features`. `IHttpMinRequestBodyDataRateFeature.MinDataRate` 읽기를 시도하거나 `null`이 아닌 값으로 설정하려고 하면 HTTP/2 요청이 있을 때 `NotSupportedException`이 throw됩니다.

`KestrelServerOptions.Limits`를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.

### <a name="request-headers-timeout"></a>요청 헤더 시간 제한

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다. 기본값은 30초입니다.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

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

### <a name="trailers"></a>트레일러

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>다시 설정

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a>동기 I/O

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 I/O가 허용되는지 여부를 제어합니다. 기본값은 `false`입니다.

> [!WARNING]
> 차단 동기 I/O 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다. 비동기 I/O를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 사용하도록 설정하세요.

다음 예제에서는 동기 I/O를 사용하도록 설정합니다.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>엔드포인트 구성

기본적으로 ASP.NET Core는 다음으로 바인딩합니다.

* `http://localhost:5000`
* `https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)

다음을 사용하여 URL을 지정합니다.

* `ASPNETCORE_URLS` 환경 변수.
* `--urls` 명령줄 인수.
* `urls` 호스트 구성 키.
* `UseUrls` 확장명 메서드.

이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다. 값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).

이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.

개발 인증서를 만듭니다.

* [.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.
* [dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.

일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.

프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.

`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).

`KestrelServerOptions` 구성:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults(Action\<ListenOptions>)

지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다. `ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)

각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다. `ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.

### <a name="configureiconfiguration"></a>Configure(IConfiguration)

입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다. 구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

HTTPS를 사용하려면 Kestrel을 구성합니다.

`ListenOptions.UseHttps` 확장:

* `UseHttps`: 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel을 구성합니다. 기본 인증서가 구성되지 않은 경우 예외를 throw합니다.
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

`ListenOptions.UseHttps` 매개 변수:

* `filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.
* `password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.
* `configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다. `ListenOptions`를 반환합니다.
* `storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.
* `subject`은 인증서의 주체 이름입니다.
* `allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.
* `location`은 인증서를 로드할 수 있는 저장소 위치입니다.
* `serverCertificate`은 X.509 인증서입니다.

프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다. 최소한 기본 인증서를 제공해야 합니다.

다음에 설명된 지원되는 구성입니다.

* 구성 없음
* 구성에서 기본 인증서를 바꿈
* 코드에서 기본값 변경

*구성 없음*

Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).

<a name="configuration"></a>

*구성에서 기본 인증서를 바꿈*

`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다. 기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다. 디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.

다음 *appsettings.json* 예제에서

* 잘못된 인증서 사용을 허가하려면 **AllowInvalid** 를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).
* 인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본** 에서 정의된 인증서 또는 개발 인증서로 대체합니다.

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

모든 인증서 노드에 대해 **경로** 및 **암호** 를 사용하는 대신 인증서 저장소 필드를 지정합니다. 예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

스키마 참고 사항:

* 엔드포인트 이름은 대/소문자를 구분하지 않습니다. 예를 들어, `HTTPS` 및 `Https`는 유효합니다.
* `Url` 매개 변수는 각 엔드포인트에 대해 필요합니다. 이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.
* 이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다. `Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.
* `Certificate` 섹션은 선택 사항입니다. `Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다. 기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.
* `Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.
* 많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.

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

`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.

* 각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.
* 여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다. `Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다. 메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.
* `KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다. 이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.

*코드에서 기본값 변경*

`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다. `ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.

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

*SNI에 대한 Kestrel 지원*

[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다. 함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다. TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.

Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다. 앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.

SNI 지원에는 다음 항목이 필요합니다.

* 대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다. `net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다. 클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.
* 모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다. Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.

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

### <a name="connection-logging"></a>연결 로깅

<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다. 연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다. `UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다. `UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>TCP 소켓에 바인딩

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다. 동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Unix 소켓에 바인딩

이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* Nginx 구성 파일에서 `server` > `location` > `proxy_pass` 항목을 `http://unix:/tmp/{KESTREL SOCKET}:/;`으로 설정합니다. `{KESTREL SOCKET}`은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>에 제공된 소켓의 이름입니다(예: 이전 예제의 `kestrel-test.sock`).
* 소켓이 Nginx에서 쓰기 가능한지 확인합니다(예: `chmod go+w /tmp/kestrel-test.sock`).

### <a name="port-0"></a>포트 0

포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다. 다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>제한 사항

다음 방법으로 엔드포인트를 구성합니다.

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls` 명령줄 인수
* `urls` 호스트 구성 키
* `ASPNETCORE_URLS`환경 변수

이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다. 그러나 다음과 같은 제한 사항에 유의하세요.

* HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).
* `Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.

### <a name="iis-endpoint-configuration"></a>IIS 엔드포인트 구성

IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다. 자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.

### <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

`Protocols` 속성은 연결 엔드포인트 또는 서버에 대해 사용할 수 있는 HTTP 프로토콜(`HttpProtocols`)을 설정합니다. `HttpProtocols` 열거형의 `Protocols` 속성에 값을 할당합니다.

| `HttpProtocols` 열거형 값 | 허용되는 연결 프로토콜 |
| -------------------------- | ----------------------------- |
| `Http1`                    | HTTP/1.1 전용. TLS와 함께 또는 TLS 없이 사용할 수 있습니다. |
| `Http2`                    | HTTP/2 전용. 클라이언트가 [이전 기술 모드](https://tools.ietf.org/html/rfc7540#section-3.4)를 지원하는 경우에만 TLS 없이 사용할 수 있습니다. |
| `Http1AndHttp2`            | HTTP/1.1 및 HTTP/2. HTTP/2를 사용하려면 클라이언트가 TLS [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 핸드셰이크에서 HTTP/2를 선택해야 합니다. 그렇지 않으면 연결 기본값은 HTTP/1.1입니다. |

모든 엔드포인트의 기본 `ListenOptions.Protocols` 값은 `HttpProtocols.Http1AndHttp2`입니다.

HTTP/2에 대한 TLS 제한 사항:

* TLS 버전 1.2 이상
* 재협상 사용 안 함
* 압축 사용 안함
* 최소 임시 키 교환 크기:
  * ECDHE(타원 곡선 Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 최소 224비트
  * DHE(유한체 Diffie-Hellman) &lbrack;`TLS12`&rbrack;: 최소 2048비트
* 암호 도구 모음은 금지되지 않습니다. 

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;(P-256 타원 곡선 &lbrack;`FIPS186`&rbrack; 포함)는 기본적으로 지원됩니다.

다음 예제는 포트 8000에서 HTTP/1.1 및 HTTP/2 연결을 허용합니다. 연결은 제공된 인증서를 사용하여 TLS로 보호됩니다.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

필요할 경우 연결 미들웨어를 사용하여 각 연결을 기준으로 특정 암호에 대한 TLS 핸드셰이크를 필터링합니다.

다음 예는 앱에서 지원하지 않는 모든 암호화 알고리즘에 대해 <xref:System.NotSupportedException>을 throw합니다. 또는 [ITlsHandshakeFeature CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm)을 정의하고 허용되는 암호 그룹 목록과 비교합니다.

[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 암호화 알고리즘과 함께 사용되는 암호화는 없습니다.

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

<xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> 람다를 통해 연결 필터링을 구성할 수도 있습니다.

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

Linux에서 <xref:System.Net.Security.CipherSuitesPolicy>를 사용하여 각 연결을 기준으로 TLS 핸드셰이크를 필터링할 수 있습니다.

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

구성에서 프로토콜 설정

`CreateDefaultBuilder`는 기본적으로 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.

다음 *appsettings.json* 예제에서는 HTTP/1.1을 모든 엔드포인트의 기본 연결 프로토콜로 설정합니다.

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

다음 *appsettings.json* 예제에서는 특정 엔드포인트의 HTTP/1.1 연결 프로토콜을 설정합니다.

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

코드에서 지정한 프로토콜이 구성에서 설정된 값을 재정의합니다.

### <a name="url-prefixes"></a>URL 접두사

`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.

HTTP URL 접두사만 유효합니다. Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.

* 포트 번호가 있는 IPv4 주소

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.

* 포트 번호가 있는 IPv6 주소

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.

* 포트 번호가 있는 호스트 이름

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  호스트 이름, `*` 및 `+`는 특별하지 않습니다. 유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다. 서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.

  > [!WARNING]
  > 역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.

* 포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  `localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다. 요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다. 루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.

## <a name="host-filtering"></a>호스트 필터링

Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다. 호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다. 명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다. `Host` 헤더의 유효성이 검사되지 않았습니다.

해결 방법으로 호스트 필터링 미들웨어를 사용합니다. 호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공하며, 이 패키지는 ASP.NET Core 앱을 위해 암시적으로 제공됩니다. 미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

호스트 필터링 미들웨어는 기본적으로 비활성화됩니다. 미들웨어를 활성화하려면 *appsettings.json* /*appsettings.\<EnvironmentName>.json* 에서 `AllowedHosts` 키를 정의합니다. 값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다. 전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다. 전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다. 호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.
>
> 전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.

## <a name="libuv-transport-configuration"></a>Libuv 전송 구성

Libuv를 사용해야 하는 프로젝트의 경우(<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):

* 앱의 프로젝트 파일에 [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) 패키지에 대한 종속성을 추가합니다.

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* `IWebHostBuilder`에서 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> 호출:

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

## <a name="http11-request-draining"></a>HTTP/1.1 요청 드레이닝

HTTP 연결을 여는 데 시간이 오래 걸립니다. HTTPS의 경우 리소스도 많이 사용됩니다. 따라서 Kestrel은 HTTP/1.1 프로토콜에 따라 연결을 다시 사용하려고 시도합니다. 연결을 다시 사용하려면 요청 본문이 완전히 사용되어야 합니다. 서버가 리디렉션 또는 404 응답을 반환하는 경우 앱이 항상 `POST` 요청 같은 요청 본문을 사용하는 것은 아닙니다. `POST` 리디렉션 사례:

* 클라이언트가 이미 `POST` 데이터 일부를 전송했을 수 있습니다.
* 서버가 301 응답을 기록합니다.
* 이전 요청 본문의 `POST` 데이터를 완전히 읽을 때까지 새 요청에 연결을 사용할 수 없습니다.
* Kestrel은 요청 본문을 드레이닝하려고 시도합니다. 요청 본문을 드레이닝하면 데이터가 처리되지 않고 읽고 삭제됩니다.

드레이닝 프로세스는 연결이 다시 사용되도록 허용하는 것과 남은 데이터를 드레이닝하는 데 걸리는 시간을 절충합니다.

* 드레이닝의 시간 제한은 5초이며, 이는 구성할 수 없습니다.
* `Content-Length` 또는 `Transfer-Encoding` 헤더에 지정된 모든 데이터를 시간 제한 전에 읽지 않으면 연결이 닫힙니다.

때에 따라 응답을 쓰기 전이나 쓴 후에 즉시 요청을 종료하려고 할 수 있습니다. 예를 들어 클라이언트는 제한적인 데이터 한도를 포함할 수 있으므로 업로드된 데이터를 제한하는 것이 우선일 수 있습니다. 이 경우 요청을 종료하려면 컨트롤러, Razor 페이지 또는 미들웨어에서 [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)를 호출합니다.

`Abort` 호출에 관련된 주의 사항이 있습니다.

* 새 연결을 만드는 것은 느리고 비용이 많이 들 수 있습니다.
* 연결이 닫히기 전에 클라이언트가 응답을 읽었다는 보장은 없습니다.
* `Abort` 호출은 드문 경우이며 일반적인 오류가 아닌 심각한 오류 사례용으로 예약되어야 합니다.
  * 특정 문제를 해결해야 하는 경우에만 `Abort`를 호출합니다. 예를 들어 악의적인 클라이언트가 데이터를 `POST`하려고 하거나 클라이언트 코드에 크거나 많은 요청을 발생시키는 버그가 있는 경우 `Abort`를 호출합니다.
  * HTTP 404(찾을 수 없음)와 같은 일반적인 오류 상황에서는 `Abort`를 호출하지 마세요.

`Abort`를 호출하기 전에 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A)를 호출하면 서버가 응답 쓰기를 완료합니다. 그러나 클라이언트 동작은 예측할 수 없으며 연결이 중단되기 전에 응답을 읽지 못할 수 있습니다.

프로토콜이 연결을 닫지 않고 개별 요청 스트림을 중단하는 동작을 지원하기 때문에 HTTP/2의 경우 이 프로세스가 다릅니다. 5초 드레이닝 시간 제한이 적용되지 않습니다. 응답을 완료한 후에 읽지 않은 요청 본문 데이터가 있으면 서버는 HTTP/2 RST 프레임을 보냅니다. 추가 요청 본문 데이터 프레임은 무시됩니다.

가능하면 클라이언트가 [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 요청 헤더를 사용하고 서버가 요청 본문을 보내기 시작하기 전에 응답할 때까지 기다리는 것이 좋습니다. 이렇게 하면 클라이언트는 응답을 검사하고 불필요한 데이터를 보내기 전에 중단할 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* Linux에서 UNIX 소켓을 사용하는 경우 앱을 종료할 때 소켓이 자동으로 삭제되지 않습니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/aspnetcore/issues/14134)를 참조하세요.
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [RFC 7230: 메시지 구문 및 라우팅(섹션 5.4: 호스트)](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다. Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.

Kestrel에서는 다음 시나리오를 지원합니다.

* HTTPS
* [Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드
* Nginx 뒤의 고성능을 위한 Unix 소켓
* HTTP/2(macOS&dagger; 제외)

이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.

Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>HTTP/2 지원

다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해 [HTTP/2](https://httpwg.org/specs/rfc7540.html)를 사용할 수 있습니다.

* 운영 체제&dagger;
  * Windows Server 2016/Windows 10 이상&Dagger;
  * Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)
* 대상 프레임워크: .NET Core 2.2 이상
* [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결
* TLS 1.2 이상 연결

이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.
&Dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다. 이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다. TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.

HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.

HTTP/2는 기본적으로 사용할 수 없습니다. 구성에 대한 자세한 내용은 [Kestrel 옵션](#kestrel-options) 및 [ListenOptions.Protocols](#listenoptionsprotocols) 섹션을 참조하세요.

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kestrel을 역방향 프록시와 함께 사용하는 경우

Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버* 와 함께 사용할 수 있습니다. 역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.

Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

Kestrel은 역방향 프록시 구성에 사용됩니다.

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.

역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다. Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다. 포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.

역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.

역방향 프록시:

* 호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.
* 구성 및 방어의 추가 계층을 제공합니다.
* 기존 인프라와 잘 통합될 수 있습니다.
* 부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다. 역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.

> [!WARNING]
> 역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>ASP.NET Core 앱에서 Kestrel을 사용하는 방법

[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.

ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다. *Program.cs* 에서 템플릿 코드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> 숨은 기능을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

`CreateDefaultBuilder`와 호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>의 *호스트 설정* 섹션을 참조하세요.

`CreateDefaultBuilder`를 호출한 후 추가 구성을 제공하려면 `ConfigureKestrel`을 사용합니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

앱이 호스트를 설정하기 위해 `CreateDefaultBuilder`를 호출하지 않는 경우 `ConfigureKestrel`을 호출하기 **전에** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>을 호출합니다.

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

## <a name="kestrel-options"></a>Kestrel 옵션

Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다. `Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.

다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

다음 예에서 C# 코드로 구성된 Kestrel 옵션도 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수 있습니다. 예를 들어 파일 구성 공급자는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.

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

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.

### <a name="keep-alive-timeout"></a>Keep-alive 시간 제한

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다. 기본값은 2분입니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a>최대 클라이언트 연결

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다. 연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

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

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

미들웨어에서 특정 요청에 대한 설정을 재정의합니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다. `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.

앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.

### <a name="minimum-request-body-data-rate"></a>최소 요청 본문 데이터 속도

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다. 속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다. 유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.

기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.

최소 속도는 응답에도 적용됩니다. 요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.

*Program.cs* 에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

미들웨어에서 요청당 최소 속도 제한을 재정의합니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

요청 멀티플렉싱에 대한 프로토콜의 지원으로 인해 요청별 속도 제한을 수정하는 것이 HTTP/2에 대해 지원되지 않으므로, 이전 샘플에서 참조된 속도 기능은 HTTP/2 요청에 대해 `HttpContext.Features`에 존재하지 않습니다. `KestrelServerOptions.Limits`를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.

### <a name="request-headers-timeout"></a>요청 헤더 시간 제한

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다. 기본값은 30초입니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a>연결당 최대 스트림

`Http2.MaxStreamsPerConnection`은 HTTP/2 연결당 동시 요청 스트림 수를 제한합니다. 초과 스트림은 거부됩니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

기본값은 100입니다.

### <a name="header-table-size"></a>헤더 테이블 크기

HPACK 디코더는 HTTP/2 연결에 대한 HTTP 헤더의 압축을 풉니다. `Http2.HeaderTableSize`는 HPACK 디코더가 사용하는 헤더 압축 테이블의 크기를 제한합니다. 값은 8진수로 제공되며 영(0)보다 커야 합니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

기본값은 4096입니다.

### <a name="maximum-frame-size"></a>최대 프레임 크기

`Http2.MaxFrameSize`는 수신할 HTTP/2 연결 프레임 페이로드의 최대 크기를 나타냅니다. 값은 8진수로 제공되며 2^14(16,384)와 2^24-1(16,777,215) 사이여야 합니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

기본값은 2^14(16,384)입니다.

### <a name="maximum-request-header-size"></a>최대 요청 헤더 크기

`Http2.MaxRequestHeaderFieldSize`는 요청 헤더 값의 8진수로 허용되는 최대 크기를 나타냅니다. 이 한도는 이름과 값이 모두 압축된 표현과 압축되지 않은 표현으로 함께 적용됩니다. 값은 0보다 커야 합니다(0).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

기본값은 8,192입니다.

### <a name="initial-connection-window-size"></a>초기 연결 창 크기

`Http2.InitialConnectionWindowSize`는 연결당 모든 요청(스트림)을 통해 한 번에 집계하는 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다. 요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다. 값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

기본값은 128KB(131,072)입니다.

### <a name="initial-stream-window-size"></a>초기 스트림 창 크기

`Http2.InitialStreamWindowSize`는 요청(스트림)당 한 번에 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다. 요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다. 값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

기본값은 96KB(98,304)입니다.

### <a name="synchronous-io"></a>동기 I/O

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 I/O가 허용되는지 여부를 제어합니다. 기본값은 `true`입니다.

> [!WARNING]
> 차단 동기 I/O 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다. 비동기 I/O를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 사용하도록 설정하세요.

다음 예제에서는 동기 I/O를 사용하도록 설정합니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>엔드포인트 구성

기본적으로 ASP.NET Core는 다음으로 바인딩합니다.

* `http://localhost:5000`
* `https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)

다음을 사용하여 URL을 지정합니다.

* `ASPNETCORE_URLS` 환경 변수.
* `--urls` 명령줄 인수.
* `urls` 호스트 구성 키.
* `UseUrls` 확장명 메서드.

이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다. 값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).

이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.

개발 인증서를 만듭니다.

* [.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.
* [dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.

일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.

프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.

`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).

`KestrelServerOptions` 구성:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults(Action\<ListenOptions>)

지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다. `ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)

각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다. `ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.


### <a name="configureiconfiguration"></a>Configure(IConfiguration)

입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다. 구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

HTTPS를 사용하려면 Kestrel을 구성합니다.

`ListenOptions.UseHttps` 확장:

* `UseHttps`: 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel을 구성합니다. 기본 인증서가 구성되지 않은 경우 예외를 throw합니다.
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

`ListenOptions.UseHttps` 매개 변수:

* `filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.
* `password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.
* `configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다. `ListenOptions`를 반환합니다.
* `storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.
* `subject`은 인증서의 주체 이름입니다.
* `allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.
* `location`은 인증서를 로드할 수 있는 저장소 위치입니다.
* `serverCertificate`은 X.509 인증서입니다.

프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다. 최소한 기본 인증서를 제공해야 합니다.

다음에 설명된 지원되는 구성입니다.

* 구성 없음
* 구성에서 기본 인증서를 바꿈
* 코드에서 기본값 변경

*구성 없음*

Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).

<a name="configuration"></a>

*구성에서 기본 인증서를 바꿈*

`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다. 기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다. 디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.

다음 *appsettings.json* 예제에서

* 잘못된 인증서 사용을 허가하려면 **AllowInvalid** 를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).
* 인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본** 에서 정의된 인증서 또는 개발 인증서로 대체합니다.

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

모든 인증서 노드에 대해 **경로** 및 **암호** 를 사용하는 대신 인증서 저장소 필드를 지정합니다. 예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

스키마 참고 사항:

* 엔드포인트 이름은 대/소문자를 구분하지 않습니다. 예를 들어, `HTTPS` 및 `Https`는 유효합니다.
* `Url` 매개 변수는 각 엔드포인트에 대해 필요합니다. 이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.
* 이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다. `Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.
* `Certificate` 섹션은 선택 사항입니다. `Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다. 기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.
* `Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.
* 많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.

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

`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.

* 각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.
* 여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다. `Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다. 메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.
* `KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다. 이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.

*코드에서 기본값 변경*

`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다. `ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.

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

*SNI에 대한 Kestrel 지원*

[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다. 함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다. TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.

Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다. 앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.

SNI 지원에는 다음 항목이 필요합니다.

* 대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다. `net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다. 클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.
* 모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다. Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.

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

### <a name="connection-logging"></a>연결 로깅

<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다. 연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다. `UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다. `UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>TCP 소켓에 바인딩

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다. 동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Unix 소켓에 바인딩

이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* Nginx 구성 파일에서 `server` > `location` > `proxy_pass` 항목을 `http://unix:/tmp/{KESTREL SOCKET}:/;`으로 설정합니다. `{KESTREL SOCKET}`은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>에 제공된 소켓의 이름입니다(예: 이전 예제의 `kestrel-test.sock`).
* 소켓이 Nginx에서 쓰기 가능한지 확인합니다(예: `chmod go+w /tmp/kestrel-test.sock`). 

### <a name="port-0"></a>포트 0

포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다. 다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>제한 사항

다음 방법으로 엔드포인트를 구성합니다.

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls` 명령줄 인수
* `urls` 호스트 구성 키
* `ASPNETCORE_URLS`환경 변수

이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다. 그러나 다음과 같은 제한 사항에 유의하세요.

* HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).
* `Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.

### <a name="iis-endpoint-configuration"></a>IIS 엔드포인트 구성

IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다. 자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.

### <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

`Protocols` 속성은 연결 엔드포인트 또는 서버에 대해 사용할 수 있는 HTTP 프로토콜(`HttpProtocols`)을 설정합니다. `HttpProtocols` 열거형의 `Protocols` 속성에 값을 할당합니다.

| `HttpProtocols` 열거형 값 | 허용되는 연결 프로토콜 |
| -------------------------- | ----------------------------- |
| `Http1`                    | HTTP/1.1 전용. TLS와 함께 또는 TLS 없이 사용할 수 있습니다. |
| `Http2`                    | HTTP/2 전용. 클라이언트가 [이전 기술 모드](https://tools.ietf.org/html/rfc7540#section-3.4)를 지원하는 경우에만 TLS 없이 사용할 수 있습니다. |
| `Http1AndHttp2`            | HTTP/1.1 및 HTTP/2. HTTP/2를 사용하려면 TLS 및 [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결이 필요합니다. 그렇지 않은 경우 연결은 기본적으로 HTTP/1.1로 설정됩니다. |

기본 프로토콜은 HTTP/1.1입니다.

HTTP/2에 대한 TLS 제한 사항:

* TLS 버전 1.2 이상
* 재협상 사용 안 함
* 압축 사용 안함
* 최소 임시 키 교환 크기:
  * ECDHE(타원 곡선 Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 최소 224비트
  * DHE(유한체 Diffie-Hellman) &lbrack;`TLS12`&rbrack;: 최소 2048비트
* 암호 그룹 차단 안 됨

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;(P-256 타원 곡선 &lbrack;`FIPS186`&rbrack; 포함)는 기본적으로 지원됩니다.

다음 예제는 포트 8000에서 HTTP/1.1 및 HTTP/2 연결을 허용합니다. 연결은 제공된 인증서를 사용하여 TLS로 보호됩니다.

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

선택적으로, `IConnectionAdapter` 구현을 만들어 각 연결 단위로 특정 암호에 대한 TLS 핸드셰이크를 필터링합니다.

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

구성에서 프로토콜 설정

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>는 기본적으로 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.

다음 *appsettings.json* 예제에서는 모든 Kestrel의 엔드포인트에 기본 연결 프로토콜(HTTP/1.1 및 HTTP/2)이 설정됩니다.

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

다음 구성 파일 예제에서는 특정 엔드포인트에 대해 연결 프로토콜을 설정합니다.

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

코드에서 지정한 프로토콜이 구성에서 설정된 값을 재정의합니다.

## <a name="libuv-transport-configuration"></a>Libuv 전송 구성

ASP.NET Core 2.1 릴리스에서 Kestrel의 기본 전송은 더 이상 Libuv에 기반하지 않으며 대신 관리 소켓에 기반합니다. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출하고 다음 패키지 중 하나를 사용하는 2.1로 업그레이드된 ASP.NET Core 2.0 앱의 주요 변경 내용입니다.

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)(직접 패키지 참조)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Libuv를 사용해야 하는 프로젝트의 경우:

* [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 패키지에 대한 종속성을 앱의 프로젝트 파일에 추가합니다.

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출합니다.

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

### <a name="url-prefixes"></a>URL 접두사

`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.

HTTP URL 접두사만 유효합니다. Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.

* 포트 번호가 있는 IPv4 주소

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.

* 포트 번호가 있는 IPv6 주소

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.

* 포트 번호가 있는 호스트 이름

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  호스트 이름, `*` 및 `+`는 특별하지 않습니다. 유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다. 서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.

  > [!WARNING]
  > 역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.

* 포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  `localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다. 요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다. 루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.

## <a name="host-filtering"></a>호스트 필터링

Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다. 호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다. 명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다. `Host` 헤더의 유효성이 검사되지 않았습니다.

해결 방법으로 호스트 필터링 미들웨어를 사용합니다. 호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공되고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 또는 2.2)에 포함됩니다. 미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

호스트 필터링 미들웨어는 기본적으로 비활성화됩니다. 미들웨어를 활성화하려면 *appsettings.json* /*appsettings.\<EnvironmentName>.json* 에서 `AllowedHosts` 키를 정의합니다. 값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다. 전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다. 전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다. 호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.
>
> 전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.

## <a name="http11-request-draining"></a>HTTP/1.1 요청 드레이닝

HTTP 연결을 여는 데 시간이 오래 걸립니다. HTTPS의 경우 리소스도 많이 사용됩니다. 따라서 Kestrel은 HTTP/1.1 프로토콜에 따라 연결을 다시 사용하려고 시도합니다. 연결을 다시 사용하려면 요청 본문이 완전히 사용되어야 합니다. 서버가 리디렉션 또는 404 응답을 반환하는 경우 앱이 항상 `POST` 요청 같은 요청 본문을 사용하는 것은 아닙니다. `POST` 리디렉션 사례:

* 클라이언트가 이미 `POST` 데이터 일부를 전송했을 수 있습니다.
* 서버가 301 응답을 기록합니다.
* 이전 요청 본문의 `POST` 데이터를 완전히 읽을 때까지 새 요청에 연결을 사용할 수 없습니다.
* Kestrel은 요청 본문을 드레이닝하려고 시도합니다. 요청 본문을 드레이닝하면 데이터가 처리되지 않고 읽고 삭제됩니다.

드레이닝 프로세스는 연결이 다시 사용되도록 허용하는 것과 남은 데이터를 드레이닝하는 데 걸리는 시간을 절충합니다.

* 드레이닝의 시간 제한은 5초이며, 이는 구성할 수 없습니다.
* `Content-Length` 또는 `Transfer-Encoding` 헤더에 지정된 모든 데이터를 시간 제한 전에 읽지 않으면 연결이 닫힙니다.

때에 따라 응답을 쓰기 전이나 쓴 후에 즉시 요청을 종료하려고 할 수 있습니다. 예를 들어 클라이언트는 제한적인 데이터 한도를 포함할 수 있으므로 업로드된 데이터를 제한하는 것이 우선일 수 있습니다. 이 경우 요청을 종료하려면 컨트롤러, Razor 페이지 또는 미들웨어에서 [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)를 호출합니다.

`Abort` 호출에 관련된 주의 사항이 있습니다.

* 새 연결을 만드는 것은 느리고 비용이 많이 들 수 있습니다.
* 연결이 닫히기 전에 클라이언트가 응답을 읽었다는 보장은 없습니다.
* `Abort` 호출은 드문 경우이며 일반적인 오류가 아닌 심각한 오류 사례용으로 예약되어야 합니다.
  * 특정 문제를 해결해야 하는 경우에만 `Abort`를 호출합니다. 예를 들어 악의적인 클라이언트가 데이터를 `POST`하려고 하거나 클라이언트 코드에 크거나 많은 요청을 발생시키는 버그가 있는 경우 `Abort`를 호출합니다.
  * HTTP 404(찾을 수 없음)와 같은 일반적인 오류 상황에서는 `Abort`를 호출하지 마세요.

`Abort`를 호출하기 전에 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A)를 호출하면 서버가 응답 쓰기를 완료합니다. 그러나 클라이언트 동작은 예측할 수 없으며 연결이 중단되기 전에 응답을 읽지 못할 수 있습니다.

프로토콜이 연결을 닫지 않고 개별 요청 스트림을 중단하는 동작을 지원하기 때문에 HTTP/2의 경우 이 프로세스가 다릅니다. 5초 드레이닝 시간 제한이 적용되지 않습니다. 응답을 완료한 후에 읽지 않은 요청 본문 데이터가 있으면 서버는 HTTP/2 RST 프레임을 보냅니다. 추가 요청 본문 데이터 프레임은 무시됩니다.

가능하면 클라이언트가 [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 요청 헤더를 사용하고 서버가 요청 본문을 보내기 시작하기 전에 응답할 때까지 기다리는 것이 좋습니다. 이렇게 하면 클라이언트는 응답을 검사하고 불필요한 데이터를 보내기 전에 중단할 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* Linux에서 UNIX 소켓을 사용하는 경우 앱을 종료할 때 소켓이 자동으로 삭제되지 않습니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/aspnetcore/issues/14134)를 참조하세요.
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [RFC 7230: 메시지 구문 및 라우팅(섹션 5.4: 호스트)](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다. Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.

Kestrel에서는 다음 시나리오를 지원합니다.

* HTTPS
* [Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드
* Nginx 뒤의 고성능을 위한 Unix 소켓

Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kestrel을 역방향 프록시와 함께 사용하는 경우

Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버* 와 함께 사용할 수 있습니다. 역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.

Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

Kestrel은 역방향 프록시 구성에 사용됩니다.

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.

역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다. Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다. 포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.

역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.

역방향 프록시:

* 호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.
* 구성 및 방어의 추가 계층을 제공합니다.
* 기존 인프라와 잘 통합될 수 있습니다.
* 부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다. 역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.

> [!WARNING]
> 역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>ASP.NET Core 앱에서 Kestrel을 사용하는 방법

[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.

ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다. *Program.cs* 에서 템플릿 코드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> 숨은 기능을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.

`CreateDefaultBuilder`를 호출한 후 추가 구성을 제공하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>을 호출합니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

`CreateDefaultBuilder`와 호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>의 *호스트 설정* 섹션을 참조하세요.

## <a name="kestrel-options"></a>Kestrel 옵션

Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다. `Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.

다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

다음 예에서 C# 코드로 구성된 Kestrel 옵션도 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수 있습니다. 예를 들어 파일 구성 공급자는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.

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

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.

### <a name="keep-alive-timeout"></a>Keep-alive 시간 제한

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다. 기본값은 2분입니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a>최대 클라이언트 연결

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다. 연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

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

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

미들웨어에서 특정 요청에 대한 설정을 재정의합니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다. `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.

앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.

### <a name="minimum-request-body-data-rate"></a>최소 요청 본문 데이터 속도

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다. 속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다. 유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.

기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.

최소 속도는 응답에도 적용됩니다. 요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.

*Program.cs* 에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.

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

### <a name="request-headers-timeout"></a>요청 헤더 시간 제한

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다. 기본값은 30초입니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a>동기 I/O

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 I/O가 허용되는지 여부를 제어합니다. 기본값은 `true`입니다.

> [!WARNING]
> 차단 동기 I/O 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다. 비동기 I/O를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 사용하도록 설정하세요.

다음 예제에서는 동기 I/O를 사용하지 않도록 설정합니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>엔드포인트 구성

기본적으로 ASP.NET Core는 다음으로 바인딩합니다.

* `http://localhost:5000`
* `https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)

다음을 사용하여 URL을 지정합니다.

* `ASPNETCORE_URLS` 환경 변수.
* `--urls` 명령줄 인수.
* `urls` 호스트 구성 키.
* `UseUrls` 확장명 메서드.

이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다. 값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).

이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.

개발 인증서를 만듭니다.

* [.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.
* [dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.

일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.

프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.

`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).

`KestrelServerOptions` 구성:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults(Action\<ListenOptions>)

지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다. `ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)

각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다. `ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.

### <a name="configureiconfiguration"></a>Configure(IConfiguration)

입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다. 구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

HTTPS를 사용하려면 Kestrel을 구성합니다.

`ListenOptions.UseHttps` 확장:

* `UseHttps`: 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel을 구성합니다. 기본 인증서가 구성되지 않은 경우 예외를 throw합니다.
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

`ListenOptions.UseHttps` 매개 변수:

* `filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.
* `password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.
* `configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다. `ListenOptions`를 반환합니다.
* `storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.
* `subject`은 인증서의 주체 이름입니다.
* `allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.
* `location`은 인증서를 로드할 수 있는 저장소 위치입니다.
* `serverCertificate`은 X.509 인증서입니다.

프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다. 최소한 기본 인증서를 제공해야 합니다.

다음에 설명된 지원되는 구성입니다.

* 구성 없음
* 구성에서 기본 인증서를 바꿈
* 코드에서 기본값 변경

*구성 없음*

Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).

<a name="configuration"></a>

*구성에서 기본 인증서를 바꿈*

`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다. 기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다. 디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.

다음 *appsettings.json* 예제에서

* 잘못된 인증서 사용을 허가하려면 **AllowInvalid** 를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).
* 인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본** 에서 정의된 인증서 또는 개발 인증서로 대체합니다.

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

모든 인증서 노드에 대해 **경로** 및 **암호** 를 사용하는 대신 인증서 저장소 필드를 지정합니다. 예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

스키마 참고 사항:

* 엔드포인트 이름은 대/소문자를 구분하지 않습니다. 예를 들어, `HTTPS` 및 `Https`는 유효합니다.
* `Url` 매개 변수는 각 엔드포인트에 대해 필요합니다. 이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.
* 이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다. `Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.
* `Certificate` 섹션은 선택 사항입니다. `Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다. 기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.
* `Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.
* 많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.

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

`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.

* 각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.
* 여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다. `Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다. 메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.
* `KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다. 이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.

*코드에서 기본값 변경*

`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다. `ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.

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

*SNI에 대한 Kestrel 지원*

[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다. 함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다. TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.

Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다. 앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.

SNI 지원에는 다음 항목이 필요합니다.

* 대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다. `net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다. 클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.
* 모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다. Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.

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

### <a name="connection-logging"></a>연결 로깅

<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다. 연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다. `UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다. `UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>TCP 소켓에 바인딩

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.

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

예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다. 동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Unix 소켓에 바인딩

이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.

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

* Nginx 구성 파일에서 `server` > `location` > `proxy_pass` 항목을 `http://unix:/tmp/{KESTREL SOCKET}:/;`으로 설정합니다. `{KESTREL SOCKET}`은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>에 제공된 소켓의 이름입니다(예: 이전 예제의 `kestrel-test.sock`).
* 소켓이 Nginx에서 쓰기 가능한지 확인합니다(예: `chmod go+w /tmp/kestrel-test.sock`). 

### <a name="port-0"></a>포트 0

포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다. 다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>제한 사항

다음 방법으로 엔드포인트를 구성합니다.

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls` 명령줄 인수
* `urls` 호스트 구성 키
* `ASPNETCORE_URLS`환경 변수

이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다. 그러나 다음과 같은 제한 사항에 유의하세요.

* HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).
* `Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.

### <a name="iis-endpoint-configuration"></a>IIS 엔드포인트 구성

IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다. 자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.

## <a name="libuv-transport-configuration"></a>Libuv 전송 구성

ASP.NET Core 2.1 릴리스에서 Kestrel의 기본 전송은 더 이상 Libuv에 기반하지 않으며 대신 관리 소켓에 기반합니다. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출하고 다음 패키지 중 하나를 사용하는 2.1로 업그레이드된 ASP.NET Core 2.0 앱의 주요 변경 내용입니다.

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)(직접 패키지 참조)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Libuv를 사용해야 하는 프로젝트의 경우:

* [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 패키지에 대한 종속성을 앱의 프로젝트 파일에 추가합니다.

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출합니다.

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

### <a name="url-prefixes"></a>URL 접두사

`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.

HTTP URL 접두사만 유효합니다. Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.

* 포트 번호가 있는 IPv4 주소

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.

* 포트 번호가 있는 IPv6 주소

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.

* 포트 번호가 있는 호스트 이름

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  호스트 이름, `*` 및 `+`는 특별하지 않습니다. 유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다. 서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.

  > [!WARNING]
  > 역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.

* 포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  `localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다. 요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다. 루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.

## <a name="host-filtering"></a>호스트 필터링

Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다. 호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다. 명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다. `Host` 헤더의 유효성이 검사되지 않았습니다.

해결 방법으로 호스트 필터링 미들웨어를 사용합니다. 호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공되고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 또는 2.2)에 포함됩니다. 미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

호스트 필터링 미들웨어는 기본적으로 비활성화됩니다. 미들웨어를 활성화하려면 *appsettings.json* /*appsettings.\<EnvironmentName>.json* 에서 `AllowedHosts` 키를 정의합니다. 값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다. 전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다. 전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다. 호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.
>
> 전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.

## <a name="http11-request-draining"></a>HTTP/1.1 요청 드레이닝

HTTP 연결을 여는 데 시간이 오래 걸립니다. HTTPS의 경우 리소스도 많이 사용됩니다. 따라서 Kestrel은 HTTP/1.1 프로토콜에 따라 연결을 다시 사용하려고 시도합니다. 연결을 다시 사용하려면 요청 본문이 완전히 사용되어야 합니다. 서버가 리디렉션 또는 404 응답을 반환하는 경우 앱이 항상 `POST` 요청 같은 요청 본문을 사용하는 것은 아닙니다. `POST` 리디렉션 사례:

* 클라이언트가 이미 `POST` 데이터 일부를 전송했을 수 있습니다.
* 서버가 301 응답을 기록합니다.
* 이전 요청 본문의 `POST` 데이터를 완전히 읽을 때까지 새 요청에 연결을 사용할 수 없습니다.
* Kestrel은 요청 본문을 드레이닝하려고 시도합니다. 요청 본문을 드레이닝하면 데이터가 처리되지 않고 읽고 삭제됩니다.

드레이닝 프로세스는 연결이 다시 사용되도록 허용하는 것과 남은 데이터를 드레이닝하는 데 걸리는 시간을 절충합니다.

* 드레이닝의 시간 제한은 5초이며, 이는 구성할 수 없습니다.
* `Content-Length` 또는 `Transfer-Encoding` 헤더에 지정된 모든 데이터를 시간 제한 전에 읽지 않으면 연결이 닫힙니다.

때에 따라 응답을 쓰기 전이나 쓴 후에 즉시 요청을 종료하려고 할 수 있습니다. 예를 들어 클라이언트는 제한적인 데이터 한도를 포함할 수 있으므로 업로드된 데이터를 제한하는 것이 우선일 수 있습니다. 이 경우 요청을 종료하려면 컨트롤러, Razor 페이지 또는 미들웨어에서 [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)를 호출합니다.

`Abort` 호출에 관련된 주의 사항이 있습니다.

* 새 연결을 만드는 것은 느리고 비용이 많이 들 수 있습니다.
* 연결이 닫히기 전에 클라이언트가 응답을 읽었다는 보장은 없습니다.
* `Abort` 호출은 드문 경우이며 일반적인 오류가 아닌 심각한 오류 사례용으로 예약되어야 합니다.
  * 특정 문제를 해결해야 하는 경우에만 `Abort`를 호출합니다. 예를 들어 악의적인 클라이언트가 데이터를 `POST`하려고 하거나 클라이언트 코드에 크거나 많은 요청을 발생시키는 버그가 있는 경우 `Abort`를 호출합니다.
  * HTTP 404(찾을 수 없음)와 같은 일반적인 오류 상황에서는 `Abort`를 호출하지 마세요.

`Abort`를 호출하기 전에 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A)를 호출하면 서버가 응답 쓰기를 완료합니다. 그러나 클라이언트 동작은 예측할 수 없으며 연결이 중단되기 전에 응답을 읽지 못할 수 있습니다.

프로토콜이 연결을 닫지 않고 개별 요청 스트림을 중단하는 동작을 지원하기 때문에 HTTP/2의 경우 이 프로세스가 다릅니다. 5초 드레이닝 시간 제한이 적용되지 않습니다. 응답을 완료한 후에 읽지 않은 요청 본문 데이터가 있으면 서버는 HTTP/2 RST 프레임을 보냅니다. 추가 요청 본문 데이터 프레임은 무시됩니다.

가능하면 클라이언트가 [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 요청 헤더를 사용하고 서버가 요청 본문을 보내기 시작하기 전에 응답할 때까지 기다리는 것이 좋습니다. 이렇게 하면 클라이언트는 응답을 검사하고 불필요한 데이터를 보내기 전에 중단할 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* Linux에서 UNIX 소켓을 사용하는 경우 앱을 종료할 때 소켓이 자동으로 삭제되지 않습니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/aspnetcore/issues/14134)를 참조하세요.
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [RFC 7230: 메시지 구문 및 라우팅(섹션 5.4: 호스트)](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end
