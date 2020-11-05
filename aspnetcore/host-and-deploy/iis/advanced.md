---
title: 고급 구성
author: rick-anderson
description: ASP.NET Core 모듈 및 IIS(인터넷 정보 서비스)를 사용하는 고급 구성입니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058618"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a>ASP.NET Core 모듈 및 IIS의 고급 구성

이 문서에서는 ASP.NET Core 모듈 및 IIS의 고급 구성 옵션 및 시나리오를 설명합니다.

## <a name="modify-the-stack-size"></a>스택 크기 수정

*In-process 호스팅 모델을 사용하는 경우에만 적용됩니다.*

`web.config` 파일에서 `stackSize` 설정을 사용하여 관리형 스택 크기(바이트)를 구성합니다. 기본 크기는 1,048,576 bytes(1MB)입니다. 다음 예제에서는 스택 크기를 2MB(2,097,152바이트)로 변경합니다.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>프록시 구성은 HTTP 프로토콜 및 페어링 토큰을 사용합니다.

*호스팅에만 적용됩니다.*

ASP.NET Core 모듈과 Kestrel 사이에 만들어진 프록시는 HTTP 프로토콜을 사용합니다. 서버에서 분리된 위치에서 모듈과 Kestrel 간 트래픽 도청의 위험은 없습니다.

페어링 토큰은 Kestrel에서 받은 요청이 IIS에서 프록시되었으며 다른 원본에서 오지 않았다는 것을 보장하는 데 사용됩니다. 페어링 토큰은 모듈에 의해 생성되며 환경 변수(`ASPNETCORE_TOKEN`)로 설정됩니다. 페어링 토큰은 프록시된 모든 요청에서 헤더(`MS-ASPNETCORE-TOKEN`)로도 설정됩니다. IIS 미들웨어는 수신하는 각 요청을 검사하여 페어링 토큰 헤더 값이 환경 변수 값과 일치하는지 확인합니다. 토큰 값이 일치하지 않는 경우 요청이 기록되고 거부됩니다. 페어링 토큰 환경 변수와 모듈과 Kestrel 간의 트래픽은 서버에서 분리된 위치에서 액세스할 수 없습니다. 페어링 토큰 값을 알지 못하면 공격자는 IIS 미들웨어에서 검사를 무시하는 요청을 전송할 수 없습니다.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS 공유 구성이 포함된 ASP.NET Core 모듈

ASP.NET Core 모듈 설치 관리자는 `TrustedInstaller` 계정의 권한으로 실행됩니다. 로컬 시스템 계정에는 IIS 공유 구성에서 사용하는 공유 경로에 대한 수정 권한이 없으므로 공유의 `applicationHost.config`에서 모듈 설정을 구성하려고 하면 설치 관리자에서 액세스 거부 오류가 throw됩니다.

IIS 설치와 동일한 머신에서 IIS 공유 구성을 사용하는 경우 `OPT_NO_SHARED_CONFIG_CHECK` 매개 변수를 `1`로 설정한 상태에서 ASP.NET Core Hosting Bundle 설치 관리자를 실행합니다

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

공유 구성에 대한 경로가 IIS 설치와 동일한 머신에 있지 않으면 다음 단계를 수행합니다.

1. IIS 공유 구성을 사용하지 않도록 설정합니다.
1. 설치 관리자를 실행합니다.
1. 업데이트된 `applicationHost.config` 파일을 파일 공유로 내보냅니다.
1. IIS 공유 구성을 다시 사용하도록 설정합니다.

## <a name="data-protection"></a>데이터 보호

