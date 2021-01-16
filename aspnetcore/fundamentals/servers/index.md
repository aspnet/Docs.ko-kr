---
title: ASP.NET Core의 웹 서버 구현
author: rick-anderson
description: ASP.NET Core의 웹 서버 Kestrel 및 HTTP.sys를 알아봅니다. 서버를 선택하는 방법 및 역방향 프록시 서버를 사용하는 시기에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 49e299ed00ea0e5d54c1ba795971da379cd5b695
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253139"
---
# <a name="web-server-implementations-in-aspnet-core"></a>ASP.NET Core의 웹 서버 구현

작성자: [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) 및 [Chris Ross](https://github.com/Tratcher)

ASP.NET Core 앱은 In-Process HTTP 서버 구현을 사용하여 실행됩니다. 서버 구현은 HTTP 요청을 수신하고 <xref:Microsoft.AspNetCore.Http.HttpContext>에 구성된 일련의 [요청 기능](xref:fundamentals/request-features)으로 앱에 표시합니다.

## <a name="kestrel"></a>Kestrel

Kestrel은 ASP.NET Core 프로젝트 템플릿에서 지정된 기본 웹 서버입니다.

다음과 같이 Kestrel을 사용할 수 있습니다.

* 인터넷을 포함한 네트워크의 요청을 직접 처리하는 에지 서버로 단독 사용합니다.

  ![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

* [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/) 같은 *역방향 프록시 서버* 와 함께 사용합니다. 역방향 프록시 서버는 인터넷에서 HTTP 요청을 받아서 Kestrel에 전달합니다.

  ![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

&mdash;역방향 프록시 서버가 있는 구성과 없는 구성 모두&mdash; 지원되는 호스팅 구성입니다.

Kestrel을 역방향 프록시 구성에서 사용하는 경우에 대한 Kestrel 구성 지침 및 정보는 <xref:fundamentals/servers/kestrel>을 참조하세요.

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core는 다음과 함께 제공됩니다.

* [Kestrel 서버](xref:fundamentals/servers/kestrel)는 기본 플랫폼 간 HTTP 서버 구현입니다.
* IIS HTTP 서버는 IIS의 [In-Process 서버](#hosting-models)입니다.
* [HTTP.sys 서버](xref:fundamentals/servers/httpsys)는 [Http.Sys 커널 드라이버 및 HTTP Server API](/windows/desktop/Http/http-api-start-page)를 기반으로 하는 Windows 전용 HTTP 서버입니다.

[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 사용하는 경우 앱은 다음 중 하나에서 실행됩니다.

* IIS 작업자 프로세스와 동일한 프로세스에서 IIS HTTP 서버를 사용하여 실행됩니다([In-Process 호스팅 모델](#hosting-models)). *In-Process* 가 권장되는 구성입니다.
* IIS 작업자 프로세스와 다른 별도의 프로세스에서 [Kestrel 서버](#kestrel)를 사용하여 실행됩니다([Out-of-Process 호스팅 모델](#hosting-models)).

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 IIS와 In-Process IIS HTTP 서버 또는 Kestrel 간의 네이티브 IIS 요청을 처리하는 네이티브 IIS 모듈입니다. 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.

## <a name="hosting-models"></a>호스팅 모델

In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다. 요청이 나가는 네트워크 트래픽을 동일한 머신에 다시 반환하는 네트워크 인터페이스인 루프백 어댑터를 통해 프록시되지 않기 때문에 In-Process 호스팅이 Out-of-Process 호스팅보다 향상된 성능을 제공합니다. IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.

Out-of-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되고, 모듈이 프로세스 관리를 수행합니다. 모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다. 이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.

자세한 내용 및 구성 지침은 다음 항목을 참조하세요.

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core는 다음과 함께 제공됩니다.

* [Kestrel](xref:fundamentals/servers/kestrel) 서버는 기본 플랫폼 간 HTTP 서버입니다.
* [HTTP.sys 서버](xref:fundamentals/servers/httpsys)는 [Http.Sys 커널 드라이버 및 HTTP Server API](/windows/desktop/Http/http-api-start-page)를 기반으로 하는 Windows 전용 HTTP 서버입니다.

[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 사용하면 앱이 [Kestrel 서버](#kestrel)를 사용하여 IIS 작업자 프로세스와 다른 별도의 프로세스에서(*Out-of-Process*) 실행됩니다.

ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 이 모듈은 프로세스 관리를 수행합니다. 모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다. 이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.

다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.

![ASP.NET Core 모듈](_static/ancm-outofprocess.png)

요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다. 드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다. 모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.

모듈은 시작 시 환경 변수를 통해 포트를 지정하고 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)는 `http://localhost:{port}`에서 수신 대기하도록 서버를 구성합니다. 추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다. 모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.

Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다. 미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다. IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다. 앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.

IIS 및 ASP.NET Core 모듈 구성 지침은 다음 토픽을 참조하세요.

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Nginx 및 Kestrel

Linux에서 Kestrel에 대한 역방향 프록시 서버로 Nginx를 사용하는 방법은 <xref:host-and-deploy/linux-nginx>를 참조하세요.

### <a name="apache-with-kestrel"></a>Apache 및 Kestrel

Linux에서 Kestrel에 대한 역방향 프록시 서버로 Apache를 사용하는 방법은 <xref:host-and-deploy/linux-apache>를 참조하세요.

## <a name="httpsys"></a>HTTP.sys

Windows에서 ASP.NET Core 앱을 실행할 경우 Kestrel 대신 HTTP.sys를 사용할 수 있습니다. 최상의 성능을 위해 일반적으로 Kestrel을 사용하는 것이 좋습니다. 앱이 인터넷에 노출되고 필수 기능이 Kestrel이 아닌 HTTP.sys에서 지원되는 시나리오에서 HTTP.sys를 사용할 수 있습니다. 자세한 내용은 <xref:fundamentals/servers/httpsys>를 참조하세요.

![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

HTTP.sys는 내부 네트워크에만 노출되는 앱에도 사용할 수 있습니다.

![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

HTTP.sys 구성 지침은 <xref:fundamentals/servers/httpsys>를 참조하세요.

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET Core 서버 인프라

`Startup.Configure` 메서드에서 사용 가능한 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>는 <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> 형식의 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> 속성을 노출합니다. Kestrel 및 HTTP.sys는 각각 단일 기능인 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>만을 노출하지만 다른 서버 구현은 추가 기능을 노출할 수 있습니다.

`IServerAddressesFeature`를 사용하여 런타임 시 서버 구현이 바인딩된 포트를 확인할 수 있습니다.

## <a name="custom-servers"></a>사용자 지정 서버

기본 제공 서버가 앱의 요구 사항을 충족하지 않으면 사용자 지정 서버 구현을 만들 수 있습니다. [OWIN(Open Web Interface for .NET) 가이드](xref:fundamentals/owin)에서는 [Nowin](https://github.com/Bobris/Nowin) 기반 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 구현을 작성하는 방법을 보여 줍니다. 앱이 사용하는 기능 인터페이스에만 구현이 필요하지만, 최소한 <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> 및 <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>는 지원되어야 합니다.

## <a name="server-startup"></a>서버 시작

IDE(통합 개발 환경)이나 편집기가 앱을 시작하는 경우 서버가 실행됩니다.

* [Visual Studio](https://visualstudio.microsoft.com): 실행 프로필을 사용하여 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 또는 콘솔에서 앱 및 서버를 시작할 수 있습니다.
* [Visual Studio Code](https://code.visualstudio.com/): [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode)로 앱 및 서버를 시작합니다. 그러면 CoreCLR 디버거를 활성화합니다.
* [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/): 앱과 서버는 [Mono Soft-Mode 디버거](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)에 의해 시작됩니다.

프로젝트의 폴더에 있는 명령 프롬프트에서 앱을 시작할 때 [dotnet run](/dotnet/core/tools/dotnet-run)은 서버 및 앱을 시작합니다(Kestrel 및 HTTP.sys만 해당). `Debug`(기본값) 또는 `Release`로 설정되어 있는 `-c|--configuration` 옵션으로 구성을 지정합니다.

*launchSettings.json* 파일은 `dotnet run`을 사용하거나 Visual Studio와 같은 도구에 기본 제공되는 디버거를 사용하여 앱을 시작할 때 구성을 제공합니다. 시작 프로필이 *launchSettings.json* 파일에 있는 경우 `dotnet run` 명령에 `--launch-profile {PROFILE NAME}` 옵션을 사용하거나 Visual Studio에서 프로필을 선택합니다. 자세한 내용은 [dotnet run](/dotnet/core/tools/dotnet-run) 및 [.NET Core 배포 패키징](/dotnet/core/build/distribution-packaging)을 참조하세요.

## <a name="http2-support"></a>HTTP/2 지원

[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 배포 시나리오에서 ASP.NET Core에서 지원됩니다.

::: moniker range=">= aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel/http2)
  * 운영 체제
    * Windows Server 2016/Windows 10 이상&dagger;
    * Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)
    * macOS에서는 이후 릴리스에서 HTTP/2가 지원됩니다.
  * 대상 프레임워크: .NET Core 2.2 이상
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 이상
  * 대상 프레임워크: HTTP.sys 배포에는 적용되지 않습니다.
* [IIS(In-Process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 이상, IIS 10 이상
  * 대상 프레임워크: .NET Core 2.2 이상
* [IIS(Out-of-Process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 이상, IIS 10 이상
  * 공개 에지 서버 연결은 HTTP/2를 사용하지만 Kestrel에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.
  * 대상 프레임워크: IIS Out-of-Process 배포에는 적용되지 않습니다.

&dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다. 이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다. TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * 운영 체제
    * Windows Server 2016/Windows 10 이상&dagger;
    * Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)
    * macOS에서는 이후 릴리스에서 HTTP/2가 지원됩니다.
  * 대상 프레임워크: .NET Core 2.2 이상
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 이상
  * 대상 프레임워크: HTTP.sys 배포에는 적용되지 않습니다.
* [IIS(In-Process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 이상, IIS 10 이상
  * 대상 프레임워크: .NET Core 2.2 이상
* [IIS(Out-of-Process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 이상, IIS 10 이상
  * 공개 에지 서버 연결은 HTTP/2를 사용하지만 Kestrel에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.
  * 대상 프레임워크: IIS Out-of-Process 배포에는 적용되지 않습니다.

&dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다. 이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다. TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 이상
  * 대상 프레임워크: HTTP.sys 배포에는 적용되지 않습니다.
* [IIS(Out-of-Process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 이상, IIS 10 이상
  * 공개 에지 서버 연결은 HTTP/2를 사용하지만 Kestrel에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.
  * 대상 프레임워크: IIS Out-of-Process 배포에는 적용되지 않습니다.

::: moniker-end

HTTP/2 연결은 [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 및 TLS 1.2 이상을 사용해야 합니다. 자세한 정보는 서버 배포 시나리오와 관련된 항목을 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
