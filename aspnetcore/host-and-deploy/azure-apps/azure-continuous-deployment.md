---
title: ASP.NET Core와 함께 Visual Studio 및 Git을 사용하여 Azure에 지속적인 배포
author: rick-anderson
description: Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 연속 배포를 위한 Git을 사용하여 Azure App Service에 배포하는 방법을 알아봅니다.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 12/06/2018
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
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 1485e1a97e51bb4d12d0d74ca1550e5cf18258d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053789"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>ASP.NET Core와 함께 Visual Studio 및 Git을 사용하여 Azure에 지속적인 배포

작성자: [Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

이 자습서에서는 Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 지속적인 배포를 사용하여 Visual Studio에서 Azure App Service에 배포하는 방법을 알아봅니다.

Azure DevOps Services를 사용하여 [Azure App Service](/azure/app-service/app-service-web-overview)에 대한 CD(지속적인 업데이트) 워크플로를 구성하는 방법을 보여 주는 [Azure Pipelines를 사용하여 첫 번째 파이프라인 만들기](/azure/devops/pipelines/get-started-yaml)를 참조하세요. Azure Pipelines는 Azure DevOps Services의 서비스 중 하나로, Azure App Service에서 호스트되는 앱의 업데이트를 게시하는 강력한 배포 파이프라인을 간단하게 설정합니다. 파이프라인을 빌드하고, 테스트를 실행하고, 스테이징 슬롯에 배포하고, 프로덕션에 배포하도록 Azure Portal에서 구성할 수 있습니다.

> [!NOTE]
> 이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정을 얻으려면 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F)하거나 [평가판에 등록](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F)합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 다음 소프트웨어가 설치되어 있다고 가정합니다.

