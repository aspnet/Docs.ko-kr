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
ms.openlocfilehash: 780250feab456fa3eedee4e023c9bc774e748291
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253874"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="14ded-103">ASP.NET Core Kestrel 웹 서버의 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="14ded-103">Configure endpoints for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="14ded-104">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-104">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="14ded-105">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="14ded-105">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="14ded-106">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-106">Specify URLs using the:</span></span>

* <span data-ttu-id="14ded-107">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="14ded-107">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="14ded-108">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="14ded-108">`--urls` command-line argument.</span></span>
* <span data-ttu-id="14ded-109">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="14ded-109">`urls` host configuration key.</span></span>
* <span data-ttu-id="14ded-110">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="14ded-110">`UseUrls` extension method.</span></span>

<span data-ttu-id="14ded-111">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-111">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="14ded-112">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="14ded-112">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="14ded-113">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14ded-113">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="14ded-114">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-114">A development certificate is created:</span></span>

* <span data-ttu-id="14ded-115">[.NET SDK](/dotnet/core/sdk)가 설치된 경우.</span><span class="sxs-lookup"><span data-stu-id="14ded-115">When the [.NET SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="14ded-116">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-116">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="14ded-117">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-117">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="14ded-118">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-118">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="14ded-119"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-119">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="14ded-120">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="14ded-120">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="14ded-121">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="14ded-121">`KestrelServerOptions` configuration:</span></span>

## <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="14ded-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="14ded-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="14ded-123">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-123">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="14ded-124">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-124">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="14ded-125"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-125">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> won't have the defaults applied.</span></span>

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="14ded-126">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="14ded-126">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="14ded-127">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-127">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="14ded-128">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-128">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="14ded-129"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A>를 호출하기 **전에** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>을 호출하여 생성된 엔드포인트는 기본값이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-129">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> won't have the defaults applied.</span></span>

## <a name="configureiconfiguration"></a><span data-ttu-id="14ded-130">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="14ded-130">Configure(IConfiguration)</span></span>

<span data-ttu-id="14ded-131">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-131">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="14ded-132">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-132">The configuration must be scoped to the configuration section for Kestrel.</span></span>

<span data-ttu-id="14ded-133">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-133">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

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

## <a name="listenoptionsusehttps"></a><span data-ttu-id="14ded-134">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="14ded-134">ListenOptions.UseHttps</span></span>

<span data-ttu-id="14ded-135">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-135">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="14ded-136">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="14ded-136">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="14ded-137">`UseHttps`: 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-137">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="14ded-138">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-138">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="14ded-139">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="14ded-139">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="14ded-140">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-140">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="14ded-141">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-141">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="14ded-142">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-142">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="14ded-143">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-143">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="14ded-144">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-144">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="14ded-145">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-145">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="14ded-146">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-146">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="14ded-147">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-147">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="14ded-148">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-148">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="14ded-149">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-149">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="14ded-150">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-150">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="14ded-151">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-151">Supported configurations described next:</span></span>

* <span data-ttu-id="14ded-152">구성 없음</span><span class="sxs-lookup"><span data-stu-id="14ded-152">No configuration</span></span>
* <span data-ttu-id="14ded-153">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="14ded-153">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="14ded-154">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="14ded-154">Change the defaults in code</span></span>

### <a name="no-configuration"></a><span data-ttu-id="14ded-155">구성 없음</span><span class="sxs-lookup"><span data-stu-id="14ded-155">No configuration</span></span>

<span data-ttu-id="14ded-156">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="14ded-156">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a><span data-ttu-id="14ded-157">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="14ded-157">Replace the default certificate from configuration</span></span>

<span data-ttu-id="14ded-158">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-158">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="14ded-159">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-159">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="14ded-160">다음 *appsettings.json* 예제에서</span><span class="sxs-lookup"><span data-stu-id="14ded-160">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="14ded-161">잘못된 인증서 사용을 허가하려면 `AllowInvalid`를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="14ded-161">Set `AllowInvalid` to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="14ded-162">인증서를 지정하지 않은 임의의 HTTPS 엔드포인트(다음 예제에서 `HttpsDefaultCert`)는 `Certificates` > `Default`에서 정의된 인증서 또는 개발 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-162">Any HTTPS endpoint that doesn't specify a certificate (`HttpsDefaultCert` in the example that follows) falls back to the cert defined under `Certificates` > `Default` or the development certificate.</span></span>

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

<span data-ttu-id="14ded-163">모든 인증서 노드에 대해 `Path` 및 `Password`를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-163">An alternative to using `Path` and `Password` for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="14ded-164">예를 들어, `Certificates` > `Default` 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-164">For example, the `Certificates` > `Default` certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="14ded-165">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="14ded-165">Schema notes:</span></span>

* <span data-ttu-id="14ded-166">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-166">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="14ded-167">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-167">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="14ded-168">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-168">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="14ded-169">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-169">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="14ded-170">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-170">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="14ded-171">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-171">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="14ded-172">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-172">The `Certificate` section is optional.</span></span> <span data-ttu-id="14ded-173">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-173">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="14ded-174">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-174">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="14ded-175">`Certificate` 섹션은 `Path`&ndash;`Password` 및 `Subject`&ndash;`Store` 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-175">The `Certificate` section supports both `Path`&ndash;`Password` and `Subject`&ndash;`Store` certificates.</span></span>
* <span data-ttu-id="14ded-176">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-176">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="14ded-177">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-177">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="14ded-178">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-178">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span>

* <span data-ttu-id="14ded-179">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-179">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="14ded-180">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-180">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="14ded-181">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-181">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="14ded-182">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-182">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="14ded-183">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-183">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="14ded-184">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-184">These overloads don't use names and only consume default settings from configuration.</span></span>

### <a name="change-the-defaults-in-code"></a><span data-ttu-id="14ded-185">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="14ded-185">Change the defaults in code</span></span>

<span data-ttu-id="14ded-186">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-186">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="14ded-187">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-187">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

## <a name="configure-endpoints-using-server-name-indication"></a><span data-ttu-id="14ded-188">서버 이름 표시를 사용하여 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="14ded-188">Configure endpoints using Server Name Indication</span></span>

<span data-ttu-id="14ded-189">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-189">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="14ded-190">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-190">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="14ded-191">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-191">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="14ded-192">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-192">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="14ded-193">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-193">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span> <span data-ttu-id="14ded-194">다음 콜백 코드는 프로젝트 *Program.cs* 파일의 `ConfigureWebHostDefaults` 메서드 호출에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-194">The following callback code can be used in the `ConfigureWebHostDefaults` method call of a project's *Program.cs* file:</span></span>

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

<span data-ttu-id="14ded-195">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-195">SNI support requires:</span></span>

* <span data-ttu-id="14ded-196">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-196">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="14ded-197">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-197">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="14ded-198">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-198">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="14ded-199">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-199">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="14ded-200">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-200">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

## <a name="connection-logging"></a><span data-ttu-id="14ded-201">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="14ded-201">Connection logging</span></span>

<span data-ttu-id="14ded-202"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-202">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="14ded-203">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-203">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="14ded-204">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-204">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="14ded-205">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-205">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span> <span data-ttu-id="14ded-206">이는 기본 제공 [연결 미들웨어](#connection-middleware)입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-206">This is built-in [Connection Middleware](#connection-middleware).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="14ded-207">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="14ded-207">Bind to a TCP socket</span></span>

<span data-ttu-id="14ded-208"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-208">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="14ded-209">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-209">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="14ded-210">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-210">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a><span data-ttu-id="14ded-211">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="14ded-211">Bind to a Unix socket</span></span>

<span data-ttu-id="14ded-212">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-212">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="14ded-213">Nginx 구성 파일에서 `server` > `location` > `proxy_pass` 항목을 `http://unix:/tmp/{KESTREL SOCKET}:/;`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-213">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="14ded-214">`{KESTREL SOCKET}`은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A>에 제공된 소켓의 이름입니다(예: 이전 예제의 `kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="14ded-214">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="14ded-215">소켓이 Nginx에서 쓰기 가능한지 확인합니다(예: `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="14ded-215">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

## <a name="port-0"></a><span data-ttu-id="14ded-216">포트 0</span><span class="sxs-lookup"><span data-stu-id="14ded-216">Port 0</span></span>

<span data-ttu-id="14ded-217">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-217">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="14ded-218">다음 예제에서는 Kestrel이 런타임에 바인딩된 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-218">The following example shows how to determine which port Kestrel bound at runtime:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="14ded-219">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-219">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a><span data-ttu-id="14ded-220">제한 사항</span><span class="sxs-lookup"><span data-stu-id="14ded-220">Limitations</span></span>

<span data-ttu-id="14ded-221">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-221">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* <span data-ttu-id="14ded-222">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="14ded-222">`--urls` command-line argument</span></span>
* <span data-ttu-id="14ded-223">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="14ded-223">`urls` host configuration key</span></span>
* <span data-ttu-id="14ded-224">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="14ded-224">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="14ded-225">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-225">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="14ded-226">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="14ded-226">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="14ded-227">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 문서의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="14ded-227">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this article).</span></span>
* <span data-ttu-id="14ded-228">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-228">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

## <a name="iis-endpoint-configuration"></a><span data-ttu-id="14ded-229">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="14ded-229">IIS endpoint configuration</span></span>

<span data-ttu-id="14ded-230">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-230">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="14ded-231">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14ded-231">For more information, see [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

## <a name="listenoptionsprotocols"></a><span data-ttu-id="14ded-232">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="14ded-232">ListenOptions.Protocols</span></span>

<span data-ttu-id="14ded-233">`Protocols` 속성은 연결 엔드포인트 또는 서버에 대해 사용할 수 있는 HTTP 프로토콜(`HttpProtocols`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-233">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="14ded-234">`HttpProtocols` 열거형의 `Protocols` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-234">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="14ded-235">`HttpProtocols` 열거형 값</span><span class="sxs-lookup"><span data-stu-id="14ded-235">`HttpProtocols` enum value</span></span> | <span data-ttu-id="14ded-236">허용되는 연결 프로토콜</span><span class="sxs-lookup"><span data-stu-id="14ded-236">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="14ded-237">HTTP/1.1 전용.</span><span class="sxs-lookup"><span data-stu-id="14ded-237">HTTP/1.1 only.</span></span> <span data-ttu-id="14ded-238">TLS와 함께 또는 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-238">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="14ded-239">HTTP/2 전용.</span><span class="sxs-lookup"><span data-stu-id="14ded-239">HTTP/2 only.</span></span> <span data-ttu-id="14ded-240">클라이언트가 [이전 기술 모드](https://tools.ietf.org/html/rfc7540#section-3.4)를 지원하는 경우에만 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-240">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="14ded-241">HTTP/1.1 및 HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="14ded-241">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="14ded-242">HTTP/2를 사용하려면 클라이언트가 TLS [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 핸드셰이크에서 HTTP/2를 선택해야 합니다. 그렇지 않으면 연결 기본값은 HTTP/1.1입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-242">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="14ded-243">모든 엔드포인트의 기본 `ListenOptions.Protocols` 값은 `HttpProtocols.Http1AndHttp2`입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-243">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="14ded-244">HTTP/2에 대한 TLS 제한 사항:</span><span class="sxs-lookup"><span data-stu-id="14ded-244">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="14ded-245">TLS 버전 1.2 이상</span><span class="sxs-lookup"><span data-stu-id="14ded-245">TLS version 1.2 or later</span></span>
* <span data-ttu-id="14ded-246">재협상 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="14ded-246">Renegotiation disabled</span></span>
* <span data-ttu-id="14ded-247">압축 사용 안함</span><span class="sxs-lookup"><span data-stu-id="14ded-247">Compression disabled</span></span>
* <span data-ttu-id="14ded-248">최소 임시 키 교환 크기:</span><span class="sxs-lookup"><span data-stu-id="14ded-248">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="14ded-249">ECDHE(타원 곡선 Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 최소 224비트</span><span class="sxs-lookup"><span data-stu-id="14ded-249">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="14ded-250">DHE(유한체 Diffie-Hellman) &lbrack;`TLS12`&rbrack;: 최소 2048비트</span><span class="sxs-lookup"><span data-stu-id="14ded-250">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="14ded-251">암호 도구 모음은 금지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-251">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="14ded-252">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;(P-256 타원 곡선 &lbrack;`FIPS186`&rbrack; 포함)는 기본적으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-252">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="14ded-253">다음 예제는 포트 8000에서 HTTP/1.1 및 HTTP/2 연결을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-253">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="14ded-254">연결은 제공된 인증서를 사용하여 TLS로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-254">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="14ded-255">Linux에서 <xref:System.Net.Security.CipherSuitesPolicy>를 사용하여 각 연결을 기준으로 TLS 핸드셰이크를 필터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-255">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

## <a name="connection-middleware"></a><span data-ttu-id="14ded-256">연결 미들웨어</span><span class="sxs-lookup"><span data-stu-id="14ded-256">Connection Middleware</span></span>

<span data-ttu-id="14ded-257">필요할 경우 사용자 지정 연결 미들웨어를 사용하여 각 연결을 기준으로 특정 암호에 대한 TLS 핸드셰이크를 필터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-257">Custom connection middleware can filter TLS handshakes on a per-connection basis for specific ciphers if necessary.</span></span>

<span data-ttu-id="14ded-258">다음 예는 앱에서 지원하지 않는 모든 암호화 알고리즘에 대해 <xref:System.NotSupportedException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-258">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="14ded-259">또는 [ITlsHandshakeFeature CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm)을 정의하고 허용되는 암호 그룹 목록과 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-259">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="14ded-260">[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 암호화 알고리즘과 함께 사용되는 암호화는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-260">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="14ded-261"><xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> 람다를 통해 연결 필터링을 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-261">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

## <a name="set-the-http-protocol-from-configuration"></a><span data-ttu-id="14ded-262">구성에서 HTTP 프로토콜 설정</span><span class="sxs-lookup"><span data-stu-id="14ded-262">Set the HTTP protocol from configuration</span></span>

<span data-ttu-id="14ded-263">`CreateDefaultBuilder`는 기본적으로 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-263">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="14ded-264">다음 *appsettings.json* 예제에서는 HTTP/1.1을 모든 엔드포인트의 기본 연결 프로토콜로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-264">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="14ded-265">다음 *appsettings.json* 예제에서는 특정 엔드포인트의 HTTP/1.1 연결 프로토콜을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-265">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="14ded-266">코드에서 지정한 프로토콜이 구성에서 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-266">Protocols specified in code override values set by configuration.</span></span>

## <a name="url-prefixes"></a><span data-ttu-id="14ded-267">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="14ded-267">URL prefixes</span></span>

<span data-ttu-id="14ded-268">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-268">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="14ded-269">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-269">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="14ded-270">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-270">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="14ded-271">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="14ded-271">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="14ded-272">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-272">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="14ded-273">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="14ded-273">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="14ded-274">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-274">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="14ded-275">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="14ded-275">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="14ded-276">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-276">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="14ded-277">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-277">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="14ded-278">다양한 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-278">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server.</span></span> <span data-ttu-id="14ded-279">역방향 프록시 서버의 예로는 IIS, Nginx 또는 Apache가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-279">Reverse proxy server examples include IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="14ded-280">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](xref:fundamentals/servers/kestrel/host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-280">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

* <span data-ttu-id="14ded-281">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="14ded-281">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="14ded-282">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-282">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="14ded-283">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-283">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="14ded-284">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="14ded-284">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>
