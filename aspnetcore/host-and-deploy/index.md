---
title: ASP.NET Core 호스트 및 배포
author: rick-anderson
description: 호스팅 환경을 설정하고 ASP.NET Core 앱을 배포하는 방법을 알아봅니다.
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
uid: host-and-deploy/index
ms.openlocfilehash: 19e888859cea35624491a516404c57e30aa9db05
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057221"
---
# <a name="host-and-deploy-aspnet-core"></a>ASP.NET Core 호스트 및 배포

::: moniker range=">= aspnetcore-2.2"

일반적으로 ASP.NET Core 앱을 호스팅 환경에 배포하기 위해서는 다음을 수행합니다.

* 게시한 앱을 호스팅 서버의 폴더에 배포합니다.
* 요청이 도착할 때 앱을 시작하고 작동이 중단되거나 서버가 다시 부팅된 후 앱을 다시 시작하는 프로세스 관리자를 설정합니다.
* 역항뱡 프록시 구성이 필요한 경우, 요청을 앱으로 전달하는 역방향 프록시를 설정합니다.

## <a name="publish-to-a-folder"></a>폴더에 게시

[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 앱 코드를 컴파일하고 앱을 실행하는 데 필요한 파일을 *publish* 폴더로 복사합니다. Visual Studio에서 배포하는 경우에는 파일이 배포 대상으로 복사되기 전에 `dotnet publish` 단계가 자동으로 수행됩니다.

### <a name="folder-contents"></a>폴더 콘텐츠

*publish* 폴더에는 하나 이상의 앱 어셈블리 파일, 종속성, 그리고 선택적으로 .NET 런타임이 포함되어 있습니다.

.NET Core 앱은 *자체 포함된 배포* 또는 *프레임워크 종속 배포* 로 게시할 수 있습니다. 앱이 자체 포함된 배포인 경우, .NET 런타임이 포함된 어셈블리 파일은 *publish* 폴더에 들어 있습니다. 앱이 프레임워크 종속인 경우 서버에 설치된 .NET 버전에 대한 참조가 앱에 포함되므로 .NET 런타임 파일이 포함되지 않습니다. 기본 배포 모델은 프레임워크 종속입니다. 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.

*.exe* 및 *.dll* 파일 이외에 ASP.NET Core 앱에 대한 *publish* 폴더에는 일반적으로 구성 파일, 정적 자산 및 MVC 뷰가 포함됩니다. 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.

## <a name="set-up-a-process-manager"></a>프로세스 관리자 설정

ASP.NET Core 앱은 서버가 부팅되고 작동 중단 후 다시 시작될 때 시작되어야 하는 콘솔 앱입니다. 시작 및 다시 시작을 자동화하려면 프로세스 관리자가 필요합니다. ASP.NET Core에 대한 가장 일반적인 프로세스 관리자는 다음과 같습니다.

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Windows 서비스](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>역방향 프록시 설정

앱에서 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 사용하는 경우 [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) 또는 [IIS](xref:host-and-deploy/iis/index)를 역방향 프록시 서버로 사용할 수 있습니다. 역방향 프록시 서버는 인터넷에서 HTTP 요청을 받아서 Kestrel에 전달합니다.

&mdash;역방향 프록시 서버가 있는 구성과 없는 구성 모두&mdash; 지원되는 호스팅 구성입니다. 자세한 내용은 [Kestrel를 역방향 프록시와 함께 사용할 경우](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)를 참조하세요.

## <a name="proxy-server-and-load-balancer-scenarios"></a>프록시 서버 및 부하 분산 장치 시나리오

프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다. 추가 구성이 없는 앱에는 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소에 대한 액세스 권한이 없을 수 있습니다. 자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Visual Studio 및 MSBuild를 사용하여 배포 자동화

일반적으로 배포에는 [dotnet publish](/dotnet/core/tools/dotnet-publish)에서 서버로 출력을 복사하는 것 외에 추가 작업이 필요합니다. 예를 들어 추가 파일이 필요하거나 *publish* 폴더에서 제외될 수 있습니다. Visual Studio에서는 웹 배포에 [MSBuild](/visualstudio/msbuild/msbuild)를 사용하고 MSBuild를 사용자 지정하여 배포 중에 많은 다른 작업을 수행할 수 있습니다. 자세한 내용은 <xref:host-and-deploy/visual-studio-publish-profiles> 및 [Using MSBuild and Team Foundation Build](http://msbuildbook.com/)(MSBuild 및 Team Foundation Build 사용) 책을 참조하세요.

[웹 게시 기능](xref:tutorials/publish-to-azure-webapp-using-vs)을 사용하거나 [기본 제공 Git 지원](xref:host-and-deploy/azure-apps/azure-continuous-deployment)을 사용하여 Visual Studio에서 Azure App Service로 앱을 직접 배포할 수 있습니다. Azure DevOps Services는 [Azure App Service에 지속적인 배포](/azure/devops/pipelines/targets/webapp)를 지원합니다. 자세한 내용은 [ASP.NET Core 및 Azure에서 DevOps](xref:azure/devops/index)를 참조하세요.

## <a name="publish-to-azure"></a>Azure에 게시

Visual Studio를 사용하여 Azure에 앱을 게시하는 방법은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요. 추가 예제는 [Azure에서 ASP.NET Core 웹앱 만들기](/azure/app-service/app-service-web-get-started-dotnet)에서 제공됩니다.

## <a name="publish-with-msdeploy-on-windows"></a>Windows에서 MSDeploy를 사용하여 게시

[dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 명령을 사용하여 Windows 명령 프롬프트에서 수행하는 방법을 비롯하여 Visual Studio 게시 프로필을 사용하여 앱을 게시하는 방법에 대한 지침은 <xref:host-and-deploy/visual-studio-publish-profiles>를 참조하세요.

## <a name="internet-information-services-iis"></a>IIS(인터넷 정보 서비스)

*web.config* 파일에 제공된 구성을 사용하여 IIS(인터넷 정보 서비스)에 배포하는 경우 <xref:host-and-deploy/iis/index>의 문서를 참조하세요.

## <a name="host-in-a-web-farm"></a>웹 팜에서 호스트

웹 팜 환경에서 ASP.NET Core 앱을 호스팅하는 구성에 대한 자세한 내용(예: 확장성을 위해 앱의 여러 인스턴스 배포)은 <xref:host-and-deploy/web-farm>을 참조하세요.

## <a name="host-on-docker"></a>Docker에서 호스트

자세한 내용은 <xref:host-and-deploy/docker/index>를 참조하세요.

## <a name="perform-health-checks"></a>상태 검사 수행

상태 검사 미들웨어를 사용하여 앱 및 그 종속성에 대해 상태 검사를 수행합니다. 자세한 내용은 <xref:host-and-deploy/health-checks>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:test/troubleshoot>
* [ASP.NET 호스팅](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

일반적으로 ASP.NET Core 앱을 호스팅 환경에 배포하기 위해서는 다음을 수행합니다.

* 게시한 앱을 호스팅 서버의 폴더에 배포합니다.
* 요청이 도착할 때 앱을 시작하고 작동이 중단되거나 서버가 다시 부팅된 후 앱을 다시 시작하는 프로세스 관리자를 설정합니다.
* 역항뱡 프록시 구성이 필요한 경우, 요청을 앱으로 전달하는 역방향 프록시를 설정합니다.

## <a name="publish-to-a-folder"></a>폴더에 게시

[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 앱 코드를 컴파일하고 앱을 실행하는 데 필요한 파일을 *publish* 폴더로 복사합니다. Visual Studio에서 배포하는 경우에는 파일이 배포 대상으로 복사되기 전에 `dotnet publish` 단계가 자동으로 수행됩니다.

### <a name="folder-contents"></a>폴더 콘텐츠

*publish* 폴더에는 하나 이상의 앱 어셈블리 파일, 종속성, 그리고 선택적으로 .NET 런타임이 포함되어 있습니다.

.NET Core 앱은 *자체 포함된 배포* 또는 *프레임워크 종속 배포* 로 게시할 수 있습니다. 앱이 자체 포함된 배포인 경우, .NET 런타임이 포함된 어셈블리 파일은 *publish* 폴더에 들어 있습니다. 앱이 프레임워크 종속인 경우 서버에 설치된 .NET 버전에 대한 참조가 앱에 포함되므로 .NET 런타임 파일이 포함되지 않습니다. 기본 배포 모델은 프레임워크 종속입니다. 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.

*.exe* 및 *.dll* 파일 이외에 ASP.NET Core 앱에 대한 *publish* 폴더에는 일반적으로 구성 파일, 정적 자산 및 MVC 뷰가 포함됩니다. 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.

## <a name="set-up-a-process-manager"></a>프로세스 관리자 설정

ASP.NET Core 앱은 서버가 부팅되고 작동 중단 후 다시 시작될 때 시작되어야 하는 콘솔 앱입니다. 시작 및 다시 시작을 자동화하려면 프로세스 관리자가 필요합니다. ASP.NET Core에 대한 가장 일반적인 프로세스 관리자는 다음과 같습니다.

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Windows 서비스](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>역방향 프록시 설정

앱에서 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 사용하는 경우 [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) 또는 [IIS](xref:host-and-deploy/iis/index)를 역방향 프록시 서버로 사용할 수 있습니다. 역방향 프록시 서버는 인터넷에서 HTTP 요청을 받아서 Kestrel에 전달합니다.

&mdash;역방향 프록시 서버가 있는 구성과 없는 구성 모두&mdash; 지원되는 호스팅 구성입니다. 자세한 내용은 [Kestrel를 역방향 프록시와 함께 사용할 경우](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)를 참조하세요.

## <a name="proxy-server-and-load-balancer-scenarios"></a>프록시 서버 및 부하 분산 장치 시나리오

프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다. 추가 구성이 없는 앱에는 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소에 대한 액세스 권한이 없을 수 있습니다. 자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Visual Studio 및 MSBuild를 사용하여 배포 자동화

일반적으로 배포에는 [dotnet publish](/dotnet/core/tools/dotnet-publish)에서 서버로 출력을 복사하는 것 외에 추가 작업이 필요합니다. 예를 들어 추가 파일이 필요하거나 *publish* 폴더에서 제외될 수 있습니다. Visual Studio에서는 웹 배포에 MSBuild를 사용하고 MSBuild를 사용자 지정하여 배포 중에 많은 다른 작업을 수행할 수 있습니다. 자세한 내용은 <xref:host-and-deploy/visual-studio-publish-profiles> 및 [Using MSBuild and Team Foundation Build](http://msbuildbook.com/)(MSBuild 및 Team Foundation Build 사용) 책을 참조하세요.

[웹 게시 기능](xref:tutorials/publish-to-azure-webapp-using-vs)을 사용하거나 [기본 제공 Git 지원](xref:host-and-deploy/azure-apps/azure-continuous-deployment)을 사용하여 Visual Studio에서 Azure App Service로 앱을 직접 배포할 수 있습니다. Azure DevOps Services는 [Azure App Service에 지속적인 배포](/azure/devops/pipelines/targets/webapp)를 지원합니다. 자세한 내용은 [ASP.NET Core 및 Azure에서 DevOps](xref:azure/devops/index)를 참조하세요.

## <a name="publish-to-azure"></a>Azure에 게시

Visual Studio를 사용하여 Azure에 앱을 게시하는 방법은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요. 추가 예제는 [Azure에서 ASP.NET Core 웹앱 만들기](/azure/app-service/app-service-web-get-started-dotnet)에서 제공됩니다.

## <a name="publish-with-msdeploy-on-windows"></a>Windows에서 MSDeploy를 사용하여 게시

[dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 명령을 사용하여 Windows 명령 프롬프트에서 수행하는 방법을 비롯하여 Visual Studio 게시 프로필을 사용하여 앱을 게시하는 방법에 대한 지침은 <xref:host-and-deploy/visual-studio-publish-profiles>를 참조하세요.

## <a name="internet-information-services-iis"></a>IIS(인터넷 정보 서비스)

*web.config* 파일에 제공된 구성을 사용하여 IIS(인터넷 정보 서비스)에 배포하는 경우 <xref:host-and-deploy/iis/index>의 문서를 참조하세요.

## <a name="host-in-a-web-farm"></a>웹 팜에서 호스트

웹 팜 환경에서 ASP.NET Core 앱을 호스팅하는 구성에 대한 자세한 내용(예: 확장성을 위해 앱의 여러 인스턴스 배포)은 <xref:host-and-deploy/web-farm>을 참조하세요.

## <a name="host-on-docker"></a>Docker에서 호스트

자세한 내용은 <xref:host-and-deploy/docker/index>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:test/troubleshoot>
* [ASP.NET 호스팅](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
