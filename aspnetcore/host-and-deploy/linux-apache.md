---
title: Apache를 사용하여 Linux에서 ASP.NET Core 호스트
author: rick-anderson
description: CentOS에서 Apache를 역방향 프록시 서버로 설정하여 Kestrel에서 실행되는 ASP.NET Core 웹앱에 HTTP 트래픽을 리디렉션하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
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
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 0bae3f888a1b7a3c2860b85754779189c636d86f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057702"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Apache를 사용하여 Linux에서 ASP.NET Core 호스트

작성자: [Shayne Boyer](https://github.com/spboyer)

이 가이드를 사용하여 [CentOS 7](https://www.centos.org/)에서 [Apache](https://httpd.apache.org/)를 역방향 프록시 서버로 설정하여 [Kestrel](xref:fundamentals/servers/kestrel) 서버에서 실행되는 ASP.NET Core 웹앱에 HTTP 트래픽을 리디렉션하는 방법을 알아봅니다. [mod_proxy 확장](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) 및 관련 모듈은 서버의 역방향 프록시를 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* sudo 권한을 가진 표준 사용자 계정으로 CentOS 7을 실행하는 서버가 필요합니다.
* 서버에서 .NET Core 런타임을 설치합니다.
   1. [.NET Core 다운로드 페이지](https://dotnet.microsoft.com/download/dotnet-core)를 참조하세요.
   1. 미리 보기가 아닌 최신 .NET Core 버전을 선택합니다.
   1. **앱 실행 - 런타임** 에 있는 테이블에서 미리 보기가 아닌 최신 런타임을 다운로드합니다.
   1. Linux **패키지 관리자 지침** 링크를 선택하고 CentOS 지침을 따릅니다.
* 기존 ASP.NET Core 앱입니다.

공유 프레임워크를 업그레이드한 후 나중에 언제든지 서버에서 호스트되는 ASP.NET Core 앱을 다시 시작합니다.

## <a name="publish-and-copy-over-the-app"></a>앱 게시 및 복사

[프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 앱을 구성합니다.

앱을 로컬에서 실행하고 보안 연결(HTTPS)을 확인하도록 구성하지 않은 경우 다음 방법 중 하나를 채택합니다.

* 보안 로컬 연결을 처리하도록 앱을 구성합니다. 자세한 내용은 [HTTPS 구성](#https-configuration) 섹션을 참조하세요.
* *Properties/launchSettings.json* 파일의 `applicationUrl` 속성에서 `https://localhost:5001`(있는 경우)을 제거합니다.

서버에서 실행할 수 있는 디렉터리(예: *bin/Release/&lt;target_framework_moniker&gt;/publish*)로 앱을 패키징하기 위해 개발 환경에서 [dotnet publish](/dotnet/core/tools/dotnet-publish)를 실행합니다.

```dotnetcli
dotnet publish --configuration Release
```

.NET Core 런타임을 서버에서 유지 관리하지 않으려는 경우 앱은 [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)로 게시될 수도 있습니다.

조직의 워크플로에 통합된 도구(예: SCP, SFTP)를 사용하여 ASP.NET Core 앱을 서버에 복사합니다. *var* 디렉터리(예: *var/www/helloapp*)에서 웹앱을 찾는 것이 일반적입니다.

> [!NOTE]
> 프로덕션 배포 시나리오에서 지속적인 통합 워크플로는 앱을 게시하고 자산을 서버로 복사하는 워크플로를 수행합니다.

## <a name="configure-a-proxy-server"></a>프록시 서버 구성

역방향 프록시는 동적 웹앱을 지원하기 위한 일반적인 설정입니다. 역방향 프록시는 HTTP 요청을 종료하고 이 요청을 ASP.NET 앱에 전달합니다.

프록시 서버는 클라이언트 요청을 자체 수행하는 대신 다른 서버에 전달하는 서버입니다. 역방향 프록시는 일반적으로 임의의 클라이언트 대신 고정 대상에 전달됩니다. 이 가이드에서 Apache는 Kestrel이 ASP.NET Core 앱을 제공하는 동일한 서버에서 실행되는 역방향 프록시로 구성됩니다.

요청이 역방향 프록시를 통해 전달되므로 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 패키지의 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)를 사용합니다. 이 미들웨어는 `X-Forwarded-Proto` 헤더를 사용하여 `Request.Scheme`을 업데이트하므로 리디렉션 URI 및 기타 보안 정책이 제대로 작동합니다.

전달된 헤더 미들웨어를 호출한 후에 인증, 링크 생성, 리디렉션 및 지리적 위치 등 체계에 따라 달라지는 구성 요소를 배치해야 합니다.

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

다른 미들웨어를 호출하기 전에 `Startup.Configure`의 맨 위에 있는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 메서드를 호출합니다. `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 미들웨어를 구성합니다.

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

미들웨어에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>가 지정되지 않은 경우 전달할 기본 헤더는 `None`입니다.

표준 localhost 주소(127.0.0.1)를 포함하여 루프백 주소(127.0.0.0/8, [::1])에서 실행 중인 프록시는 기본적으로 신뢰됩니다. 조직 내의 다른 신뢰할 수 있는 프록시 또는 네트워크가 인터넷과 웹 서버 간의 요청을 처리하는 경우 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 사용하여 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> 또는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> 목록에 추가합니다. 다음 예제는 IP 주소 10.0.0.100의 신뢰할 수 있는 프록시 서버를 `Startup.ConfigureServices`의 전달된 헤더 미들웨어 `KnownProxies`에 추가합니다.

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.

### <a name="install-apache"></a>Apache 설치

CentOS 패키지를 안정적인 최신 버전으로 업데이트합니다.

```bash
sudo yum update -y
```

단일 `yum` 명령을 사용하여 CentOS에 Apache 웹 서버를 설치합니다.

```bash
sudo yum -y install httpd mod_ssl
```

명령을 실행한 후 샘플 출력은 다음과 같습니다.

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> 이 예제에서 CentOS 7 버전은 64비트이므로 출력에는 httpd.86_64가 반영됩니다. Apache를 설치한 위치를 확인하려면 명령 프롬프트에서 `whereis httpd`를 실행합니다.

### <a name="configure-apache"></a>Apache 구성

Apache의 구성 파일은 `/etc/httpd/conf.d/` 디렉터리 내에 위치합니다. `/etc/httpd/conf.modules.d/`의 모듈 구성 파일 외에도 *.conf* 확장을 포함한 모든 파일은 알파벳순으로 처리됩니다. 여기에는 모듈을 로드하는 데 필요한 구성 파일도 포함됩니다.

앱에 대해 *helloapp.conf* 라는 구성 파일을 만듭니다.

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

`VirtualHost` 블록은 서버에 있는 하나 이상의 파일에 여러 번 나타날 수 있습니다. 이전 구성 파일에서 Apache는 포트 80에서 공용 트래픽을 허용합니다. 도메인 `www.example.com`을 제공하고 있고 `*.example.com` 별칭이 동일한 웹 사이트로 확인됩니다. 자세한 내용은 [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html)(이름 기반 가상 호스트 지원)를 참조하세요. 요청은 127.0.0.1에 있는 서버의 포트 5000에 대한 루트에서 프록시 처리됩니다. 양방향 통신의 경우 `ProxyPass` 및 `ProxyPassReverse`가 필요합니다. Kestrel의 IP/포트를 변경하려면 [Kestrel: 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조하세요.

> [!WARNING]
> **VirtualHost** 블록에서 적절한 [ServerName 지시문](https://httpd.apache.org/docs/current/mod/core.html#servername)을 지정하지 않으면 앱이 보안 취약성에 노출됩니다. 전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.example.com`)에는 이러한 보안 위험이 발생하지 않습니다(취약한 `*.com`과 반대임). 자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.

로깅은 `ErrorLog` 및 `CustomLog` 지시문을 사용하여 `VirtualHost`별로 구성할 수 있습니다. `ErrorLog`는 서버가 오류를 기록하는 위치이고 `CustomLog`는 로그 파일의 파일 이름과 형식을 설정합니다. 이 경우에는 요청 정보가 기록되는 위치입니다. 각 요청이 한 줄에 기록됩니다.

파일을 저장하고 구성을 테스트합니다. 모든 항목이 통과하는 경우 응답은 `Syntax [OK]`이어야 합니다.

```bash
sudo service httpd configtest
```

Apache를 다시 시작합니다.

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>앱 모니터링

이제 Apache는 `http://localhost:80`에 대해 실행된 요청을 `http://127.0.0.1:5000`의 Kestrel에서 실행되는 ASP.NET Core 앱에 전달하도록 설정됩니다. 그러나 Apache는 Kestrel 프로세스를 관리하도록 설정되지 않습니다. *systemd* 를 사용하고 서비스 파일을 만들어 기본 웹앱을 시작하고 모니터링합니다. *systemd* 는 프로세스를 시작, 중지 및 관리하기 위한 다양하고 강력한 기능을 제공하는 init 시스템입니다.

### <a name="create-the-service-file"></a>서비스 파일 만들기

서비스 정의 파일을 만듭니다.

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

앱의 예제 서비스 파일:

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

앞의 예제에서 서비스를 관리하는 사용자는 `User` 옵션으로 지정됩니다. 사용자(`apache`)가 존재해야 하며 앱 파일에 대한 적절한 소유권이 있어야 합니다.

`TimeoutStopSec`를 사용하여 초기 인터럽트 신호를 받은 후 앱이 종료되기를 기다리는 기간을 구성합니다. 이 기간 내에 앱이 종료되지 않으면 앱을 종료하기 위해 SIGKILL이 실행됩니다. 단위 없는 초로 된 값(예: `150`) 또는 시간 범위 값(예: `2min 30s`)으로 값을 입력하거나, 시간 제한을 사용하지 않으려면 `infinity`를 입력합니다. `TimeoutStopSec`의 기본값은 관리자 구성 파일(*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*)의 `DefaultTimeoutStopSec` 값입니다. 대부분의 배포에서 기본 시간 제한은 90초입니다.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

일부 값(예: SQL 연결 문자열)은 환경 변수를 읽기 위해 구성 공급자에 대해 이스케이프되어야 합니다. 다음 명령을 사용하여 구성 파일에서 사용할 제대로 이스케이프된 값을 생성합니다.

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

콜론(`:`) 구분 기호는 환경 변수 이름에서 지원되지 않습니다. 콜론 대신 이중 밑줄(`__`)을 사용합니다. [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)는 환경 변수를 구성으로 읽을 때 이중 밑줄을 콜론으로 변환합니다. 다음 예제에서 연결 문자열 키 `ConnectionStrings:DefaultConnection`은 서비스 정의 파일에 `ConnectionStrings__DefaultConnection`으로 설정됩니다.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

콜론(`:`) 구분 기호는 환경 변수 이름에서 지원되지 않습니다. 콜론 대신 이중 밑줄(`__`)을 사용합니다. [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables)는 환경 변수를 구성으로 읽을 때 이중 밑줄을 콜론으로 변환합니다. 다음 예제에서 연결 문자열 키 `ConnectionStrings:DefaultConnection`은 서비스 정의 파일에 `ConnectionStrings__DefaultConnection`으로 설정됩니다.

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

파일을 저장하고 서비스를 사용하도록 설정합니다.

```bash
sudo systemctl enable kestrel-helloapp.service
```

서비스를 시작하고 실행 중인지 확인합니다.

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

역방향 프록시를 구성하고 *systemd* 를 통해 Kestrel을 관리하면 웹앱이 완전히 구성되고 로컬 컴퓨터(`http://localhost`)의 브라우저에서 웹앱에 액세스할 수 있습니다. 응답 헤더를 검사하는 **Server** 헤더는 ASP.NET Core 앱이 Kestrel에서 제공됨을 나타냅니다.

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>로그 보기

Kestrel을 사용하는 웹앱은 *systemd* 를 사용하여 관리되므로 이벤트 및 프로세스가 중앙형 저널에 기록됩니다. 그러나 이 저널에는 *systemd* 에서 관리하는 모든 서비스 및 프로세스에 대한 항목이 포함됩니다. `kestrel-helloapp.service` 관련 항목을 보려면 다음 명령을 사용합니다.

```bash
sudo journalctl -fu kestrel-helloapp.service
```

시간 필터링의 경우 명령을 사용하여 시간 옵션을 지정합니다. 예를 들어 `--since today`를 사용하여 현재 날짜를 기준으로 필터링하거나 `--until 1 hour ago`를 사용하여 이전 시간의 항목을 확인합니다. 자세한 내용은 [journalctl에 대한 기본 페이지](https://www.unix.com/man-page/centos/1/journalctl/)를 참조하세요.

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>데이터 보호

[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증 미들웨어(예: cookie 미들웨어) 및 CSRF(교차 사이트 요청 위조) 보호를 비롯한 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에 사용됩니다. 사용자 코드에서 데이터 보호 API가 호출되지 않더라도 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다. 데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.

키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.

* 모든 cookie 기반 인증 토큰이 무효화됩니다.
* 사용자는 다음 요청에서 다시 로그인해야 합니다.
* 키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다. 여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.

키 링을 유지하고 암호화하도록 데이터 보호를 구성하려면 다음을 참조하십시오.

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a>앱 보안 유지

### <a name="configure-firewall"></a>방화벽 구성

*Firewalld* 는 네트워크 영역에 대한 지원을 통해 방화벽을 관리하는 동적 디먼입니다. 포트 및 패킷 필터링은 iptables로 계속 관리할 수 있습니다. *Firewalld* 는 기본적으로 설치해야 합니다. `yum`을 사용하여 패키지를 설치하거나 설치되었는지 확인할 수 있습니다.

```bash
sudo yum install firewalld -y
```

`firewalld`를 사용하여 앱에 필요한 포트만 엽니다. 이 경우에는 포트 80 및 443을 사용합니다. 다음 명령은 포트 80 및 443이 영구적으로 열리도록 설정합니다.

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

방화벽 설정을 다시 로드합니다. 기본 영역의 사용 가능한 서비스 및 포트를 확인합니다. `firewall-cmd -h`를 검사하여 옵션을 사용할 수 있습니다.

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a>HTTPS 구성

**보안(HTTPS) 로컬 연결을 위해 앱 구성**

[dotnet 실행](/dotnet/core/tools/dotnet-run) 명령은 `applicationUrl` 속성(예: `https://localhost:5001;http://localhost:5000`)이 제공하는 URL에서 수신 대기하도록 앱을 구성하는 앱의 *Properties/launchSettings.json* 파일을 사용합니다.

다음 방법 중 하나를 사용하여 `dotnet run` 명령 또는 개발 환경(Visual Studio Code의 F5 또는 Ctrl+F5)에 대해 개발 중인 인증서를 사용하도록 앱을 구성합니다.

* [구성에서 기본 인증서를 바꿈](xref:fundamentals/servers/kestrel#configuration)(*권장*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**보안 (HTTPS) 클라이언트 연결을 위해 역방향 프록시 구성**

HTTPS에 Apache를 구성하려면 *mod_ssl* 모듈을 사용합니다. *httpd* 모듈이 설치될 때 *mod_ssl* 모듈도 설치되었습니다. 설치되지 않은 경우 `yum`을 사용하여 구성에 추가합니다.

```bash
sudo yum install mod_ssl
```

HTTPS를 적용하려면 URL 재작성을 사용할 수 있도록 `mod_rewrite` 모듈을 설치합니다.

```bash
sudo yum install mod_rewrite
```

포트 443에서 URL 재작성 및 보안 통신을 사용할 수 있도록 *helloapp.conf* 파일을 수정합니다.

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> 이 예제에서는 로컬로 생성된 인증서를 사용합니다. **SSLCertificateFile** 은 도메인 이름에 대한 기본 인증서 파일이어야 합니다. **SSLCertificateKeyFile** 은 CSR을 만들 때 생성된 키 파일이어야 합니다. **SSLCertificateChainFile** 은 인증 기관에서 제공된 중간 인증서 파일(있는 경우)이어야 합니다.

파일을 저장하고 구성을 테스트합니다.

```bash
sudo service httpd configtest
```

Apache를 다시 시작합니다.

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>추가 Apache 제안

### <a name="restart-apps-with-shared-framework-updates"></a>공유 프레임워크 업데이트를 사용하여 앱 다시 시작

서버에서 공유 프레임워크를 업그레이드한 후 서버에서 호스트되는 ASP.NET Core 앱을 다시 시작합니다.

### <a name="additional-headers"></a>추가 헤더

악의적인 공격으로부터 보호하기 위해 몇 가지 헤더를 수정하거나 추가해야 합니다. `mod_headers` 모듈이 설치되었는지 확인합니다.

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>클릭재킹(clickjacking) 공격으로부터 Apache 보호

또한 ‘UI 교정 공격’이라고도 하는[클릭재킹(Clickjacking)](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)은 웹 사이트 방문자를 속여서 현재 방문 중인 것과 다른 페이지에서 링크 또는 단추를 클릭하게 하는 악의적인 공격입니다.  `X-FRAME-OPTIONS`를 사용하여 사이트를 보호합니다.

클릭재킹 공격을 완화하려면:

1. *httpd.conf* 파일을 편집합니다.

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   `Header append X-FRAME-OPTIONS "SAMEORIGIN"` 줄을 추가합니다.
1. 파일을 저장합니다.
1. Apache를 다시 시작합니다.

#### <a name="mime-type-sniffing"></a>MIME 형식 검색

`X-Content-Type-Options` 헤더는 Internet Explorer에서 ‘MIME 스니핑’을 방지합니다(파일 콘텐츠에서 파일의 `Content-Type` 확인).  서버에서 `nosniff` 옵션 집합을 사용하여 `Content-Type` 헤더를 `text/html`로 설정하는 경우 Internet Explorer는 파일 콘텐츠에 관계없이 콘텐츠를 `text/html`로 렌더링합니다.

*httpd.conf* 파일을 편집합니다.

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

`Header set X-Content-Type-Options "nosniff"` 줄을 추가합니다. 파일을 저장합니다. Apache를 다시 시작합니다.

### <a name="load-balancing"></a>부하 분산

이 예제에서는 동일한 인스턴스 컴퓨터에서 CentOS 7와 Kestrel의 Apache를 설정하고 구성하는 방법을 보여줍니다. 단일 실패 지점이 없도록 하기 위해 *mod_proxy_balancer* 를 사용하고 **VirtualHost** 를 수정하면 Apache 프록시 서버 뒤에 있는 웹앱의 여러 인스턴스를 관리할 수 있습니다.

```bash
sudo yum install mod_proxy_balancer
```

아래 표시된 구성 파일에서 `helloapp`의 추가 인스턴스는 포트 5001에서 실행되도록 설정됩니다. *Proxy* 섹션은 *byrequests* 의 부하를 분산하는 두 개의 멤버가 있는 분산 장치 구성을 사용하여 설정됩니다.

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>속도 제한

*httpd* 모듈에 포함된 *mod_ratelimit* 을 사용하여 클라이언트의 대역폭을 제한할 수 있습니다.

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

예제 파일에서는 루트 위치 아래에서 대역폭을 600KB/초로 제한합니다.

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>긴 요청 헤더 필드

프록시 서버 기본 설정은 일반적으로 요청 헤더 필드를 8,190바이트로 제한합니다. 앱에 기본값보다 긴 필드가 필요할 수 있습니다(예: [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)를 사용하는 앱). 더 긴 필드가 필요한 경우 프록시 서버의 [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) 지시문을 조정해야 합니다. 적용할 값은 시나리오에 따라 달라집니다. 자세한 내용은 서버의 설명서를 참조하세요.

> [!WARNING]
> 필요한 경우가 아니면 `LimitRequestFieldSize`의 기본값을 늘리지 마세요. 값을 늘리면 악의적인 사용자의 버퍼 오버런(오버플로) 및 DoS(서비스 거부) 공격의 위험이 증가됩니다.

## <a name="additional-resources"></a>추가 자료

* [Linux에서 .NET Core의 필수 구성 요소](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