[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다. 사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다. 데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.

데이터 보호 키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.

* 모든 cookie 기반 인증 토큰이 무효화됩니다. 
* 사용자는 다음 요청에서 다시 로그인해야 합니다. 
* 키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다. 여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.

IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나** 를 사용합니다.

* **데이터 보호 레지스트리 키 만들기**

  ASP.NET Core 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다. 지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.

  WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다. 이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다. 미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.

  웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다. 기본적으로 키는 암호화되지 않습니다. 네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다. X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다. 사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다. 자세한 내용은 <xref:security/data-protection/configuration/overview>를 참조하세요.

* **사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**

  이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다. **사용자 프로필** 을 `True`로 설정합니다. `True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다. 키는 `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` 폴더에 저장됩니다.

  앱 풀의 [`setProfileEnvironment` 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다. `setProfileEnvironment` 의 기본값은 `true`입니다. Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다. 키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.

  1. `%windir%/system32/inetsrv/config` 폴더로 이동합니다.
  1. `applicationHost.config` 파일을 엽니다.
  1. `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.
  1. `setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.

* **파일 시스템을 키 링 저장소로 사용**

  [파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다. X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다. 자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.

  웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.

  * 모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.
  * 각 시스템에 X509 인증서를 배포합니다. [코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.

* **데이터 보호에 대한 머신 수준 정책 설정**

  데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [머신 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다. 자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.

## <a name="iis-configuration"></a>IIS 구성

**Windows Server 운영 체제**

**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.

1. **관리** 메뉴 또는 **서버 관리자** 의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다. **서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. **기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다. 원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   **Windows 인증(선택 사항)**  
   Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다. **Windows 인증** 기능을 선택합니다. 자세한 내용은 [Windows 인증 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.

   **WebSockets(선택 사항)**  
   WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다. WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다. **WebSocket 프로토콜** 기능을 선택합니다. 자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.

1. **확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다. **웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.

**Windows 데스크톱 운영 체제**

**IIS 관리 콘솔** 및 **World Wide Web 서비스** 를 사용하도록 설정합니다.

1. **제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함** (화면 왼쪽)으로 이동합니다.

1. **인터넷 정보 서비스** 노드를 엽니다. **웹 관리 도구** 노드를 엽니다.

1. **IIS 관리 콘솔** 확인란을 선택합니다.

1. **World Wide Web 서비스** 확인란을 선택합니다.

1. **World Wide Web 서비스** 의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.

   **Windows 인증(선택 사항)**  
   Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다. **Windows 인증** 기능을 선택합니다. 자세한 내용은 [Windows 인증 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.

   **WebSockets(선택 사항)**  
   WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다. WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다. **WebSocket 프로토콜** 기능을 선택합니다. 자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.

1. IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a>가상 디렉터리

[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다. 앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.

## <a name="sub-applications"></a>하위 애플리케이션

ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다. 하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.

하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다. 물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다. `/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다. 루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다. 요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.

정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다. 루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.

ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.

1. 하위 앱에 대한 앱 풀을 설정합니다. 데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.

1. 루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.

1. IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application** (애플리케이션으로 변환)을 선택합니다.

1. **Add Application** (애플리케이션 추가) 대화 상자에서 **애플리케이션 풀** 에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다. **확인** 을 선택합니다.

하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.

In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.

## <a name="application-pools"></a>애플리케이션 풀

앱 풀 격리는 호스팅 모델에 따라 결정됩니다.

* In-process 호스팅: 앱은 별도의 앱 풀에서 실행해야 합니다.
* Out-of-process 호스팅: 각 앱을 자체 앱 풀에서 실행하여 앱을 서로 격리하는 것이 좋습니다.

IIS **웹 사이트 추가** 대화 상자는 기본적으로 앱당 단일 앱 풀로 구성됩니다. **사이트 이름** 을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다. 사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.

## <a name="application-pool-no-locidentity"></a>애플리케이션 풀 Identity

응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다. IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다. IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 `ApplicationPoolIdentity`를 사용하도록 설정되어 있는지 확인합니다.

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다. 리소스는 이 ID를 사용하여 보호할 수 있습니다. 그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.

IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.

1. [Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.

1. 디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.

1. **보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.

1. **위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.

1. **선택할 개체 이름 입력** 영역에 자리 표시자 `{APP POOL NAME}`이 앱 풀 이름인 `IIS AppPool\{APP POOL NAME}` 형식을 입력합니다. **이름 확인** 단추를 선택합니다. *DefaultAppPool* 의 경우 `IIS AppPool\DefaultAppPool`을 사용하는 이름을 확인합니다. **이름 확인** 단추를 선택하면 개체 이름 영역에 `DefaultAppPool` 값이 표시됩니다. 개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다. 개체 이름을 확인할 때 자리 표시자 `{APP POOL NAME}`이 앱 풀 이름인 `IIS AppPool\{APP POOL NAME}` 형식을 사용합니다.

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. **확인** 을 선택합니다.

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. 읽기 및 실행 권한은 기본적으로 부여됩니다. 필요에 따라 추가 권한을 제공합니다.

**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다. *DefaultAppPool* 을 예로 들면, 다음 명령이 사용됩니다.

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.

## <a name="http2-support"></a>HTTP/2 지원

[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.

* In-Process
  * Windows Server 2016/Windows 10 이상, IIS 10 이상
  * TLS 1.2 이상 연결
* Out of Process
  * Windows Server 2016/Windows 10 이상, IIS 10 이상
  * 공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.
  * TLS 1.2 이상 연결

In-Process 배포에서 HTTP/2 연결이 설정된 경우 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다. Out-of-Process 배포에서 HTTP/2 연결이 설정된 경우 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.

In-process 및 out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.

HTTP/2는 기본적으로 사용됩니다. HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다. IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.

## <a name="cors-preflight-requests"></a>CORS 실행 전 요청

이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.

.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다. OPTIONS 요청을 전달하도록 `web.config`에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.

## <a name="application-initialization-module-and-idle-timeout"></a>애플리케이션 초기화 모듈 및 유휴 시간 제한

ASP.NET Core 모듈 버전 2에서 IIS에 호스트된 경우

* [애플리케이션 초기화 모듈](#application-initialization-module): 앱 호스팅 [In-process](xref:host-and-deploy/iis/in-process-hosting) 또는 [Out-of-process](xref:host-and-deploy/iis/out-of-process-hosting)가 작업자 프로세스를 다시 시작하거나 서버를 다시 시작할 때 자동으로 시작되도록 구성할 수 있습니다.
* [유휴 시간 제한](#idle-timeout): 앱의 호스티드 [In Process](xref:host-and-deploy/iis/in-process-hosting)가 비활성 기간 중에 시간 초과되지 않도록 구성할 수 있습니다.

### <a name="application-initialization-module"></a>애플리케이션 초기화 모듈

‘앱 호스팅 In Process 및 Out of Process에 적용됩니다.’

[IIS 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)는 앱 풀이 시작되거나 재활용될 때 HTTP 요청을 앱으로 보내는 IIS 기능입니다. 요청은 앱이 시작되도록 트리거합니다. 기본적으로 IIS는 앱의 루트 URL(`/`)에 요청을 실행하여 앱을 초기화합니다(구성에 대한 자세한 내용은 [추가 리소스](#application-initialization-module-and-idle-timeout-additional-resources) 참조).

IIS 애플리케이션 초기화 역할 기능이 사용하도록 설정되었는지 확인합니다.

Windows 7 이상 데스크톱 시스템에서 IIS를 로컬로 사용하는 경우

1. **제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함** (화면 왼쪽)으로 이동합니다.
1. **인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능** 을 엽니다.
1. **애플리케이션 초기화** 확인란을 선택합니다.

Windows Server 2008 R2 이상

1. **역할 및 기능 추가 마법사** 를 엽니다.
1. **역할 서비스 선택** 패널에서 **애플리케이션 개발** 노드를 엽니다.
1. **애플리케이션 초기화** 확인란을 선택합니다.

다음 방법 중 하나를 사용하여 사이트에 대해 애플리케이션 초기화 모듈을 활성화합니다.

* IIS 관리자 사용

  1. **연결** 패널에서 **애플리케이션 풀** 을 선택합니다.
  1. 목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정** 을 선택합니다.
  1. 기본 **시작 모드** 는 `OnDemand`입니다. **시작 모드** 를 `AlwaysRunning`으로 설정합니다. **확인** 을 선택합니다.
  1. **연결** 패널에서 **사이트** 노드를 엽니다.
  1. 앱을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 관리** > **고급 설정** 을 선택합니다.
  1. 기본 **미리 로드 사용** 설정은 `False`입니다. **미리 로드 사용** 을 `True`로 설정합니다. **확인** 을 선택합니다.

* `web.config`를 사용하여 `doAppInitAfterRestart`가 `true`로 설정된 `<applicationInitialization>` 요소를 앱의 `web.config` 파일에 있는 `<system.webServer>` 요소에 추가합니다.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>유휴 시간 제한

‘앱 호스팅 In Process 에만 적용됩니다.’

앱의 유휴 상태를 방지하려면 IIS 관리자를 사용하여 앱 풀의 유휴 시간 제한을 설정합니다.

1. **연결** 패널에서 **애플리케이션 풀** 을 선택합니다.
1. 목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정** 을 선택합니다.
1. 기본 **유휴 시간 제한(분)** 은 `20`분입니다. **유휴 시간 제한(분)** 을 `0`으로 설정합니다. **확인** 을 선택합니다.
1. 작업자 프로세스를 재순환합니다.

앱 호스팅 [Out of Process](xref:host-and-deploy/iis/out-of-process-hosting)가 시간 초과되지 않도록 하려면 다음 방법 중 하나를 사용합니다.

* 실행 상태를 유지하기 위해 외부 서비스에서 앱을 ping합니다.
* 앱이 백그라운드 서비스만 호스트하는 경우 IIS 호스팅을 방지하고, [Windows 서비스를 사용하여 ASP.NET Core 앱을 호스트](xref:host-and-deploy/windows-service)합니다.

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>애플리케이션 초기화 모듈 및 유휴 시간 제한 추가 리소스

* [IIS 8.0 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [애플리케이션 초기화 `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).
* [애플리케이션 풀의 프로세스 모델 설정 `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="module-schema-and-configuration-file-locations"></a>모듈, 스키마 및 구성 파일 위치

### <a name="module"></a>Module

**IIS(x86/amd64)** :

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express(x86/amd64)** :

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>스키마

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>Configuration

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* *iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

`applicationHost.config` 파일에서 `aspnetcore`를 검색하여 파일을 찾을 수 있습니다.
