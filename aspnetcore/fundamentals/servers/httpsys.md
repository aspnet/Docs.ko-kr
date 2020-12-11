---
title: ASP.NET Core에서 HTTP.sys 웹 서버 구현
author: rick-anderson
description: Windows의 ASP.NET Core에 대한 웹 서버인 HTTP.sys에 대해 알아봅니다. HTTP.sys 커널 모드 드라이버를 기반으로 한 HTTP.sys는 IIS 없이 인터넷에 대한 직접 연결에 사용될 수 있는 Kestrel에 대한 대안입니다.
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 9c65abd5a055bb677a14921296316e7e03760bc2
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855367"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>ASP.NET Core에서 HTTP.sys 웹 서버 구현

작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Chris Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-3.1"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다. HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.

> [!IMPORTANT]
> HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.

HTTP.sys는 다음과 같은 기능을 지원합니다.

* [Windows 인증](xref:security/authentication/windowsauth)
* 포트 공유
* SNI를 사용하는 HTTPS
* TLS를 통한 HTTP/2(Windows 10 이상)
* 직접 파일 전송
* 응답 캐싱
* WebSockets(Windows 8 이상)

지원되는 Windows 버전:

* Windows 7 이상
* Windows Server 2008 R2 이상

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP.sys를 사용하는 경우

HTTP.sys는 다음과 같은 배포에 유용합니다.

* IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* 내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다. IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.

## <a name="http2-support"></a>HTTP/2 지원

다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.

* Windows Server 2016/Windows 10 이상
* [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결
* TLS 1.2 이상 연결

HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.

HTTP/2는 기본적으로 사용됩니다. HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다. Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos를 사용하여 커널 모드 인증

HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다. 사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다. 머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다. 앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.

## <a name="how-to-use-httpsys"></a>HTTP.sys 사용 방법

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>HTTP.sys를 사용하도록 ASP.NET Core 앱 구성

호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다. 다음 예제에서는 옵션을 기본값으로 설정합니다.

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.

**HTTP.sys 옵션**

| 속성 | 설명 | 기본값 |
| -------- | ----------- | :-----: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO> | `HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다. | `false` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 익명 요청을 허용합니다. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 허용되는 인증 체계를 지정합니다. 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다. 값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다. | `None` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching> | 적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다. 응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다. `public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Http503Verbosity> | 제한 조건으로 인해 요청을 거부할 때 HTTP.sys 동작입니다. | [Http503VerbosityLevel.<br>Basic](xref:Microsoft.AspNetCore.Server.HttpSys.Http503VerbosityLevel) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 최대 동시 승인 수입니다. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 허용되는 최대 동시 연결 수입니다. 무한의 경우 `-1`을 사용합니다. 레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다. | `null`<br>(시스템 수준<br>설정) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요. | 30000000바이트<br>(~28.6MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | 큐에 대기할 수 있는 최대 요청 수입니다. | 1000 |
| `RequestQueueMode` | 이는 서버가 요청 큐를 만들고 구성하는 것을 담당하는지, 아니면 기존 큐에 연결해야 할지를 나타냅니다.<br>기존 큐에 연결하는 경우 대부분의 기존 구성 옵션이 적용되지 않습니다. | `RequestQueueMode.Create` |
| `RequestQueueName` | HTTP.sys 요청 큐의 이름입니다. | `null`(익명 큐) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다. | `false`<br>(정상적으로 완료) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다. API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.<ul><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: 유휴 연결에 허용되는 시간입니다.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: 응답의 최소 전송 속도입니다.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다. 컬렉션에 접두사를 추가하는 데 사용되는 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>가 가장 유용합니다. 이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

요청 본문에 대해 허용되는 최대 크기(바이트)입니다. `null`로 설정하면 최대 요청 본문 크기는 무제한입니다. 항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.

단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다. `IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.

앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.

Visual Studio에서 기본 실행 프로필은 IIS Express용입니다. 프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server 구성

1. 앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다. 다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.

1. Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다. 다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.

1. 필요한 경우 X.509 인증서를 구하여 설치합니다.

   Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다. 지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.

   서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.

1. 앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.

   * **.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다. 서버에 전체 SDK를 설치하지 마세요.
   * **.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요. 필수 .NET Framework를 설치합니다. 최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.

   앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다. 서버에 프레임워크를 설치할 필요가 없습니다.

1. 앱에서 URL 및 포트를 구성합니다.

   기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다. URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls` 명령줄 인수
   * `ASPNETCORE_URLS`환경 변수
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   `UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.

   `UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다. 따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.

   HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.

   > [!WARNING]
   > 최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다. 최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다. 강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다. 와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다. 전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임). 자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.

1. 서버에 URL 접두사를 미리 등록합니다.

   HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다. *netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다. 도구를 사용하려면 관리자 권한이 필요합니다.

   *netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`: 정규화된 URL(Uniform Resource Locator)입니다. 와일드카드 바인딩을 사용하지 마세요. 유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다. ‘URL에는 후행 슬래시가 포함되어야 합니다.’
   * `<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.

   다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.

   등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. 서버에 X.509 인증서를 등록합니다.

   *netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`: 바인딩의 로컬 IP 주소를 지정합니다. 와일드카드 바인딩을 사용하지 마세요. 유효한 IP 주소를 사용합니다.
   * `<PORT>`: 바인딩의 포트를 지정합니다.
   * `<THUMBPRINT>`: X.509 인증서 지문입니다.
   * `<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.

   참조용으로 GUID를 패키지 태그로 앱에 저장합니다.

   * Visual Studio에서 다음을 수행합니다.
     * **솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.
     * **패키지** 탭을 선택합니다.
     * **태그** 필드에 직접 만든 GUID를 입력합니다.
   * Visual Studio를 사용하지 않는 경우:
     * 앱의 프로젝트 파일을 엽니다.
     * 직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   다음 예제에서는

   * 서버의 로컬 IP 주소는 `10.0.0.4`입니다.
   * 온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.

   인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *netsh.exe* 에 대한 참조 문서입니다.

   * [HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))
   * [UrlPrefix 문자열](/windows/win32/http/urlprefix-strings)

