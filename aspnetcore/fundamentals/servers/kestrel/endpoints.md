---
title: ASP.NET Core Kestrel 웹 서버의 엔드포인트 구성
author: rick-anderson
description: ASP.NET Core의 플랫폼 간 웹 서버인 Kestrel을 사용하여 엔드포인트를 구성하는 방법에 관해 알아봅니다.
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
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: 5fec573013da5bcb5039b7a189fd84d964349b3a
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658744"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a>ASP.NET Core Kestrel 웹 서버의 엔드포인트 구성

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

* [.NET SDK](/dotnet/core/sdk)가 설치된 경우.
* [dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.

일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.

프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.

`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).

`KestrelServerOptions` 구성:

## <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults(Action\<ListenOptions>)

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.

## <a name="configureiconfiguration"></a>Configure(IConfiguration)

입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다. 구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.

`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

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

### <a name="no-configuration"></a>구성 없음

Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a>구성에서 기본 인증서를 바꿈

기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다. 디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.

다음 *appsettings.json* 예제에서

* 잘못된 인증서 사용을 허가하려면 `AllowInvalid`를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).
* 인증서를 지정하지 않은 임의의 HTTPS 엔드포인트(다음 예제에서 `HttpsDefaultCert`)는 `Certificates:Default`에서 정의된 인증서 또는 개발 인증서를 사용합니다.

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
      "HttpsInlineCertAndKeyFile": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Path": "<path to .pem/.crt file>",
          "KeyPath": "<path to .key file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5003",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5004"
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

스키마 참고 사항:

* 엔드포인트 이름은 [대/소문자를 구분하지 않습니다](xref:fundamentals/configuration/index#configuration-keys-and-values). 예를 들어 `HTTPS` and `Https` 와 동일합니다.
* `Url` 매개 변수는 각 엔드포인트에 대해 필요합니다. 이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.
* 이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다. `Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.
* `Certificate` 섹션은 선택 사항입니다. `Certificate` 섹션이 지정되지 않은 경우 `Certificates:Default`에 정의된 기본값이 사용됩니다. 사용할 수 있는 기본값이 없으면 개발 인증서가 사용됩니다. 기본값이 없고 개발 인증서가 없는 경우 서버가 예외를 throw하고 시작에 실패합니다.
* `Certificate` 섹션은 여러 [인증서 원본](#certificate-sources)을 지원합니다.
* 포트 충돌을 일으키지 않는 한 [구성](xref:fundamentals/configuration/index)에서 원하는 수의 엔드포인트를 정의할 수 있습니다.

#### <a name="certificate-sources"></a>인증서 원본

인증서 노드를 구성하여 여러 원본에서 인증서를 로드할 수 있습니다.

* `Path` 및 `Password`: *.pfx* 파일 로드.
* `Path`, `KeyPath` 및 `Password`: *.pem*/ *.crt* 및 *.key* 파일 로드.
* `Subject` 및 `Store`: 인증서 저장소에서 로드.

예를 들어, `Certificates:Default` 인증서는 다음과 같이 지정될 수 있습니다.

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a>ConfigurationLoader

`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader>를 반환합니다.

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

`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.

* 각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.
* 여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다. `Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다. 메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.
* `KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다. 이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.

### <a name="change-the-defaults-in-code"></a>코드에서 기본값 변경

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

## <a name="configure-endpoints-using-server-name-indication"></a>서버 이름 표시를 사용하여 엔드포인트 구성

[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다. 함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다. TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.

Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다. 앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다. 다음 콜백 코드는 프로젝트 *Program.cs* 파일의 `ConfigureWebHostDefaults` 메서드 호출에서 사용할 수 있습니다.

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

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
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

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

SNI 지원에는 다음 항목이 필요합니다.

* 대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다. `net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다. 클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.
* 모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다. Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.

## <a name="connection-logging"></a>연결 로깅

<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다. 연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다. `UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다. `UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다. 이는 기본 제공 [연결 미들웨어](#connection-middleware)입니다.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a>TCP 소켓에 바인딩

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다. 동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a>Unix 소켓에 바인딩

이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* Nginx 구성 파일에서 `server` > `location` > `proxy_pass` 항목을 `http://unix:/tmp/{KESTREL SOCKET}:/;`으로 설정합니다. `{KESTREL SOCKET}`은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A>에 제공된 소켓의 이름입니다(예: 이전 예제의 `kestrel-test.sock`).
* 소켓이 Nginx에서 쓰기 가능한지 확인합니다(예: `chmod go+w /tmp/kestrel-test.sock`).

## <a name="port-0"></a>포트 0

포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다. 다음 예제에서는 Kestrel이 런타임에 바인딩된 포트를 확인하는 방법을 보여 줍니다.

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a>제한 사항

다음 방법으로 엔드포인트를 구성합니다.

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* `--urls` 명령줄 인수
* `urls` 호스트 구성 키
* `ASPNETCORE_URLS`환경 변수

이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다. 그러나 다음과 같은 제한 사항에 유의하세요.

* HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 문서의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).
* `Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.

## <a name="iis-endpoint-configuration"></a>IIS 엔드포인트 구성

IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다. 자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 참조하세요.

## <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

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

## <a name="connection-middleware"></a>연결 미들웨어

필요할 경우 사용자 지정 연결 미들웨어를 사용하여 각 연결을 기준으로 특정 암호에 대한 TLS 핸드셰이크를 필터링할 수 있습니다.

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

## <a name="set-the-http-protocol-from-configuration"></a>구성에서 HTTP 프로토콜 설정

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

## <a name="url-prefixes"></a>URL 접두사

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

  호스트 이름, `*` 및 `+`는 특별하지 않습니다. 유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다. 다양한 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다. 역방향 프록시 서버의 예로는 IIS, Nginx 또는 Apache가 있습니다.

  > [!WARNING]
  > 역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](xref:fundamentals/servers/kestrel/host-filtering)이 필요합니다.

* 포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  `localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다. 요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다. 루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.