* [Visual Studio](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* Windows용 [Git](https://git-scm.com/downloads)

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core 웹앱 만들기

1. Visual Studio를 시작합니다.

1. **파일** 메뉴에서 **새로 만들기** > **프로젝트** 를 선택합니다.

1. **ASP.NET Core 웹 애플리케이션** 프로젝트 템플릿을 선택합니다. **설치됨** > **템플릿** > **Visual C#**  >  **.NET Core** 아래에 표시됩니다. 프로젝트 이름을 `SampleWebAppDemo`로 지정합니다. **새 Git 리포지토리 만들기** 옵션을 선택하고 **확인** 을 클릭합니다.

   ![새 프로젝트 대화 상자](azure-continuous-deployment/_static/01-new-project.png)

1. **새 ASP.NET Core 프로젝트** 대화 상자에서 ASP.NET Core **빈** 템플릿을 선택한 후 **확인** 을 클릭합니다.

   ![새 ASP.NET Core 프로젝트 대화 상자](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> 최신 .NET Core 릴리스는 2.0입니다.

### <a name="running-the-web-app-locally"></a>로컬로 웹앱 실행

1. Visual Studio에서 앱 만들기를 완료하면 **디버그** > **디버깅 시작** 을 선택하여 앱을 실행합니다. 또는 **F5** 키를 누릅니다.

   Visual Studio 및 새 앱을 초기화하는 데 시간이 걸릴 수 있습니다. 완료되면 브라우저에 실행 중인 앱이 표시됩니다.

   ![브라우저 창에서는 'Hello World!'을 표시하는 애플리케이션이 실행 중이라고 표시합니다.](azure-continuous-deployment/_static/04-browser-runapp.png)

1. 실행 중인 웹앱을 검토한 후 브라우저를 닫고 Visual Studio의 도구 모음에서 “디버깅 중지” 아이콘을 선택하여 앱을 중지합니다.

## <a name="create-a-web-app-in-the-azure-portal"></a>Azure Portal에서 웹앱 만들기

다음 단계에서는 Azure Portal에서 웹앱을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 포털 인터페이스의 왼쪽 위에 있는 **새로 만들기** 를 선택합니다.

1. **웹 + 모바일** > **웹앱** 을 선택합니다.

   ![Microsoft Azure Portal: 새 단추: Marketplace 아래의 웹 + 모바일: 추천 앱 아래의 Web App 단추](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. **Web App** 블레이드에서 **App Service 이름** 에 고유한 값을 입력합니다.

   ![Web App 블레이드](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > **App Service 이름** 이름은 고유해야 합니다. 이름이 제공되면 포털에서 이 규칙이 적용됩니다. 다른 값을 제공하는 경우 이 자습서에서 표시되는 각 **SampleWebAppDemo** 를 해당 값으로 대체합니다.

   또한 **Web App** 블레이드에서 기존 **App Service 계획/위치** 를 선택하거나 새로 만듭니다. 새 플랜을 만들 경우 가격 책정 계층, 위치 및 기타 옵션을 선택합니다. App Service 계획에 대한 자세한 내용은 [Azure App Service 계획 세부 개요](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)를 참조하세요.

1. **만들기** 를 선택합니다. Azure에서는 웹앱을 프로비전하고 시작합니다.

   ![Azure Portal: 샘플 Web App 데모 01 Essentials 블레이드](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>새 웹앱에 대한 Git 게시 사용

Git은 Azure App Service 웹앱을 배포하는 데 사용할 수 있는 분산 버전 제어 시스템입니다. 웹앱 코드는 로컬 Git 리포지토리에 저장되고 코드는 원격 리포지토리로 푸시하여 Azure에 배포됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **App Services** 를 선택하여 Azure 구독과 연결된 App Service 목록을 표시합니다.

1. 이 자습서의 이전 섹션에서 만든 웹앱을 선택합니다.

1. **배포** 블레이드에서 **배포 옵션** > **원본 선택** > **로컬 Git 리포지토리** 를 선택합니다.

   ![설정 블레이드: 배포 소스 블레이드: 소스 블레이드 선택](azure-continuous-deployment/_static/deployment-options.png)

1. **확인** 을 선택합니다.

1. 웹앱 또는 다른 App Service 앱을 게시하기 위한 배포 자격 증명이 이전에 설정되지 않은 경우 지금 설정합니다.

   * **설정** > **배포 자격 증명** 을 선택합니다. **배포 자격 증명 설정** 블레이드가 표시됩니다.
   * 사용자 이름 및 암호를 만듭니다. 나중에 Git을 설정할 때 사용할 암호를 저장합니다.
   * **저장** 을 선택합니다.

1. **웹앱** 블레이드에서 **설정** > **속성** 을 선택합니다. 배포할 원격 Git 리포지토리 URL이 **GIT URL** 아래에 표시됩니다.

1. 나중에 자습서에서 사용할 **GIT URL** 값을 복사합니다.

   ![Azure Portal: 애플리케이션 속성 블레이드](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Azure App Service에 웹앱 게시

이 섹션에서는 Visual Studio를 사용하여 로컬 Git 리포지토리를 만들고 해당 리포지토리로부터 Azure에 푸시하여 웹앱을 배포합니다. 관련 단계에는 다음이 포함됩니다.

* Azure에 로컬 리포지토리를 배포할 수 있도록 GIT URL 값을 사용하여 원격 리포지토리 설정을 추가합니다.
* 프로젝트에 변경 내용을 커밋합니다.
* 로컬 리포지토리에서 Azure의 원격 리포지토리로 프로젝트 변경 내용을 푸시합니다.

1. **솔루션 탐색기** 에서 **솔루션 'SampleWebAppDemo'** 를 마우스 오른쪽 단추로 클릭하고 **커밋** 을 선택합니다. **팀 탐색기** 가 표시됩니다.

   ![팀 탐색기 연결 탭](azure-continuous-deployment/_static/10-team-explorer.png)

1. **팀 탐색기** 에서 **홈**(홈 아이콘) > **설정** > **리포지토리 설정** 을 선택합니다.

1. **리포지토리 설정** 의 **원격** 섹션에서 **추가** 를 선택합니다. **원격 추가** 대화 상자가 표시됩니다.

1. 원격 리포지토리의 **이름** 을 **Azure SampleApp** 으로 설정합니다.

1. **페치** 의 값을 이 자습서의 앞부분에 나오는 Azure에서 복사한 **Git URL** 로 설정합니다. 이 URL은 **.git** 로 끝납니다.

   ![원격 대화 상자 편집](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > 대안으로 **명령 창** 을 열고, 프로젝트 디렉터리로 변경하고, 명령을 입력하여 **명령 창** 에서 원격 리포지토리를 지정합니다. 예:
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. **홈**(홈 아이콘) > **설정** > **전역 설정** 을 선택합니다. 이름 및 전자 메일 주소가 설정되었는지 확인합니다. 필요한 경우 **업데이트** 를 선택합니다.

1. **홈** > **변경 내용** 을 선택하여 **변경 내용** 보기로 돌아갑니다.

1. **Initial Push #1** 과 같은 커밋 메시지를 입력하고 **커밋** 을 선택합니다. 이렇게 하면 로컬에서 ‘커밋’이 생성됩니다.

   ![팀 탐색기 연결 탭](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > 대안으로 **명령 창** 을 열고, 프로젝트 디렉터리로 변경하고, git 명령을 입력하여 **명령 창** 에서 변경 내용을 커밋합니다. 예:
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. **홈** > **동기화** > **동작** > **명령 프롬프트 열기** 를 선택합니다. 명령 프롬프트가 프로젝트 디렉터리에서 열립니다.

1. 명령 창에서 다음 명령을 입력합니다.

   `git push -u Azure-SampleApp master`

1. Azure에서 이전에 만든 Azure **배포 자격 증명** 암호를 입력합니다.

   이 명령은 로컬 프로젝트 파일을 Azure에 푸시하는 프로세스를 시작합니다. 위 명령의 출력은 성공적으로 배포했다는 메시지와 함께 종료됩니다.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > 프로젝트의 협업이 필요한 경우 Azure로 푸시하기 전에 [GitHub](https://github.com)로 푸시하는 것이 좋습니다.
 
### <a name="verify-the-active-deployment"></a>활성 배포 확인

로컬 환경에서 웹앱을 Azure로 성공적으로 전송했는지 확인합니다.

[Azure Portal](https://portal.azure.com)에서 웹앱을 선택합니다. **배포** > **배포 옵션** 을 선택합니다.

![Azure Portal: 설정 블레이드: 성공적인 배포를 보여주는 배포 블레이드](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Azure에서 앱 실행

이제 웹앱이 Azure에 배포되었으므로 앱을 실행합니다.

이는 두 가지 방법으로 수행할 수 있습니다.

* Azure Portal에서 웹앱에 대한 웹앱 블레이드를 찾습니다. **찾아보기** 를 선택하여 기본 브라우저에서 앱을 봅니다.
* 브라우저를 열고 웹앱의 URL을 입력합니다. 예: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>웹앱 업데이트 및 다시 게시

로컬 코드를 변경한 후 다시 게시합니다.

1. Visual Studio의 **솔루션 탐색기** 에서 *Startup.cs* 파일을 엽니다.

1. `Configure` 메서드에서 `Response.WriteAsync` 메서드를 수정하여 다음과 같이 표시되도록 합니다.

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. 변경 내용을 *Startup.cs* 에 저장합니다.

1. **솔루션 탐색기** 에서 **솔루션 'SampleWebAppDemo'** 를 마우스 오른쪽 단추로 클릭하고 **커밋** 을 선택합니다. **팀 탐색기** 가 표시됩니다.

1. `Update #2`와 같은 커밋 메시지를 입력합니다.

1. **커밋** 단추를 눌러 프로젝트 변경 내용을 커밋합니다.

1. **홈** > **동기화** > **작업** > **푸시** 를 선택합니다.

> [!NOTE]
> 대안으로 **명령 창** 을 열고, 프로젝트 디렉터리를 변경하고, git 명령을 입력하여 **명령 창** 의 변경 내용을 푸시합니다. 예:
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Azure에서 업데이트된 웹앱 보기

Azure Portal의 웹앱 블레이드에서 **찾아보기** 를 선택하거나 브라우저를 열고 웹앱의 URL을 입력하여 업데이트된 웹앱을 봅니다. 예: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>추가 자료

* [Azure Pipelines를 사용하여 첫 번째 파이프라인 만들기](/azure/devops/pipelines/get-started-yaml)
* [프로젝트 Kudu](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