1. 앱을 실행합니다.

   1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다. 다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.

   이 앱은 서버의 공용 IP 주소에서 응답합니다. 이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.

   이 예제에는 개발 인증서가 사용됩니다. 브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>프록시 서버 및 부하 분산 장치 시나리오

인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다. 자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.

## <a name="advanced-http2-features-to-support-grpc"></a>gRPC를 지원하기 위한 고급 HTTP/2 기능

HTTP.sys의 추가 HTTP/2 기능은 응답 트레일러 및 초기화 프레임 전송을 위한 지원을 포함하여 gRPC를 지원합니다.

HTTP.SYS를 사용하여 gRPC를 실행하기 위한 요구 사항:

* Windows 10, OS 빌드 19041.508 이상
* TLS 1.2 이상 연결

### <a name="trailers"></a>트레일러

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>다시 설정

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a>추가 자료

* [HTTP.sys에서 Windows 인증 사용](xref:security/authentication/windowsauth#httpsys)
* [HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)
* [aspnet/HttpSysServer GitHub 리포지토리(소스 코드)](https://github.com/aspnet/HttpSysServer/)
* [호스트](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다. HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.

> [!IMPORTANT]
> HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.

HTTP.sys는 다음과 같은 기능을 지원합니다.

* [Windows 인증](xref:security/authentication/windowsauth)
* 포트 공유
* SNI를 사용하는 HTTPS
* TLS를 통한 HTTP/2(Windows 10 이상)
* 직접 파일 전송
* 응답 캐싱
* WebSockets(Windows 8 이상)

지원되는 Windows 버전:

* Windows 7 이상
* Windows Server 2008 R2 이상

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP.sys를 사용하는 경우

HTTP.sys는 다음과 같은 배포에 유용합니다.

* IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* 내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다. IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.

## <a name="http2-support"></a>HTTP/2 지원

다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.

* Windows Server 2016/Windows 10 이상
* [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결
* TLS 1.2 이상 연결

HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.

HTTP/2는 기본적으로 사용됩니다. HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다. Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos를 사용하여 커널 모드 인증

HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다. 사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다. 머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다. 앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.

## <a name="how-to-use-httpsys"></a>HTTP.sys 사용 방법

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>HTTP.sys를 사용하도록 ASP.NET Core 앱 구성

호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다. 다음 예제에서는 옵션을 기본값으로 설정합니다.

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.

**HTTP.sys 옵션**

| 속성 | 설명 | 기본값 |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다. | `false` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 익명 요청을 허용합니다. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 허용되는 인증 체계를 지정합니다. 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다. 값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다. 응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다. `public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 최대 동시 승인 수입니다. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 허용되는 최대 동시 연결 수입니다. 무한의 경우 `-1`을 사용합니다. 레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다. | `null`<br>(시스템 수준<br>설정) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요. | 30000000바이트<br>(~28.6MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | 큐에 대기할 수 있는 최대 요청 수입니다. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다. | `false`<br>(정상적으로 완료) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다. API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다. 컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다. 이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

요청 본문에 대해 허용되는 최대 크기(바이트)입니다. `null`로 설정하면 최대 요청 본문 크기는 무제한입니다. 항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.

단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다. `IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.

앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.

Visual Studio에서 기본 실행 프로필은 IIS Express용입니다. 프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server 구성

1. 앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다. 다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.

1. Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다. 다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.

1. 필요한 경우 X.509 인증서를 구하여 설치합니다.

   Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다. 지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.

   서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.

1. 앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.

   * **.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다. 서버에 전체 SDK를 설치하지 마세요.
   * **.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요. 필수 .NET Framework를 설치합니다. 최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.

   앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다. 서버에 프레임워크를 설치할 필요가 없습니다.

1. 앱에서 URL 및 포트를 구성합니다.

   기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다. URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls` 명령줄 인수
   * `ASPNETCORE_URLS`환경 변수
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   `UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.

   `UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다. 따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.

   HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.

   > [!WARNING]
   > 최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다. 최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다. 강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다. 와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다. 전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임). 자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.

1. 서버에 URL 접두사를 미리 등록합니다.

   HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다. *netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다. 도구를 사용하려면 관리자 권한이 필요합니다.

   *netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`: 정규화된 URL(Uniform Resource Locator)입니다. 와일드카드 바인딩을 사용하지 마세요. 유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다. ‘URL에는 후행 슬래시가 포함되어야 합니다.’
   * `<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.

   다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.

   등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. 서버에 X.509 인증서를 등록합니다.

   *netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`: 바인딩의 로컬 IP 주소를 지정합니다. 와일드카드 바인딩을 사용하지 마세요. 유효한 IP 주소를 사용합니다.
   * `<PORT>`: 바인딩의 포트를 지정합니다.
   * `<THUMBPRINT>`: X.509 인증서 지문입니다.
   * `<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.

   참조용으로 GUID를 패키지 태그로 앱에 저장합니다.

   * Visual Studio에서 다음을 수행합니다.
     * **솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.
     * **패키지** 탭을 선택합니다.
     * **태그** 필드에 직접 만든 GUID를 입력합니다.
   * Visual Studio를 사용하지 않는 경우:
     * 앱의 프로젝트 파일을 엽니다.
     * 직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   다음 예제에서는

   * 서버의 로컬 IP 주소는 `10.0.0.4`입니다.
   * 온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.

   인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *netsh.exe* 에 대한 참조 문서입니다.

   * [HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))
   * [UrlPrefix 문자열](/windows/win32/http/urlprefix-strings)

1. 앱을 실행합니다.

   1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다. 다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.

   이 앱은 서버의 공용 IP 주소에서 응답합니다. 이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.

   이 예제에는 개발 인증서가 사용됩니다. 브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>프록시 서버 및 부하 분산 장치 시나리오

인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다. 자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [HTTP.sys에서 Windows 인증 사용](xref:security/authentication/windowsauth#httpsys)
* [HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)
* [aspnet/HttpSysServer GitHub 리포지토리(소스 코드)](https://github.com/aspnet/HttpSysServer/)
* [호스트](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다. HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.

> [!IMPORTANT]
> HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.

HTTP.sys는 다음과 같은 기능을 지원합니다.

* [Windows 인증](xref:security/authentication/windowsauth)
* 포트 공유
* SNI를 사용하는 HTTPS
* TLS를 통한 HTTP/2(Windows 10 이상)
* 직접 파일 전송
* 응답 캐싱
* WebSockets(Windows 8 이상)

지원되는 Windows 버전:

* Windows 7 이상
* Windows Server 2008 R2 이상

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP.sys를 사용하는 경우

HTTP.sys는 다음과 같은 배포에 유용합니다.

* IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* 내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다. IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.

## <a name="http2-support"></a>HTTP/2 지원

다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.

* Windows Server 2016/Windows 10 이상
* [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결
* TLS 1.2 이상 연결

HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.

HTTP/2는 기본적으로 사용됩니다. HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다. Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos를 사용하여 커널 모드 인증

HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다. 사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다. 머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다. 앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.

## <a name="how-to-use-httpsys"></a>HTTP.sys 사용 방법

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>HTTP.sys를 사용하도록 ASP.NET Core 앱 구성

[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))를 사용할 경우 프로젝트 파일의 패키지 참조가 필요하지 않습니다. `Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)에 패키지 참조를 추가합니다.

호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다. 다음 예제에서는 옵션을 기본값으로 설정합니다.

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.

**HTTP.sys 옵션**

| 속성 | 설명 | 기본값 |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다. | `true` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 익명 요청을 허용합니다. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 허용되는 인증 체계를 지정합니다. 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다. 값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다. 응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다. `public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 최대 동시 승인 수입니다. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 허용되는 최대 동시 연결 수입니다. 무한의 경우 `-1`을 사용합니다. 레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다. | `null`<br>(시스템 수준<br>설정) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요. | 30000000바이트<br>(~28.6MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | 큐에 대기할 수 있는 최대 요청 수입니다. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다. | `false`<br>(정상적으로 완료) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다. API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다. 컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다. 이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

요청 본문에 대해 허용되는 최대 크기(바이트)입니다. `null`로 설정하면 최대 요청 본문 크기는 무제한입니다. 항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.

단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다. `IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.

앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.

Visual Studio에서 기본 실행 프로필은 IIS Express용입니다. 프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server 구성

1. 앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다. 다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.

1. Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다. 다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.

1. 필요한 경우 X.509 인증서를 구하여 설치합니다.

   Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다. 지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.

   서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.

1. 앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.

   * **.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다. 서버에 전체 SDK를 설치하지 마세요.
   * **.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요. 필수 .NET Framework를 설치합니다. 최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.

   앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다. 서버에 프레임워크를 설치할 필요가 없습니다.

1. 앱에서 URL 및 포트를 구성합니다.

   기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다. URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls` 명령줄 인수
   * `ASPNETCORE_URLS`환경 변수
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   `UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.

   `UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다. 따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.

   HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.

   > [!WARNING]
   > 최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다. 최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다. 강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다. 와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다. 전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임). 자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.

1. 서버에 URL 접두사를 미리 등록합니다.

   HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다. *netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다. 도구를 사용하려면 관리자 권한이 필요합니다.

   *netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`: 정규화된 URL(Uniform Resource Locator)입니다. 와일드카드 바인딩을 사용하지 마세요. 유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다. ‘URL에는 후행 슬래시가 포함되어야 합니다.’
   * `<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.

   다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.

   등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. 서버에 X.509 인증서를 등록합니다.

   *netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`: 바인딩의 로컬 IP 주소를 지정합니다. 와일드카드 바인딩을 사용하지 마세요. 유효한 IP 주소를 사용합니다.
   * `<PORT>`: 바인딩의 포트를 지정합니다.
   * `<THUMBPRINT>`: X.509 인증서 지문입니다.
   * `<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.

   참조용으로 GUID를 패키지 태그로 앱에 저장합니다.

   * Visual Studio에서 다음을 수행합니다.
     * **솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.
     * **패키지** 탭을 선택합니다.
     * **태그** 필드에 직접 만든 GUID를 입력합니다.
   * Visual Studio를 사용하지 않는 경우:
     * 앱의 프로젝트 파일을 엽니다.
     * 직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   다음 예제에서는

   * 서버의 로컬 IP 주소는 `10.0.0.4`입니다.
   * 온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.

   인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *netsh.exe* 에 대한 참조 문서입니다.

   * [HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))
   * [UrlPrefix 문자열](/windows/win32/http/urlprefix-strings)

1. 앱을 실행합니다.

   1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다. 다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.

   이 앱은 서버의 공용 IP 주소에서 응답합니다. 이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.

   이 예제에는 개발 인증서가 사용됩니다. 브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>프록시 서버 및 부하 분산 장치 시나리오

인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다. 자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [HTTP.sys에서 Windows 인증 사용](xref:security/authentication/windowsauth#httpsys)
* [HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)
* [aspnet/HttpSysServer GitHub 리포지토리(소스 코드)](https://github.com/aspnet/HttpSysServer/)
* [호스트](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다. HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.

> [!IMPORTANT]
> HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.

HTTP.sys는 다음과 같은 기능을 지원합니다.

* [Windows 인증](xref:security/authentication/windowsauth)
* 포트 공유
* SNI를 사용하는 HTTPS
* TLS를 통한 HTTP/2(Windows 10 이상)
* 직접 파일 전송
* 응답 캐싱
* WebSockets(Windows 8 이상)

지원되는 Windows 버전:

* Windows 7 이상
* Windows Server 2008 R2 이상

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP.sys를 사용하는 경우

HTTP.sys는 다음과 같은 배포에 유용합니다.

* IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* 내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다. IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.

## <a name="http2-support"></a>HTTP/2 지원

다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.

* Windows Server 2016/Windows 10 이상
* [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결
* TLS 1.2 이상 연결

HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.

HTTP/2는 기본적으로 사용됩니다. HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다. Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos를 사용하여 커널 모드 인증

HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다. 사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다. 머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다. 앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.

## <a name="how-to-use-httpsys"></a>HTTP.sys 사용 방법

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>HTTP.sys를 사용하도록 ASP.NET Core 앱 구성

[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))를 사용할 경우 프로젝트 파일의 패키지 참조가 필요하지 않습니다. `Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)에 패키지 참조를 추가합니다.

호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다. 다음 예제에서는 옵션을 기본값으로 설정합니다.

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.

**HTTP.sys 옵션**

| 속성 | 설명 | 기본값 |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다. | `true` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 익명 요청을 허용합니다. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 허용되는 인증 체계를 지정합니다. 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다. 값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다. 응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다. `public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 최대 동시 승인 수입니다. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 허용되는 최대 동시 연결 수입니다. 무한의 경우 `-1`을 사용합니다. 레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다. | `null`<br>(시스템 수준<br>설정) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요. | 30000000바이트<br>(~28.6MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | 큐에 대기할 수 있는 최대 요청 수입니다. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다. | `false`<br>(정상적으로 완료) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다. API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다. 컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다. 이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

요청 본문에 대해 허용되는 최대 크기(바이트)입니다. `null`로 설정하면 최대 요청 본문 크기는 무제한입니다. 항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.

단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다. `IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.

앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.

Visual Studio에서 기본 실행 프로필은 IIS Express용입니다. 프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server 구성

1. 앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다. 다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.

1. Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다. 다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.

1. 필요한 경우 X.509 인증서를 구하여 설치합니다.

   Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다. 지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.

   서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.

1. 앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.

   * **.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다. 서버에 전체 SDK를 설치하지 마세요.
   * **.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요. 필수 .NET Framework를 설치합니다. 최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.

   앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다. 서버에 프레임워크를 설치할 필요가 없습니다.

1. 앱에서 URL 및 포트를 구성합니다.

   기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다. URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls` 명령줄 인수
   * `ASPNETCORE_URLS`환경 변수
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   `UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.

   `UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다. 따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.

   HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.

   > [!WARNING]
   > 최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다. 최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다. 강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다. 와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다. 전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임). 자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.

1. 서버에 URL 접두사를 미리 등록합니다.

   HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다. *netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다. 도구를 사용하려면 관리자 권한이 필요합니다.

   *netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`: 정규화된 URL(Uniform Resource Locator)입니다. 와일드카드 바인딩을 사용하지 마세요. 유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다. ‘URL에는 후행 슬래시가 포함되어야 합니다.’
   * `<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.

   다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.

   등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. 서버에 X.509 인증서를 등록합니다.

   *netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`: 바인딩의 로컬 IP 주소를 지정합니다. 와일드카드 바인딩을 사용하지 마세요. 유효한 IP 주소를 사용합니다.
   * `<PORT>`: 바인딩의 포트를 지정합니다.
   * `<THUMBPRINT>`: X.509 인증서 지문입니다.
   * `<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.

   참조용으로 GUID를 패키지 태그로 앱에 저장합니다.

   * Visual Studio에서 다음을 수행합니다.
     * **솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.
     * **패키지** 탭을 선택합니다.
     * **태그** 필드에 직접 만든 GUID를 입력합니다.
   * Visual Studio를 사용하지 않는 경우:
     * 앱의 프로젝트 파일을 엽니다.
     * 직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   다음 예제에서는

   * 서버의 로컬 IP 주소는 `10.0.0.4`입니다.
   * 온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.

   인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *netsh.exe* 에 대한 참조 문서입니다.

   * [HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))
   * [UrlPrefix 문자열](/windows/win32/http/urlprefix-strings)

1. 앱을 실행합니다.

   1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다. 다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.

   이 앱은 서버의 공용 IP 주소에서 응답합니다. 이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.

   이 예제에는 개발 인증서가 사용됩니다. 브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>프록시 서버 및 부하 분산 장치 시나리오

인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다. 자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [HTTP.sys에서 Windows 인증 사용](xref:security/authentication/windowsauth#httpsys)
* [HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)
* [aspnet/HttpSysServer GitHub 리포지토리(소스 코드)](https://github.com/aspnet/HttpSysServer/)
* [호스트](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
