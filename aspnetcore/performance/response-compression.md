---
title: ASP.NET Core 응답 압축
author: rick-anderson
description: ASP.NET Core 앱에서 응답 압축 미들웨어를 사용하는 방법 및 응답 압축에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/response-compression
ms.openlocfilehash: 239f9e84d068bfd75c84ccf16f0e74cdbbbebfb2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586321"
---
# <a name="response-compression-in-aspnet-core"></a>ASP.NET Core 응답 압축

::: moniker range=">= aspnetcore-3.0"

네트워크 대역폭은 제한 된 리소스입니다. 응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 향상 됩니다. 페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축 하는 것입니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="when-to-use-response-compression-middleware"></a>응답 압축 미들웨어를 사용 하는 경우

IIS, Apache 또는 Nginx에서 서버 기반 응답 압축 기술을 사용 합니다. 미들웨어의 성능은 서버 모듈의 성능과 일치 하지 않을 수 있습니다. [HTTP.sys 서버](xref:fundamentals/servers/httpsys) 서버와 [kestrel](xref:fundamentals/servers/kestrel) 서버는 현재 기본 제공 압축 지원을 제공 하지 않습니다.

응답 압축 미들웨어를 사용 하는 경우:

* 다음 서버 기반 압축 기술을 사용할 수 없습니다.
  * [IIS 동적 압축 모듈](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate 모듈](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx 압축 및 압축 풀기](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* 에서 직접 호스팅:
  * [HTTP.sys 서버](xref:fundamentals/servers/httpsys) (이전에는 weblistener 라고 함)
  * [Kestrel 서버](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>응답 압축

일반적으로 기본적으로 압축 되지 않은 모든 응답은 응답 압축의 이점을 누릴 수 있습니다. 기본적으로 압축 되지 않은 응답은 일반적으로 CSS, JavaScript, HTML, XML 및 JSON을 포함 합니다. PNG 파일과 같이 기본적으로 압축 된 자산은 압축 해서는 안 됩니다. 고유 하 게 압축 된 응답을 추가로 압축 하려는 경우 크기 및 전송 시간의 작은 추가 감소는 압축을 처리 하는 데 걸린 시간을 overshadowed 수 있습니다. 파일의 내용과 압축 효율성에 따라 약 150-1000 바이트 보다 작은 파일은 압축 하지 않습니다. 작은 파일을 압축 하는 오버 헤드로 인해 압축 되지 않은 파일 보다 큰 압축 된 파일이 생성 될 수 있습니다.

클라이언트가 압축 된 콘텐츠를 처리할 수 있는 경우 클라이언트는 요청과 함께 헤더를 전송 하 여 해당 기능을 서버에 알려야 합니다 `Accept-Encoding` . 서버는 압축 된 콘텐츠를 보낼 때 `Content-Encoding` 압축 된 응답의 인코딩 방법에 대 한 정보를 헤더에 포함 해야 합니다. 미들웨어에서 지 원하는 콘텐츠 인코딩 명칭은 다음 표에 나와 있습니다.

| `Accept-Encoding` 헤더 값 | 미들웨어 지원 | 설명 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 예(‘기본값’)        | [Brotli 압축 된 데이터 형식](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | No                   | [DEFLATE 압축 데이터 형식](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | No                   | [W3C 효율적인 XML 교환](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Yes                  | [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Yes                  | "인코딩 안 함" 식별자: 응답은 인코딩되지 않아야 합니다. |
| `pack200-gzip`                  | No                   | [Java 보관을 위한 네트워크 전송 형식](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Yes                  | 명시적으로 요청 되지 않은 모든 사용 가능한 콘텐츠 인코딩입니다. |

자세한 내용은 [IANA 공식 콘텐츠 코딩 목록](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)을 참조 하세요.

미들웨어를 사용 하면 사용자 지정 헤더 값에 대 한 추가 압축 공급자를 추가할 수 있습니다 `Accept-Encoding` . 자세한 내용은 아래의 [사용자 지정 공급자](#custom-providers) 를 참조 하세요.

미들웨어는 `q` 압축 체계의 우선 순위를 지정 하기 위해 클라이언트에서 보낼 때 품질 값 (qvalue,) 가중치에 대응 시킬 수 있습니다. 자세한 내용은 [RFC 7231: 수락-인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4)을 참조 하세요.

압축 알고리즘은 압축 속도와 압축의 효율성 사이의 균형을 적용 합니다. 이 컨텍스트의 *효과* 는 압축 후 출력의 크기를 나타냅니다. 가장 작은 크기는 가장 *최적* 압축을 통해 달성 됩니다.

다음 표에서는 압축 된 콘텐츠 요청, 송신, 캐싱 및 수신에 관련 된 헤더에 대해 설명 합니다.

| header             | 역할 |
| ------------------ | ---- |
| `Accept-Encoding`  | 클라이언트에서 서버로 전송 되어 클라이언트에 허용 되는 콘텐츠 인코딩 스키마를 표시 합니다. |
| `Content-Encoding` | 페이로드에 있는 콘텐츠의 인코딩을 나타내기 위해 서버에서 클라이언트로 전송 됩니다. |
| `Content-Length`   | 압축이 발생 하면 `Content-Length` 응답이 압축 될 때 본문 내용이 변경 되므로 헤더가 제거 됩니다. |
| `Content-MD5`      | 압축이 발생 하면 `Content-MD5` 본문 내용이 변경 되 고 해시가 더 이상 유효 하지 않기 때문에 헤더가 제거 됩니다. |
| `Content-Type`     | 콘텐츠의 MIME 형식을 지정 합니다. 모든 응답은를 지정 해야 `Content-Type` 합니다. 미들웨어는이 값을 확인 하 여 응답이 압축 되어야 하는지 확인 합니다. 미들웨어는 인코딩할 수 있는 [기본 MIME 형식의](#mime-types) 집합을 지정 하지만 mime 형식을 바꾸거나 추가할 수 있습니다. |
| `Vary`             | 클라이언트와 프록시 값을 사용 하 여 서버에서 보내는 경우 `Accept-Encoding` `Vary` 헤더는 요청 헤더의 값을 기반으로 응답을 캐시 (vary) 해야 한다는 것을 클라이언트나 프록시에 나타냅니다 `Accept-Encoding` . 헤더를 사용 하 여 콘텐츠를 반환한 결과는 압축 된 응답과 압축 되지 않은 `Vary: Accept-Encoding` 응답이 모두 개별적으로 캐시 된다는 것입니다. |

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples)을 사용 하 여 응답 압축 미들웨어의 기능을 탐색 합니다. 샘플은 다음을 보여 줍니다.

* Gzip 및 사용자 지정 압축 공급자를 사용 하 여 앱 응답을 압축 합니다.
* Mime 형식을 압축을 위한 MIME 형식의 기본 목록에 추가 하는 방법입니다.

## <a name="package"></a>패키지

응답 압축 미들웨어는 ASP.NET Core 앱에 암시적으로 포함 되는 [ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지에서 제공 됩니다.

## <a name="configuration"></a>구성

다음 코드에서는 기본 MIME 형식 및 압축 공급자 ([Brotli](#brotli-compression-provider) 및 [Gzip](#gzip-compression-provider))에 대 한 응답 압축 미들웨어를 사용 하도록 설정 하는 방법을 보여 줍니다.

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

참고:

* `app.UseResponseCompression` 응답을 압축 하는 미들웨어 전에를 호출 해야 합니다. 자세한 내용은 <xref:fundamentals/middleware/index#middleware-order>를 참조하세요.
* [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/) 과 같은 도구를 사용 하 여 요청 헤더를 설정 하 `Accept-Encoding` 고 응답 헤더, 크기 및 본문을 연구 합니다.

헤더 없이 샘플 앱에 요청을 제출 `Accept-Encoding` 하 고 응답이 압축 되지 않은 상태 인지 확인 합니다. `Content-Encoding`및 `Vary` 헤더가 응답에 없습니다.

![Accept-Encoding 헤더가 없는 요청 결과를 표시 하는 Fiddler 창입니다. 응답이 압축 되지 않습니다.](response-compression/_static/request-uncompressed.png)

헤더 (Brotli 압축)를 사용 하 여 샘플 앱에 요청을 제출 하 `Accept-Encoding: br` 고 응답이 압축 되는지 확인 합니다. `Content-Encoding`및 `Vary` 헤더는 응답에 표시 됩니다.

![Accept-Encoding 헤더가 있는 요청 결과와 br 값을 보여 주는 Fiddler 창입니다. 응답에는 Vary 및 콘텐츠 인코딩 헤더가 추가 됩니다. 응답이 압축 됩니다.](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a>공급자

### <a name="brotli-compression-provider"></a>Brotli 압축 공급자

을 사용 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> 하 여 [Brotli 압축 된 데이터 형식의](https://tools.ietf.org/html/rfc7932)응답을 압축 합니다.

에 명시적으로 추가 된 압축 공급자가 없는 경우 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :

* Brotli 압축 공급자는 기본적으로 [Gzip 압축 공급자](#gzip-compression-provider)와 함께 압축 공급자 배열에 추가 됩니다.
* 클라이언트에서 Brotli 압축 데이터 형식이 지원 되는 경우 압축의 기본값은 Brotli 압축입니다. 클라이언트에서 Brotli을 지원 하지 않는 경우 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

압축 공급자가 명시적으로 추가 되 면 Brotli 압축 공급자를 추가 해야 합니다.

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

로 압축 수준을 설정 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> 합니다. Brotli 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며,이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다. 가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.

| 압축 수준 | 설명 |
| ----------------- | ----------- |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다. |
| [CompressionLevel 압축](xref:System.IO.Compression.CompressionLevel) | 압축을 수행할 수 없습니다. |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="gzip-compression-provider"></a>Gzip 압축 공급자

를 사용 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 하 여 [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952)으로 응답을 압축 합니다.

에 명시적으로 추가 된 압축 공급자가 없는 경우 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :

* Gzip 압축 공급자는 기본적으로 [Brotli 압축](#brotli-compression-provider)공급자와 함께 압축 공급자 배열에 추가 됩니다.
* 클라이언트에서 Brotli 압축 데이터 형식이 지원 되는 경우 압축의 기본값은 Brotli 압축입니다. 클라이언트에서 Brotli을 지원 하지 않는 경우 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

모든 압축 공급자가 명시적으로 추가 되 면 Gzip 압축 공급자를 추가 해야 합니다.

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

로 압축 수준을 설정 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 합니다. Gzip 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다. 가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.

| 압축 수준 | 설명 |
| ----------------- | ----------- |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다. |
| [CompressionLevel 압축](xref:System.IO.Compression.CompressionLevel) | 압축을 수행할 수 없습니다. |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>사용자 지정 공급자

를 사용 하 여 사용자 지정 압축 구현을 만듭니다 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> . 는 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> 이가 생성 하는 콘텐츠 인코딩을 나타냅니다 `ICompressionProvider` . 미들웨어는이 정보를 사용 하 여 요청의 헤더에 지정 된 목록에 따라 공급자를 선택 합니다 `Accept-Encoding` .

클라이언트는 샘플 앱을 사용 하 여 헤더를 사용 하 여 요청을 제출 합니다 `Accept-Encoding: mycustomcompression` . 미들웨어는 사용자 지정 압축 구현을 사용 하 고 헤더를 사용 하 여 응답을 반환 합니다 `Content-Encoding: mycustomcompression` . 클라이언트는 사용자 지정 압축 구현이 작동 하기 위해 사용자 지정 인코딩의 압축을 해제할 수 있어야 합니다.

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


헤더를 사용 하 여 샘플 앱에 요청을 제출 `Accept-Encoding: mycustomcompression` 하 고 응답 헤더를 관찰 합니다. `Vary`및 `Content-Encoding` 헤더는 응답에 표시 됩니다. 응답 본문 (표시 되지 않음)은 샘플에 의해 압축 되지 않습니다. 샘플의 클래스에는 압축 구현이 없습니다 `CustomCompressionProvider` . 그러나이 샘플에서는 이러한 압축 알고리즘을 구현 하는 위치를 보여 줍니다.

![Accept-Encoding 헤더가 있는 요청 결과와 mycustomcompression 값이 표시 된 Fiddler 창 응답에는 Vary 및 콘텐츠 인코딩 헤더가 추가 됩니다.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME 형식

미들웨어는 압축을 위한 기본 MIME 형식 집합을 지정 합니다.

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

MIME 형식을 응답 압축 미들웨어 옵션으로 바꾸거나 추가 합니다. 와 같은 와일드 카드 MIME 형식은 `text/*` 지원 되지 않습니다. 샘플 앱은에 대 한 MIME 형식을 추가 하 고 `image/svg+xml` 압축 하 고 ASP.NET Core 배너 이미지 (*배너*)를 사용 합니다.

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a>보안 프로토콜을 사용한 압축

보안 연결을 통한 압축 된 응답 `EnableForHttps` 은 기본적으로 사용 하지 않도록 설정 된 옵션을 사용 하 여 제어할 수 있습니다. 동적으로 생성 된 페이지에서 압축을 사용 하면 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격과 같은 보안 문제가 발생할 수 있습니다.

## <a name="adding-the-vary-header"></a>Vary 헤더 추가

헤더를 기반으로 응답을 압축 하는 경우 `Accept-Encoding` 여러 압축 버전의 응답 및 압축 되지 않은 버전이 있습니다. 여러 버전이 존재 하 고 저장 해야 함을 클라이언트 및 프록시 캐시에 지시 하기 위해 헤더는 `Vary` 값을 사용 하 여 추가 됩니다 `Accept-Encoding` . ASP.NET Core 2.0 이상에서 미들웨어는 `Vary` 응답이 압축 될 때 헤더를 자동으로 추가 합니다.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Nginx 역방향 프록시 뒤에 있는 미들웨어 문제

Nginx에서 요청을 프록시 하는 경우 `Accept-Encoding` 헤더가 제거 됩니다. 헤더를 제거 하면 `Accept-Encoding` 미들웨어가 응답을 압축 하지 않습니다. 자세한 내용은 [NGINX: 압축 및](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)압축 해제를 참조 하세요. 이 문제는 [Nginx (aspnet/BasicMiddleware #123)에 대 한 통과 압축](https://github.com/aspnet/BasicMiddleware/issues/123)을 통해 추적 됩니다.

## <a name="working-with-iis-dynamic-compression"></a>IIS 동적 압축 사용

앱에 대해 사용 하지 않도록 설정할 서버 수준에서 활성 IIS 동적 압축 모듈이 구성 된 경우 *web.config* 파일을 추가 하 여 모듈을 사용 하지 않도록 설정 합니다. 보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.

## <a name="troubleshooting"></a>문제 해결

[Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 하 여 요청 헤더를 설정 하 `Accept-Encoding` 고 응답 헤더, 크기 및 본문을 검토할 수 있습니다. 기본적으로 응답 압축 미들웨어는 다음 조건을 충족 하는 응답을 압축 합니다.

* `Accept-Encoding`헤더는 `br` `gzip` `*` 설정 된 사용자 지정 압축 공급자와 일치 하는,, 또는 사용자 지정 인코딩의 값을 포함 합니다. 값 `identity` 이 이거나, 값이 0 인 품질 값 (qvalue, `q` )이 설정 되어 있지 않아야 합니다.
* MIME 형식 ( `Content-Type` )을 설정 하 고에 구성 된 mime 형식과 일치 해야 합니다 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> .
* 요청은 헤더를 포함 하지 않아야 합니다 `Content-Range` .
* 응답 압축 미들웨어 옵션에 보안 프로토콜 (https)이 구성 되지 않은 경우 요청은 안전 하지 않은 프로토콜 (http)을 사용 해야 합니다. *보안 콘텐츠 압축을 사용 하도록 설정할 때 [위에서 설명한](#compression-with-secure-protocol) 위험에 유의 하십시오.*

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla 개발자 네트워크: 수락-인코딩](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 섹션 3.1.2.1: Content Codings](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 섹션 4.2.3: Gzip 코딩](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP 파일 형식 사양 버전 4.3](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

네트워크 대역폭은 제한 된 리소스입니다. 응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 향상 됩니다. 페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축 하는 것입니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="when-to-use-response-compression-middleware"></a>응답 압축 미들웨어를 사용 하는 경우

IIS, Apache 또는 Nginx에서 서버 기반 응답 압축 기술을 사용 합니다. 미들웨어의 성능은 서버 모듈의 성능과 일치 하지 않을 수 있습니다. [HTTP.sys 서버](xref:fundamentals/servers/httpsys) 서버와 [kestrel](xref:fundamentals/servers/kestrel) 서버는 현재 기본 제공 압축 지원을 제공 하지 않습니다.

응답 압축 미들웨어를 사용 하는 경우:

* 다음 서버 기반 압축 기술을 사용할 수 없습니다.
  * [IIS 동적 압축 모듈](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate 모듈](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx 압축 및 압축 풀기](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* 에서 직접 호스팅:
  * [HTTP.sys 서버](xref:fundamentals/servers/httpsys) (이전에는 weblistener 라고 함)
  * [Kestrel 서버](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>응답 압축

일반적으로 기본적으로 압축 되지 않은 모든 응답은 응답 압축의 이점을 누릴 수 있습니다. 기본적으로 압축 되지 않은 응답은 일반적으로 CSS, JavaScript, HTML, XML 및 JSON을 포함 합니다. PNG 파일과 같이 기본적으로 압축 된 자산은 압축 해서는 안 됩니다. 고유 하 게 압축 된 응답을 추가로 압축 하려는 경우 크기 및 전송 시간의 작은 추가 감소는 압축을 처리 하는 데 걸린 시간을 overshadowed 수 있습니다. 파일의 내용과 압축 효율성에 따라 약 150-1000 바이트 보다 작은 파일은 압축 하지 않습니다. 작은 파일을 압축 하는 오버 헤드로 인해 압축 되지 않은 파일 보다 큰 압축 된 파일이 생성 될 수 있습니다.

클라이언트가 압축 된 콘텐츠를 처리할 수 있는 경우 클라이언트는 요청과 함께 헤더를 전송 하 여 해당 기능을 서버에 알려야 합니다 `Accept-Encoding` . 서버는 압축 된 콘텐츠를 보낼 때 `Content-Encoding` 압축 된 응답의 인코딩 방법에 대 한 정보를 헤더에 포함 해야 합니다. 미들웨어에서 지 원하는 콘텐츠 인코딩 명칭은 다음 표에 나와 있습니다.

| `Accept-Encoding` 헤더 값 | 미들웨어 지원 | 설명 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 예(‘기본값’)        | [Brotli 압축 된 데이터 형식](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | No                   | [DEFLATE 압축 데이터 형식](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | No                   | [W3C 효율적인 XML 교환](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Yes                  | [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Yes                  | "인코딩 안 함" 식별자: 응답은 인코딩되지 않아야 합니다. |
| `pack200-gzip`                  | No                   | [Java 보관을 위한 네트워크 전송 형식](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Yes                  | 명시적으로 요청 되지 않은 모든 사용 가능한 콘텐츠 인코딩입니다. |

자세한 내용은 [IANA 공식 콘텐츠 코딩 목록](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)을 참조 하세요.

미들웨어를 사용 하면 사용자 지정 헤더 값에 대 한 추가 압축 공급자를 추가할 수 있습니다 `Accept-Encoding` . 자세한 내용은 아래의 [사용자 지정 공급자](#custom-providers) 를 참조 하세요.

미들웨어는 `q` 압축 체계의 우선 순위를 지정 하기 위해 클라이언트에서 보낼 때 품질 값 (qvalue,) 가중치에 대응 시킬 수 있습니다. 자세한 내용은 [RFC 7231: 수락-인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4)을 참조 하세요.

압축 알고리즘은 압축 속도와 압축의 효율성 사이의 균형을 적용 합니다. 이 컨텍스트의 *효과* 는 압축 후 출력의 크기를 나타냅니다. 가장 작은 크기는 가장 *최적* 압축을 통해 달성 됩니다.

다음 표에서는 압축 된 콘텐츠 요청, 송신, 캐싱 및 수신에 관련 된 헤더에 대해 설명 합니다.

| header             | 역할 |
| ------------------ | ---- |
| `Accept-Encoding`  | 클라이언트에서 서버로 전송 되어 클라이언트에 허용 되는 콘텐츠 인코딩 스키마를 표시 합니다. |
| `Content-Encoding` | 페이로드에 있는 콘텐츠의 인코딩을 나타내기 위해 서버에서 클라이언트로 전송 됩니다. |
| `Content-Length`   | 압축이 발생 하면 `Content-Length` 응답이 압축 될 때 본문 내용이 변경 되므로 헤더가 제거 됩니다. |
| `Content-MD5`      | 압축이 발생 하면 `Content-MD5` 본문 내용이 변경 되 고 해시가 더 이상 유효 하지 않기 때문에 헤더가 제거 됩니다. |
| `Content-Type`     | 콘텐츠의 MIME 형식을 지정 합니다. 모든 응답은를 지정 해야 `Content-Type` 합니다. 미들웨어는이 값을 확인 하 여 응답이 압축 되어야 하는지 확인 합니다. 미들웨어는 인코딩할 수 있는 [기본 MIME 형식의](#mime-types) 집합을 지정 하지만 mime 형식을 바꾸거나 추가할 수 있습니다. |
| `Vary`             | 클라이언트와 프록시 값을 사용 하 여 서버에서 보내는 경우 `Accept-Encoding` `Vary` 헤더는 요청 헤더의 값을 기반으로 응답을 캐시 (vary) 해야 한다는 것을 클라이언트나 프록시에 나타냅니다 `Accept-Encoding` . 헤더를 사용 하 여 콘텐츠를 반환한 결과는 압축 된 응답과 압축 되지 않은 `Vary: Accept-Encoding` 응답이 모두 개별적으로 캐시 된다는 것입니다. |

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples)을 사용 하 여 응답 압축 미들웨어의 기능을 탐색 합니다. 샘플은 다음을 보여 줍니다.

* Gzip 및 사용자 지정 압축 공급자를 사용 하 여 앱 응답을 압축 합니다.
* Mime 형식을 압축을 위한 MIME 형식의 기본 목록에 추가 하는 방법입니다.

## <a name="package"></a>패키지

프로젝트에 미들웨어를 포함 하려면 [ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지를 포함 하는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 대 한 참조를 추가 합니다.

## <a name="configuration"></a>구성

다음 코드에서는 기본 MIME 형식 및 압축 공급자 ([Brotli](#brotli-compression-provider) 및 [Gzip](#gzip-compression-provider))에 대 한 응답 압축 미들웨어를 사용 하도록 설정 하는 방법을 보여 줍니다.

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

참고:

* `app.UseResponseCompression` 응답을 압축 하는 미들웨어 전에를 호출 해야 합니다. 자세한 내용은 <xref:fundamentals/middleware/index#middleware-order>를 참조하세요.
* [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/) 과 같은 도구를 사용 하 여 요청 헤더를 설정 하 `Accept-Encoding` 고 응답 헤더, 크기 및 본문을 연구 합니다.

헤더 없이 샘플 앱에 요청을 제출 `Accept-Encoding` 하 고 응답이 압축 되지 않은 상태 인지 확인 합니다. `Content-Encoding`및 `Vary` 헤더가 응답에 없습니다.

![Accept-Encoding 헤더가 없는 요청 결과를 표시 하는 Fiddler 창입니다. 응답이 압축 되지 않습니다.](response-compression/_static/request-uncompressed.png)

헤더 (Brotli 압축)를 사용 하 여 샘플 앱에 요청을 제출 하 `Accept-Encoding: br` 고 응답이 압축 되는지 확인 합니다. `Content-Encoding`및 `Vary` 헤더는 응답에 표시 됩니다.

![Accept-Encoding 헤더가 있는 요청 결과와 br 값을 보여 주는 Fiddler 창입니다. 응답에는 Vary 및 콘텐츠 인코딩 헤더가 추가 됩니다. 응답이 압축 됩니다.](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a>공급자

### <a name="brotli-compression-provider"></a>Brotli 압축 공급자

을 사용 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> 하 여 [Brotli 압축 된 데이터 형식의](https://tools.ietf.org/html/rfc7932)응답을 압축 합니다.

에 명시적으로 추가 된 압축 공급자가 없는 경우 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :

* Brotli 압축 공급자는 기본적으로 [Gzip 압축 공급자](#gzip-compression-provider)와 함께 압축 공급자 배열에 추가 됩니다.
* 클라이언트에서 Brotli 압축 데이터 형식이 지원 되는 경우 압축의 기본값은 Brotli 압축입니다. 클라이언트에서 Brotli을 지원 하지 않는 경우 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

압축 공급자가 명시적으로 추가 되 면 Brotli 압축 공급자를 추가 해야 합니다.

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

로 압축 수준을 설정 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> 합니다. Brotli 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며,이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다. 가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.

| 압축 수준 | 설명 |
| ----------------- | ----------- |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다. |
| [CompressionLevel 압축](xref:System.IO.Compression.CompressionLevel) | 압축을 수행할 수 없습니다. |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="gzip-compression-provider"></a>Gzip 압축 공급자

를 사용 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 하 여 [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952)으로 응답을 압축 합니다.

에 명시적으로 추가 된 압축 공급자가 없는 경우 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :

* Gzip 압축 공급자는 기본적으로 [Brotli 압축](#brotli-compression-provider)공급자와 함께 압축 공급자 배열에 추가 됩니다.
* 클라이언트에서 Brotli 압축 데이터 형식이 지원 되는 경우 압축의 기본값은 Brotli 압축입니다. 클라이언트에서 Brotli을 지원 하지 않는 경우 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

모든 압축 공급자가 명시적으로 추가 되 면 Gzip 압축 공급자를 추가 해야 합니다.

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

로 압축 수준을 설정 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 합니다. Gzip 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다. 가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.

| 압축 수준 | 설명 |
| ----------------- | ----------- |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다. |
| [CompressionLevel 압축](xref:System.IO.Compression.CompressionLevel) | 압축을 수행할 수 없습니다. |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>사용자 지정 공급자

를 사용 하 여 사용자 지정 압축 구현을 만듭니다 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> . 는 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> 이가 생성 하는 콘텐츠 인코딩을 나타냅니다 `ICompressionProvider` . 미들웨어는이 정보를 사용 하 여 요청의 헤더에 지정 된 목록에 따라 공급자를 선택 합니다 `Accept-Encoding` .

클라이언트는 샘플 앱을 사용 하 여 헤더를 사용 하 여 요청을 제출 합니다 `Accept-Encoding: mycustomcompression` . 미들웨어는 사용자 지정 압축 구현을 사용 하 고 헤더를 사용 하 여 응답을 반환 합니다 `Content-Encoding: mycustomcompression` . 클라이언트는 사용자 지정 압축 구현이 작동 하기 위해 사용자 지정 인코딩의 압축을 해제할 수 있어야 합니다.

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

헤더를 사용 하 여 샘플 앱에 요청을 제출 `Accept-Encoding: mycustomcompression` 하 고 응답 헤더를 관찰 합니다. `Vary`및 `Content-Encoding` 헤더는 응답에 표시 됩니다. 응답 본문 (표시 되지 않음)은 샘플에 의해 압축 되지 않습니다. 샘플의 클래스에는 압축 구현이 없습니다 `CustomCompressionProvider` . 그러나이 샘플에서는 이러한 압축 알고리즘을 구현 하는 위치를 보여 줍니다.

![Accept-Encoding 헤더가 있는 요청 결과와 mycustomcompression 값이 표시 된 Fiddler 창 응답에는 Vary 및 콘텐츠 인코딩 헤더가 추가 됩니다.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME 형식

미들웨어는 압축을 위한 기본 MIME 형식 집합을 지정 합니다.

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

MIME 형식을 응답 압축 미들웨어 옵션으로 바꾸거나 추가 합니다. 와 같은 와일드 카드 MIME 형식은 `text/*` 지원 되지 않습니다. 샘플 앱은에 대 한 MIME 형식을 추가 하 고 `image/svg+xml` 압축 하 고 ASP.NET Core 배너 이미지 (*배너*)를 사용 합니다.

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a>보안 프로토콜을 사용한 압축

보안 연결을 통한 압축 된 응답 `EnableForHttps` 은 기본적으로 사용 하지 않도록 설정 된 옵션을 사용 하 여 제어할 수 있습니다. 동적으로 생성 된 페이지에서 압축을 사용 하면 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격과 같은 보안 문제가 발생할 수 있습니다.

## <a name="adding-the-vary-header"></a>Vary 헤더 추가

헤더를 기반으로 응답을 압축 하는 경우 `Accept-Encoding` 여러 압축 버전의 응답 및 압축 되지 않은 버전이 있습니다. 여러 버전이 존재 하 고 저장 해야 함을 클라이언트 및 프록시 캐시에 지시 하기 위해 헤더는 `Vary` 값을 사용 하 여 추가 됩니다 `Accept-Encoding` . ASP.NET Core 2.0 이상에서 미들웨어는 `Vary` 응답이 압축 될 때 헤더를 자동으로 추가 합니다.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Nginx 역방향 프록시 뒤에 있는 미들웨어 문제

Nginx에서 요청을 프록시 하는 경우 `Accept-Encoding` 헤더가 제거 됩니다. 헤더를 제거 하면 `Accept-Encoding` 미들웨어가 응답을 압축 하지 않습니다. 자세한 내용은 [NGINX: 압축 및](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)압축 해제를 참조 하세요. 이 문제는 [Nginx (aspnet/BasicMiddleware #123)에 대 한 통과 압축](https://github.com/aspnet/BasicMiddleware/issues/123)을 통해 추적 됩니다.

## <a name="working-with-iis-dynamic-compression"></a>IIS 동적 압축 사용

앱에 대해 사용 하지 않도록 설정할 서버 수준에서 활성 IIS 동적 압축 모듈이 구성 된 경우 *web.config* 파일을 추가 하 여 모듈을 사용 하지 않도록 설정 합니다. 보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.

## <a name="troubleshooting"></a>문제 해결

[Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 하 여 요청 헤더를 설정 하 `Accept-Encoding` 고 응답 헤더, 크기 및 본문을 검토할 수 있습니다. 기본적으로 응답 압축 미들웨어는 다음 조건을 충족 하는 응답을 압축 합니다.

* `Accept-Encoding`헤더는 `br` `gzip` `*` 설정 된 사용자 지정 압축 공급자와 일치 하는,, 또는 사용자 지정 인코딩의 값을 포함 합니다. 값 `identity` 이 이거나, 값이 0 인 품질 값 (qvalue, `q` )이 설정 되어 있지 않아야 합니다.
* MIME 형식 ( `Content-Type` )을 설정 하 고에 구성 된 mime 형식과 일치 해야 합니다 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> .
* 요청은 헤더를 포함 하지 않아야 합니다 `Content-Range` .
* 응답 압축 미들웨어 옵션에 보안 프로토콜 (https)이 구성 되지 않은 경우 요청은 안전 하지 않은 프로토콜 (http)을 사용 해야 합니다. *보안 콘텐츠 압축을 사용 하도록 설정할 때 [위에서 설명한](#compression-with-secure-protocol) 위험에 유의 하십시오.*

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla 개발자 네트워크: 수락-인코딩](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 섹션 3.1.2.1: Content Codings](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 섹션 4.2.3: Gzip 코딩](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP 파일 형식 사양 버전 4.3](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

네트워크 대역폭은 제한 된 리소스입니다. 응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 향상 됩니다. 페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축 하는 것입니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="when-to-use-response-compression-middleware"></a>응답 압축 미들웨어를 사용 하는 경우

IIS, Apache 또는 Nginx에서 서버 기반 응답 압축 기술을 사용 합니다. 미들웨어의 성능은 서버 모듈의 성능과 일치 하지 않을 수 있습니다. [HTTP.sys 서버](xref:fundamentals/servers/httpsys) 서버와 [kestrel](xref:fundamentals/servers/kestrel) 서버는 현재 기본 제공 압축 지원을 제공 하지 않습니다.

응답 압축 미들웨어를 사용 하는 경우:

* 다음 서버 기반 압축 기술을 사용할 수 없습니다.
  * [IIS 동적 압축 모듈](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate 모듈](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx 압축 및 압축 풀기](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* 에서 직접 호스팅:
  * [HTTP.sys 서버](xref:fundamentals/servers/httpsys) (이전에는 weblistener 라고 함)
  * [Kestrel 서버](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>응답 압축

일반적으로 기본적으로 압축 되지 않은 모든 응답은 응답 압축의 이점을 누릴 수 있습니다. 기본적으로 압축 되지 않은 응답은 일반적으로 CSS, JavaScript, HTML, XML 및 JSON을 포함 합니다. PNG 파일과 같이 기본적으로 압축 된 자산은 압축 해서는 안 됩니다. 고유 하 게 압축 된 응답을 추가로 압축 하려는 경우 크기 및 전송 시간의 작은 추가 감소는 압축을 처리 하는 데 걸린 시간을 overshadowed 수 있습니다. 파일의 내용과 압축 효율성에 따라 약 150-1000 바이트 보다 작은 파일은 압축 하지 않습니다. 작은 파일을 압축 하는 오버 헤드로 인해 압축 되지 않은 파일 보다 큰 압축 된 파일이 생성 될 수 있습니다.

클라이언트가 압축 된 콘텐츠를 처리할 수 있는 경우 클라이언트는 요청과 함께 헤더를 전송 하 여 해당 기능을 서버에 알려야 합니다 `Accept-Encoding` . 서버는 압축 된 콘텐츠를 보낼 때 `Content-Encoding` 압축 된 응답의 인코딩 방법에 대 한 정보를 헤더에 포함 해야 합니다. 미들웨어에서 지 원하는 콘텐츠 인코딩 명칭은 다음 표에 나와 있습니다.

| `Accept-Encoding` 헤더 값 | 미들웨어 지원 | 설명 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 아니요                   | [Brotli 압축 된 데이터 형식](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | No                   | [DEFLATE 압축 데이터 형식](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | No                   | [W3C 효율적인 XML 교환](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | 예(‘기본값’)        | [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Yes                  | "인코딩 안 함" 식별자: 응답은 인코딩되지 않아야 합니다. |
| `pack200-gzip`                  | No                   | [Java 보관을 위한 네트워크 전송 형식](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Yes                  | 명시적으로 요청 되지 않은 모든 사용 가능한 콘텐츠 인코딩입니다. |

자세한 내용은 [IANA 공식 콘텐츠 코딩 목록](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)을 참조 하세요.

미들웨어를 사용 하면 사용자 지정 헤더 값에 대 한 추가 압축 공급자를 추가할 수 있습니다 `Accept-Encoding` . 자세한 내용은 아래의 [사용자 지정 공급자](#custom-providers) 를 참조 하세요.

미들웨어는 `q` 압축 체계의 우선 순위를 지정 하기 위해 클라이언트에서 보낼 때 품질 값 (qvalue,) 가중치에 대응 시킬 수 있습니다. 자세한 내용은 [RFC 7231: 수락-인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4)을 참조 하세요.

압축 알고리즘은 압축 속도와 압축의 효율성 사이의 균형을 적용 합니다. 이 컨텍스트의 *효과* 는 압축 후 출력의 크기를 나타냅니다. 가장 작은 크기는 가장 *최적* 압축을 통해 달성 됩니다.

다음 표에서는 압축 된 콘텐츠 요청, 송신, 캐싱 및 수신에 관련 된 헤더에 대해 설명 합니다.

| header             | 역할 |
| ------------------ | ---- |
| `Accept-Encoding`  | 클라이언트에서 서버로 전송 되어 클라이언트에 허용 되는 콘텐츠 인코딩 스키마를 표시 합니다. |
| `Content-Encoding` | 페이로드에 있는 콘텐츠의 인코딩을 나타내기 위해 서버에서 클라이언트로 전송 됩니다. |
| `Content-Length`   | 압축이 발생 하면 `Content-Length` 응답이 압축 될 때 본문 내용이 변경 되므로 헤더가 제거 됩니다. |
| `Content-MD5`      | 압축이 발생 하면 `Content-MD5` 본문 내용이 변경 되 고 해시가 더 이상 유효 하지 않기 때문에 헤더가 제거 됩니다. |
| `Content-Type`     | 콘텐츠의 MIME 형식을 지정 합니다. 모든 응답은를 지정 해야 `Content-Type` 합니다. 미들웨어는이 값을 확인 하 여 응답이 압축 되어야 하는지 확인 합니다. 미들웨어는 인코딩할 수 있는 [기본 MIME 형식의](#mime-types) 집합을 지정 하지만 mime 형식을 바꾸거나 추가할 수 있습니다. |
| `Vary`             | 클라이언트와 프록시 값을 사용 하 여 서버에서 보내는 경우 `Accept-Encoding` `Vary` 헤더는 요청 헤더의 값을 기반으로 응답을 캐시 (vary) 해야 한다는 것을 클라이언트나 프록시에 나타냅니다 `Accept-Encoding` . 헤더를 사용 하 여 콘텐츠를 반환한 결과는 압축 된 응답과 압축 되지 않은 `Vary: Accept-Encoding` 응답이 모두 개별적으로 캐시 된다는 것입니다. |

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples)을 사용 하 여 응답 압축 미들웨어의 기능을 탐색 합니다. 샘플은 다음을 보여 줍니다.

* Gzip 및 사용자 지정 압축 공급자를 사용 하 여 앱 응답을 압축 합니다.
* Mime 형식을 압축을 위한 MIME 형식의 기본 목록에 추가 하는 방법입니다.

## <a name="package"></a>패키지

프로젝트에 미들웨어를 포함 하려면 [ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지를 포함 하는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 대 한 참조를 추가 합니다.

## <a name="configuration"></a>구성

다음 코드에서는 기본 MIME 형식 및 [Gzip 압축 공급자](#gzip-compression-provider)에 대해 응답 압축 미들웨어를 사용 하도록 설정 하는 방법을 보여 줍니다.

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

참고:

* `app.UseResponseCompression` 응답을 압축 하는 미들웨어 전에를 호출 해야 합니다. 자세한 내용은 <xref:fundamentals/middleware/index#middleware-order>를 참조하세요.
* [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/) 과 같은 도구를 사용 하 여 요청 헤더를 설정 하 `Accept-Encoding` 고 응답 헤더, 크기 및 본문을 연구 합니다.

헤더 없이 샘플 앱에 요청을 제출 `Accept-Encoding` 하 고 응답이 압축 되지 않은 상태 인지 확인 합니다. `Content-Encoding`및 `Vary` 헤더가 응답에 없습니다.

![Accept-Encoding 헤더가 없는 요청 결과를 표시 하는 Fiddler 창입니다. 응답이 압축 되지 않습니다.](response-compression/_static/request-uncompressed.png)

헤더를 사용 하 여 샘플 앱에 요청을 제출 하 `Accept-Encoding: gzip` 고 응답이 압축 되는지 확인 합니다. `Content-Encoding`및 `Vary` 헤더는 응답에 표시 됩니다.

![Accept-Encoding 헤더가 있는 요청 결과와 gzip 값을 보여 주는 Fiddler 창입니다. 응답에는 Vary 및 콘텐츠 인코딩 헤더가 추가 됩니다. 응답이 압축 됩니다.](response-compression/_static/request-compressed.png)

## <a name="providers"></a>공급자

### <a name="gzip-compression-provider"></a>Gzip 압축 공급자

를 사용 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 하 여 [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952)으로 응답을 압축 합니다.

에 명시적으로 추가 된 압축 공급자가 없는 경우 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :

* Gzip 압축 공급자는 기본적으로 압축 공급자 배열에 추가 됩니다.
* 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

모든 압축 공급자가 명시적으로 추가 되 면 Gzip 압축 공급자를 추가 해야 합니다.

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

로 압축 수준을 설정 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 합니다. Gzip 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다. 가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.

| 압축 수준 | 설명 |
| ----------------- | ----------- |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다. |
| [CompressionLevel 압축](xref:System.IO.Compression.CompressionLevel) | 압축을 수행할 수 없습니다. |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>사용자 지정 공급자

를 사용 하 여 사용자 지정 압축 구현을 만듭니다 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> . 는 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> 이가 생성 하는 콘텐츠 인코딩을 나타냅니다 `ICompressionProvider` . 미들웨어는이 정보를 사용 하 여 요청의 헤더에 지정 된 목록에 따라 공급자를 선택 합니다 `Accept-Encoding` .

클라이언트는 샘플 앱을 사용 하 여 헤더를 사용 하 여 요청을 제출 합니다 `Accept-Encoding: mycustomcompression` . 미들웨어는 사용자 지정 압축 구현을 사용 하 고 헤더를 사용 하 여 응답을 반환 합니다 `Content-Encoding: mycustomcompression` . 클라이언트는 사용자 지정 압축 구현이 작동 하기 위해 사용자 지정 인코딩의 압축을 해제할 수 있어야 합니다.

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

헤더를 사용 하 여 샘플 앱에 요청을 제출 `Accept-Encoding: mycustomcompression` 하 고 응답 헤더를 관찰 합니다. `Vary`및 `Content-Encoding` 헤더는 응답에 표시 됩니다. 응답 본문 (표시 되지 않음)은 샘플에 의해 압축 되지 않습니다. 샘플의 클래스에는 압축 구현이 없습니다 `CustomCompressionProvider` . 그러나이 샘플에서는 이러한 압축 알고리즘을 구현 하는 위치를 보여 줍니다.

![Accept-Encoding 헤더가 있는 요청 결과와 mycustomcompression 값이 표시 된 Fiddler 창 응답에는 Vary 및 콘텐츠 인코딩 헤더가 추가 됩니다.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME 형식

미들웨어는 압축을 위한 기본 MIME 형식 집합을 지정 합니다.

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

MIME 형식을 응답 압축 미들웨어 옵션으로 바꾸거나 추가 합니다. 와 같은 와일드 카드 MIME 형식은 `text/*` 지원 되지 않습니다. 샘플 앱은에 대 한 MIME 형식을 추가 하 고 `image/svg+xml` 압축 하 고 ASP.NET Core 배너 이미지 (*배너*)를 사용 합니다.

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a>보안 프로토콜을 사용한 압축

보안 연결을 통한 압축 된 응답 `EnableForHttps` 은 기본적으로 사용 하지 않도록 설정 된 옵션을 사용 하 여 제어할 수 있습니다. 동적으로 생성 된 페이지에서 압축을 사용 하면 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격과 같은 보안 문제가 발생할 수 있습니다.

## <a name="adding-the-vary-header"></a>Vary 헤더 추가

헤더를 기반으로 응답을 압축 하는 경우 `Accept-Encoding` 여러 압축 버전의 응답 및 압축 되지 않은 버전이 있습니다. 여러 버전이 존재 하 고 저장 해야 함을 클라이언트 및 프록시 캐시에 지시 하기 위해 헤더는 `Vary` 값을 사용 하 여 추가 됩니다 `Accept-Encoding` . ASP.NET Core 2.0 이상에서 미들웨어는 `Vary` 응답이 압축 될 때 헤더를 자동으로 추가 합니다.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Nginx 역방향 프록시 뒤에 있는 미들웨어 문제

Nginx에서 요청을 프록시 하는 경우 `Accept-Encoding` 헤더가 제거 됩니다. 헤더를 제거 하면 `Accept-Encoding` 미들웨어가 응답을 압축 하지 않습니다. 자세한 내용은 [NGINX: 압축 및](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)압축 해제를 참조 하세요. 이 문제는 [Nginx (aspnet/BasicMiddleware #123)에 대 한 통과 압축](https://github.com/aspnet/BasicMiddleware/issues/123)을 통해 추적 됩니다.

## <a name="working-with-iis-dynamic-compression"></a>IIS 동적 압축 사용

앱에 대해 사용 하지 않도록 설정할 서버 수준에서 활성 IIS 동적 압축 모듈이 구성 된 경우 *web.config* 파일을 추가 하 여 모듈을 사용 하지 않도록 설정 합니다. 보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.

## <a name="troubleshooting"></a>문제 해결

[Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 하 여 요청 헤더를 설정 하 `Accept-Encoding` 고 응답 헤더, 크기 및 본문을 검토할 수 있습니다. 기본적으로 응답 압축 미들웨어는 다음 조건을 충족 하는 응답을 압축 합니다.

* `Accept-Encoding`헤더는 `gzip` `*` 설정 된 사용자 지정 압축 공급자와 일치 하는, 또는 사용자 지정 인코딩의 값을 포함 합니다. 값 `identity` 이 이거나, 값이 0 인 품질 값 (qvalue, `q` )이 설정 되어 있지 않아야 합니다.
* MIME 형식 ( `Content-Type` )을 설정 하 고에 구성 된 mime 형식과 일치 해야 합니다 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> .
* 요청은 헤더를 포함 하지 않아야 합니다 `Content-Range` .
* 응답 압축 미들웨어 옵션에 보안 프로토콜 (https)이 구성 되지 않은 경우 요청은 안전 하지 않은 프로토콜 (http)을 사용 해야 합니다. *보안 콘텐츠 압축을 사용 하도록 설정할 때 [위에서 설명한](#compression-with-secure-protocol) 위험에 유의 하십시오.*

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla 개발자 네트워크: 수락-인코딩](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 섹션 3.1.2.1: Content Codings](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 섹션 4.2.3: Gzip 코딩](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP 파일 형식 사양 버전 4.3](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
