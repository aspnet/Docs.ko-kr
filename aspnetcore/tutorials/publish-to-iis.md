---
title: IIS에 ASP.NET Core 앱 게시
author: rick-anderson
description: IIS 서버에서 ASP.NET Core 앱을 호스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
no-loc:
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: 40c47da472257862414ba33be582eb19d3f0b29c
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754556"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>IIS에 ASP.NET Core 앱 게시

이 자습서에서는 IIS 서버에서 ASP.NET Core 앱을 호스트하는 방법을 보여 줍니다.

이 자습서에서 다루는 주제는 다음과 같습니다.

> [!div class="checklist"]
> * Windows Server에 .NET Core 호스팅 번들을 설치합니다.
> * IIS 관리자에서 IIS 사이트를 만듭니다.
> * ASP.NET Core 앱을 배포합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [.NET Core SDK](/dotnet/core/sdk)가 개발 머신에 설치되어 있어야 합니다.
* Windows Server가 **웹 서버(IIS)** 서버 역할로 구성되어 있어야 합니다. 서버가 IIS를 사용하여 웹 사이트를 호스트하도록 구성되지 않은 경우 <xref:host-and-deploy/iis/index#iis-configuration> 문서의 ‘IIS 구성’ 섹션에 있는 지침을 따르고 이 자습서로 돌아옵니다.

