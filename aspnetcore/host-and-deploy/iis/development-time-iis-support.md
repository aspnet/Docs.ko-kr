---
title: ASP.NET Core용 Visual Studio의 개발 시간 IIS 지원
author: rick-anderson
description: Windows Server에서 IIS를 통해 실행될 경우 ASP.NET Core 앱 디버그에 대한 지원을 확인해 보세요.
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
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: ab892b2cdfa61378ac7328c0380c8a6cffc6d376
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058456"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>ASP.NET Core용 Visual Studio의 개발 시간 IIS 지원

작성자: [Sourabh Shirhatti](https://twitter.com/sshirhatti)

::: moniker range=">= aspnetcore-3.0"

이 문서에서는 Windows Server에서 IIS를 통해 실행되는 ASP.NET Core 앱을 디버그하기 위한 [Visual Studio](https://visualstudio.microsoft.com) 지원에 대해 설명합니다. 이 항목에서는 이 시나리오를 사용하도록 설정하고 프로젝트를 설정하는 방법을 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Windows용 Visual Studio](https://visualstudio.microsoft.com/downloads/)
* **ASP.NET 및 웹 개발** 워크로드
* **.NET Core 플랫폼 간 개발** 워크로드
* X.509 보안 인증서(HTTPS 지원용)

## <a name="enable-iis"></a>IIS 활성화

1. Windows에서 **제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.
1. **인터넷 정보 서비스** 확인란을 선택합니다. **확인** 을 선택합니다.

IIS를 설치하려면 시스템을 다시 시작해야 할 수 있습니다.

## <a name="configure-iis"></a>IIS 구성

IIS에서 웹 사이트는 다음과 같이 구성되어야 합니다.

* **호스트 이름**: 일반적으로 **기본 웹 사이트** 는 `localhost`의 **호스트 이름** 과 함께 사용됩니다. 그러나 고유한 호스트 이름이 있는 모든 유효한 IIS 웹 사이트가 작동합니다.
* **사이트 바인딩**
  * HTTPS가 필요한 앱의 경우 인증서를 사용하여 포트 443에 대한 바인딩을 만듭니다. 일반적으로 **IIS Express 개발 인증서** 가 사용되지만 모든 유효한 인증서가 작동합니다.
  * HTTP를 사용하는 앱의 경우 포트 80에 대한 바인딩이 있는지 확인하거나 새 사이트에 대해 포트 80에 대한 바인딩을 만듭니다.
  * HTTP 또는 HTTPS에 단일 바인딩을 사용합니다. **HTTP 및 HTTPS 포트에 대한 바인딩은 동시에 지원되지 않습니다.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Visual Studio에서 개발 시간 IIS 지원 사용

1. Visual Studio 설치 관리자를 시작합니다.
1. IIS 개발 시 지원에 사용할 Visual Studio 설치에 대해 **수정** 을 선택합니다.
1. **ASP.NET 및 웹 개발** 워크로드의 경우 **개발 시 IIS 지원** 구성 요소를 찾고 설치합니다.

   구성 요소는 워크로드 오른쪽 **설치 세부 정보** 패널의 **개발 시 IIS 지원** 아래 **선택 사항** 섹션에 나열됩니다. 이 구성 요소는 IIS를 사용하여 ASP.NET Core 앱을 실행하는 데 필요한 네이티브 IIS 모듈인 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.

## <a name="configure-the-project"></a>프로젝트 구성

### <a name="https-redirection"></a>HTTPS 리디렉션

HTTPS가 필요한 새 프로젝트의 경우 **새 ASP.NET Core 웹 애플리케이션 만들기** 창에서 **HTTPS에 대한 구성** 확인란을 선택합니다. 확인란을 선택하면 [HTTPS 리디렉션 및 HSTS 미들웨어](xref:security/enforcing-ssl)가 생성될 때 앱에 추가됩니다.

HTTPS가 필요한 기존 프로젝트의 경우 `Startup.Configure`에서 HTTPS 리디렉션 및 HSTS Middleware를 사용합니다. 자세한 내용은 <xref:security/enforcing-ssl>를 참조하세요.

HTTP를 사용하는 프로젝트의 경우 [HTTPS 리디렉션 및 HSTS 미들웨어](xref:security/enforcing-ssl)가 앱에 추가되지 않습니다. 앱 구성이 필요하지 않습니다.

### <a name="iis-launch-profile"></a>IIS 시작 프로필

새 시작 프로필을 만들어 개발 시간 IIS 지원을 추가합니다.

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **속성** 을 선택합니다. **디버그** 탭을 엽니다.
1. **프로필** 의 경우 **새로 만들기** 단추를 선택합니다. 팝업 창에서 프로필 이름을 “IIS”로 지정합니다. **확인** 을 선택하여 프로필을 만듭니다.
1. **시작** 설정의 경우 목록에서 **IIS** 를 선택합니다.
1. **브라우저 시작** 확인란을 선택하고 엔드포인트 URL을 제공합니다.

   앱에 HTTPS가 필요한 경우 HTTPS 엔드포인트(`https://`)를 사용합니다. HTTP의 경우 HTTP(`http://`) 엔드포인트를 사용합니다.

   [이전에 지정된 IIS 구성에서 사용하는](#configure-iis) 동일한 호스트 이름 및 포트를 제공합니다(일반적으로 `localhost`).

   URL의 끝에 있는 앱 이름을 제공합니다.

   예를 들어 `https://localhost/WebApplication1`(HTTPS) 또는 `http://localhost/WebApplication1`(HTTP)은 유효한 엔드포인트 URL입니다.
1. **환경 변수** 섹션에서 **추가** 단추를 선택합니다. **이름**`ASPNETCORE_ENVIRONMENT` 및 **값**`Development`를 사용하여 환경 변수를 제공합니다.
1. **웹 서버 설정** 영역에서 **앱 URL** 을 **브라우저 시작** 엔드포인트 URL에 사용된 동일한 값으로 설정합니다.
1. Visual Studio 2019 이상에 있는 **호스팅 모델** 설정의 경우 **기본값** 을 선택하여 프로젝트에서 사용되는 호스팅 모델을 사용합니다. 프로젝트가 프로젝트 파일에서 `<AspNetCoreHostingModel>` 속성을 설정하면 해당 속성 값(`InProcess` 또는 `OutOfProcess`)이 사용됩니다. 속성이 없으면 앱의 기본 호스팅 모델인 in-process가 사용됩니다. 앱의 일반 호스팅 모델과 다른 명시적 호스팅 모델 설정이 앱에 필요한 경우에는 필요에 따라 **호스팅 모델** 을 `In Process` 또는 `Out Of Process`로 설정합니다.
1. 프로필을 저장합니다.

Visual Studio를 사용하지 않는 경우에는 *속성* 폴더의 [launchSettings.json](https://json.schemastore.org/launchsettings) 파일에 시작 프로필을 수동으로 추가합니다. 다음 예제에서는 HTTPS 프로토콜을 사용하도록 프로필을 구성합니다.

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

`applicationUrl` 및 `launchUrl` 엔드포인트가 일치하고 IIS 바인딩 구성과 동일한 프로토콜(HTTP 또는 HTTPS)을 사용하는지 확인합니다.

## <a name="run-the-project"></a>프로젝트 실행

관리자 권한으로 Visual Studio를 실행합니다.

* 빌드 구성 드롭다운 목록이 **디버그** 로 설정되었는지 확인합니다.
* [디버깅 시작 단추](/visualstudio/debugger/debugger-feature-tour)를 **IIS** 프로필로 설정하고 단추를 선택하여 앱을 시작합니다.

관리자로 실행하고 있지 않으면 Visual Studio에서 다시 시작하라는 메시지가 표시될 수 있습니다. 메시지가 표시되면 Visual Studio를 다시 시작합니다.

신뢰할 수 없는 개발 인증서를 사용하는 경우 브라우저에서 신뢰할 수 없는 인증서에 대한 예외를 만들어야 할 수 있습니다.

> [!NOTE]
> [내 코드만](/visualstudio/debugger/just-my-code) 및 컴파일러 최적화를 사용하여 릴리스 빌드 구성을 디버그하면 성능이 저하됩니다. 예를 들어 중단점이 적중되지 않습니다.

## <a name="additional-resources"></a>추가 자료

* [IIS에서 IIS 관리자 시작](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 문서에서는 Windows Server에서 IIS를 통해 실행되는 ASP.NET Core 앱을 디버그하기 위한 [Visual Studio](https://visualstudio.microsoft.com) 지원에 대해 설명합니다. 이 항목에서는 이 시나리오를 사용하도록 설정하고 프로젝트를 설정하는 방법을 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Windows용 Visual Studio](https://visualstudio.microsoft.com/downloads/)
* **ASP.NET 및 웹 개발** 워크로드
* **.NET Core 플랫폼 간 개발** 워크로드
* X.509 보안 인증서(HTTPS 지원용)

## <a name="enable-iis"></a>IIS 활성화

1. Windows에서 **제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.
1. **인터넷 정보 서비스** 확인란을 선택합니다. **확인** 을 선택합니다.

IIS를 설치하려면 시스템을 다시 시작해야 할 수 있습니다.

## <a name="configure-iis"></a>IIS 구성

IIS에서 웹 사이트는 다음과 같이 구성되어야 합니다.

* **호스트 이름**: 일반적으로 **기본 웹 사이트** 는 `localhost`의 **호스트 이름** 과 함께 사용됩니다. 그러나 고유한 호스트 이름이 있는 모든 유효한 IIS 웹 사이트가 작동합니다.
* **사이트 바인딩**
  * HTTPS가 필요한 앱의 경우 인증서를 사용하여 포트 443에 대한 바인딩을 만듭니다. 일반적으로 **IIS Express 개발 인증서** 가 사용되지만 모든 유효한 인증서가 작동합니다.
  * HTTP를 사용하는 앱의 경우 포트 80에 대한 바인딩이 있는지 확인하거나 새 사이트에 대해 포트 80에 대한 바인딩을 만듭니다.
  * HTTP 또는 HTTPS에 단일 바인딩을 사용합니다. **HTTP 및 HTTPS 포트에 대한 바인딩은 동시에 지원되지 않습니다.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Visual Studio에서 개발 시간 IIS 지원 사용

1. Visual Studio 설치 관리자를 시작합니다.
1. IIS 개발 시 지원에 사용할 Visual Studio 설치에 대해 **수정** 을 선택합니다.
1. **ASP.NET 및 웹 개발** 워크로드의 경우 **개발 시 IIS 지원** 구성 요소를 찾고 설치합니다.

   구성 요소는 워크로드 오른쪽 **설치 세부 정보** 패널의 **개발 시 IIS 지원** 아래 **선택 사항** 섹션에 나열됩니다. 이 구성 요소는 IIS를 사용하여 ASP.NET Core 앱을 실행하는 데 필요한 네이티브 IIS 모듈인 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.

## <a name="configure-the-project"></a>프로젝트 구성

### <a name="https-redirection"></a>HTTPS 리디렉션

HTTPS가 필요한 새 프로젝트의 경우 **새 ASP.NET Core 웹 애플리케이션 만들기** 창에서 **HTTPS에 대한 구성** 확인란을 선택합니다. 확인란을 선택하면 [HTTPS 리디렉션 및 HSTS 미들웨어](xref:security/enforcing-ssl)가 생성될 때 앱에 추가됩니다.

HTTPS가 필요한 기존 프로젝트의 경우 `Startup.Configure`에서 HTTPS 리디렉션 및 HSTS Middleware를 사용합니다. 자세한 내용은 <xref:security/enforcing-ssl>를 참조하세요.

HTTP를 사용하는 프로젝트의 경우 [HTTPS 리디렉션 및 HSTS 미들웨어](xref:security/enforcing-ssl)가 앱에 추가되지 않습니다. 앱 구성이 필요하지 않습니다.

### <a name="iis-launch-profile"></a>IIS 시작 프로필

새 시작 프로필을 만들어 개발 시간 IIS 지원을 추가합니다.

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **속성** 을 선택합니다. **디버그** 탭을 엽니다.
1. **프로필** 의 경우 **새로 만들기** 단추를 선택합니다. 팝업 창에서 프로필 이름을 “IIS”로 지정합니다. **확인** 을 선택하여 프로필을 만듭니다.
1. **시작** 설정의 경우 목록에서 **IIS** 를 선택합니다.
1. **브라우저 시작** 확인란을 선택하고 엔드포인트 URL을 제공합니다.

   앱에 HTTPS가 필요한 경우 HTTPS 엔드포인트(`https://`)를 사용합니다. HTTP의 경우 HTTP(`http://`) 엔드포인트를 사용합니다.

   [이전에 지정된 IIS 구성에서 사용하는](#configure-iis) 동일한 호스트 이름 및 포트를 제공합니다(일반적으로 `localhost`).

   URL의 끝에 있는 앱 이름을 제공합니다.

   예를 들어 `https://localhost/WebApplication1`(HTTPS) 또는 `http://localhost/WebApplication1`(HTTP)은 유효한 엔드포인트 URL입니다.
1. **환경 변수** 섹션에서 **추가** 단추를 선택합니다. **이름**`ASPNETCORE_ENVIRONMENT` 및 **값**`Development`를 사용하여 환경 변수를 제공합니다.
1. **웹 서버 설정** 영역에서 **앱 URL** 을 **브라우저 시작** 엔드포인트 URL에 사용된 동일한 값으로 설정합니다.
1. Visual Studio 2019 이상에 있는 **호스팅 모델** 설정의 경우 **기본값** 을 선택하여 프로젝트에서 사용되는 호스팅 모델을 사용합니다. 프로젝트가 프로젝트 파일에서 `<AspNetCoreHostingModel>` 속성을 설정하면 해당 속성 값(`InProcess` 또는 `OutOfProcess`)이 사용됩니다. 속성이 없으면 앱의 기본 호스팅 모델인 out-of-process가 사용됩니다. 앱의 일반 호스팅 모델과 다른 명시적 호스팅 모델 설정이 앱에 필요한 경우에는 필요에 따라 **호스팅 모델** 을 `In Process` 또는 `Out Of Process`로 설정합니다.
1. 프로필을 저장합니다.

Visual Studio를 사용하지 않는 경우에는 *속성* 폴더의 [launchSettings.json](https://json.schemastore.org/launchsettings) 파일에 시작 프로필을 수동으로 추가합니다. 다음 예제에서는 HTTPS 프로토콜을 사용하도록 프로필을 구성합니다.

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

`applicationUrl` 및 `launchUrl` 엔드포인트가 일치하고 IIS 바인딩 구성과 동일한 프로토콜(HTTP 또는 HTTPS)을 사용하는지 확인합니다.

## <a name="run-the-project"></a>프로젝트 실행

관리자 권한으로 Visual Studio를 실행합니다.

* 빌드 구성 드롭다운 목록이 **디버그** 로 설정되었는지 확인합니다.
* [디버깅 시작 단추](/visualstudio/debugger/debugger-feature-tour)를 **IIS** 프로필로 설정하고 단추를 선택하여 앱을 시작합니다.

관리자로 실행하고 있지 않으면 Visual Studio에서 다시 시작하라는 메시지가 표시될 수 있습니다. 메시지가 표시되면 Visual Studio를 다시 시작합니다.

신뢰할 수 없는 개발 인증서를 사용하는 경우 브라우저에서 신뢰할 수 없는 인증서에 대한 예외를 만들어야 할 수 있습니다.

> [!NOTE]
> [내 코드만](/visualstudio/debugger/just-my-code) 및 컴파일러 최적화를 사용하여 릴리스 빌드 구성을 디버그하면 성능이 저하됩니다. 예를 들어 중단점이 적중되지 않습니다.

## <a name="additional-resources"></a>추가 자료

* [IIS에서 IIS 관리자 시작](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