> [!WARNING]
> **IIS 구성 및 웹 사이트 보안에는 이 자습서에서 다루지 않는 개념이 포함됩니다.** IIS에서 프로덕션 앱을 호스트하기 전에 [Microsoft IIS 설명서](https://www.iis.net/)의 IIS 지침 및 [IIS를 사용한 호스트에 대한 ASP.NET Core 문서](xref:host-and-deploy/iis/index)를 참조하세요.
>
> 이 자습서에서 다루지 않는 IIS 호스팅에 대한 중요한 시나리오는 다음과 같습니다.
>
> * [ASP.NET Core 데이터 보호에 대한 레지스트리 하이브 만들기](xref:host-and-deploy/iis/index#data-protection)
> * [앱 풀의 ACL(액세스 제어 목록) 구성](xref:host-and-deploy/iis/index#application-pool-identity)
> * IIS 배포 개념에 중점을 두기 위해 이 자습서에서는 IIS에서 HTTPS 보안을 구성하지 않고 앱을 배포합니다. HTTPS 프로토콜을 사용할 수 있는 앱을 호스트하는 방법에 대한 자세한 내용은 이 문서의 [추가 리소스](#additional-resources) 섹션에 있는 보안 항목을 참조하세요. ASP.NET Core 앱을 호스트하기 위한 추가 지침은 <xref:host-and-deploy/iis/index> 문서에 나와 있습니다.

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core 호스팅 번들 설치

IIS 서버에 *.NET Core 호스팅 번들*을 설치합니다. 번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다. 이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.

다음 링크를 사용하여 설치 관리자를 다운로드합니다.

[현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. IIS 서버에서 설치 관리자를 실행합니다.

1. 서버를 다시 시작하거나 명령 셸에서 `net stop was /y`, `net start w3svc`를 차례로 실행합니다.

## <a name="create-the-iis-site"></a>IIS 사이트 만들기

1. IIS 서버에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다. 다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다. 앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.

1. IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다. **사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다. 상황에 맞는 메뉴에서 **웹 사이트 추가**를 선택합니다.

1. **사이트 이름**을 입력하고 **실제 경로**를 만든 앱의 배포 폴더로 설정합니다. **바인딩** 구성을 제공하고 **확인**을 선택하여 웹 사이트를 만듭니다.

   > [!WARNING]
   > 최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다. 최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다. 강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다. 와일드카드보다는 명시적 호스트 이름을 사용합니다. 전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임). 자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.

1. 프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.

   앱 풀의 기본 ID(**프로세스 모델** >  **Identity** )가 `ApplicationPoolIdentity`에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다. 예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a>ASP.NET Core Razor Pages 앱 만들기

<xref:getting-started> 자습서에 따라 Razor Pages 앱을 만듭니다.

## <a name="publish-and-deploy-the-app"></a>앱 게시 및 배포

‘앱 게시’는 서버에서 호스트할 수 있는 컴파일된 앱을 생성하는 것을 의미합니다. ‘앱 배포’는 게시된 앱을 호스팅 시스템으로 이동하는 것을 의미합니다. 게시 단계는 [.NET Core SDK](/dotnet/core/sdk)에서 처리되지만, 배포 단계는 다양한 방법으로 처리될 수 있습니다. 이 자습서에서는 다음과 같은 ‘폴더’ 배포 방법을 채택합니다.
 
* 앱은 폴더에 게시됩니다.
* 폴더의 콘텐츠는 IIS 사이트의 폴더(IIS 관리자에 있는 사이트의 **실제 경로**)로 이동됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
1. **게시 대상 선택** 대화 상자에서 **폴더** 게시 옵션을 선택합니다.
1. **폴더 또는 파일 공유** 경로를 설정합니다.
   * 개발 머신에서 네트워크 공유로 사용할 수 있는 IIS 사이트의 폴더를 만든 경우 공유 경로를 제공합니다. 공유에 게시하려면 현재 사용자에게 쓰기 권한이 있어야 합니다.
   * IIS 서버의 IIS 사이트 폴더에 직접 배포할 수 없는 경우 이동식 미디어의 폴더에 게시하고 서버의 IIS 사이트 폴더(IIS 관리자에서 사이트의 **실제 경로**)로 게시된 앱을 실제로 이동합니다. `bin/Release/{TARGET FRAMEWORK}/publish` 폴더의 콘텐츠를 서버의 IIS 사이트 폴더(IIS 관리자에서 사이트의 **실제 경로**)로 이동합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. 명령 셸에서 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 릴리스 구성에 있는 앱을 게시합니다.

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. `bin/Release/{TARGET FRAMEWORK}/publish` 폴더의 콘텐츠를 서버의 IIS 사이트 폴더(IIS 관리자에서 사이트의 **실제 경로**)로 이동합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

1. **솔루션**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** > **폴더에 게시**를 선택합니다.
1. **폴더 선택** 경로를 설정합니다.
   * 개발 머신에서 네트워크 공유로 사용할 수 있는 IIS 사이트의 폴더를 만든 경우 공유 경로를 제공합니다. 공유에 게시하려면 현재 사용자에게 쓰기 권한이 있어야 합니다.
   * IIS 서버의 IIS 사이트 폴더에 직접 배포할 수 없는 경우 이동식 미디어의 폴더에 게시하고 서버의 IIS 사이트 폴더(IIS 관리자에서 사이트의 **실제 경로**)로 게시된 앱을 실제로 이동합니다. `bin/Release/{TARGET FRAMEWORK}/publish` 폴더의 콘텐츠를 서버의 IIS 사이트 폴더(IIS 관리자에서 사이트의 **실제 경로**)로 이동합니다.

---

## <a name="browse-the-website"></a>웹 사이트 찾아보기

앱은 첫 번째 요청을 받은 후 브라우저에서 액세스할 수 있습니다. IIS 관리자에서 사이트에 대해 설정한 엔드포인트 바인딩에서 앱에 대한 요청을 만듭니다.

## <a name="next-steps"></a>다음 단계

본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.

> [!div class="checklist"]
> * Windows Server에 .NET Core 호스팅 번들을 설치합니다.
> * IIS 관리자에서 IIS 사이트를 만듭니다.
> * ASP.NET Core 앱을 배포합니다.

IIS에서 ASP.NET Core 앱을 호스트하는 방법에 대한 자세한 내용은 IIS 개요 문서를 참조하세요.

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>추가 자료

### <a name="articles-in-the-aspnet-core-documentation-set"></a>ASP.NET Core 설명서 세트의 문서

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>ASP.NET Core 앱 배포와 관련된 문서

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Mac용 Visual Studio를 사용하여 폴더에 웹앱 게시](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>IIS HTTPS 구성에 대한 문서

* [IIS 관리자에서 SSL 구성](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [IIS에서 SSL을 설정하는 방법](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>IIS 및 Windows Server에 대한 문서

* [공식 Microsoft IIS 사이트](https://www.iis.net/)
* [Windows Server 기술 콘텐츠 라이브러리](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a>IIS 관리자를 위한 배포 리소스

* [IIS 설명서](/iis)
* [IIS에서 IIS 관리자 시작](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

