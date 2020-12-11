---
title: App Service에 앱 배포 - ASP.NET Core 및 Azure를 사용하는 DevOps
author: CamSoper
description: ASP.NET Core 및 Azure를 사용하는 DevOps의 첫 번째 단계로, Azure App Service에 ASP.NET Core 앱을 배포합니다.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-azurecli
ms.date: 10/24/2018
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
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: f1c7acba0b7fb7dc07da576b188e580328ff4b89
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901160"
---
# <a name="deploy-an-app-to-app-service"></a>App Service에 앱 배포

[Azure App Service](/azure/app-service/)는 Azure의 웹 호스팅 플랫폼입니다. 수동으로 또는 자동 프로세스를 통해 Azure App Service에 웹앱을 배포할 수 있습니다. 이 가이드 섹션에서는 수동으로 또는 명령줄을 사용하여 스크립트를 통해 트리거하거나 Visual Studio를 사용하여 수동으로 트리거할 수 있는 배포 방법을 설명합니다.

이 섹션에서는 다음 작업을 수행합니다.

* 샘플 앱을 다운로드하고 빌드합니다.
* Azure Cloud Shell을 사용하여 Azure App Service 웹앱을 만듭니다.
* Git을 사용하여 Azure에 샘플 앱을 배포합니다.
* Visual Studio를 사용하여 앱의 변경 내용을 배포합니다.
* 웹앱에 스테이징 슬롯을 추가합니다.
* 스테이징 슬롯에 업데이트를 배포합니다.
* 스테이징 및 프로덕션 슬롯을 교환합니다.

## <a name="download-and-test-the-app"></a>앱 다운로드 및 테스트

이 가이드에서 사용하는 앱은 미리 빌드된 ASP.NET Core 앱인 [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/)입니다. `Microsoft.SyndicationFeed.ReaderWriter` API를 사용하여 RSS/Atom 피드를 검색하고 목록에 뉴스 항목을 표시하는 Razor Pages 앱입니다.

자유롭게 코드를 검토할 수 있지만, 이 앱에 특별한 기능이 있는 것은 아닙니다. 설명을 위해 빌드된 간단한 ASP.NET Core 앱일 뿐입니다.

명령 셸에서 코드를 다운로드하고 프로젝트를 빌드한 후, 다음과 같이 실행합니다.

> *참고: Linux/macOS 사용자는 경로를 적절하게 변경해야 합니다. 예를 들어 백슬래시(`\`) 대신 슬래시(`/`)를 사용해야 합니다.*

1. 로컬 컴퓨터의 폴더에 코드를 복제합니다.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. 작업 폴더를 생성된 *simple-feed-reader* 폴더로 변경합니다.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. 패키지를 복원하고 솔루션을 빌드합니다.

    ```dotnetcli
    dotnet build
    ```

4. 앱을 실행합니다.

    ```dotnetcli
    dotnet run
    ```

    ![dotnet run 명령 성공](./media/deploying-to-app-service/dotnet-run.png)

5. 브라우저를 열고 `http://localhost:5000` 로 이동합니다. 앱에서 배포 피드 URL을 입력하거나 붙여넣고 뉴스 항목 목록을 볼 수 있습니다.

     ![RSS 피드 콘텐츠를 표시하는 앱](./media/deploying-to-app-service/app-in-browser.png)

6. 앱이 제대로 작동하는 것을 확인했으면 명령 셸에서 **Ctrl**+**C** 를 눌러 종료합니다.

## <a name="create-the-azure-app-service-web-app"></a>Azure App Service 웹앱 만들기

앱을 배포하려면 App Service [웹앱](/azure/app-service/app-service-web-overview)을 만들어야 합니다. 웹앱을 만든 후에 Git을 사용하여 로컬 컴퓨터에서 배포합니다.

1. [Azure Cloud Shell](https://shell.azure.com/bash)에 로그인합니다. 참고: 처음으로 로그인하면 Cloud Shell에서 구성 파일에 사용할 스토리지 계정을 만들라는 메시지가 표시됩니다. 기본값을 그대로 적용하거나 고유 이름을 지정합니다.

2. 다음 단계에서 Cloud Shell을 사용합니다.

    a. 웹앱의 이름을 저장할 변수를 선언합니다. 기본 URL에 사용하려면 이름이 고유해야 합니다. `$RANDOM` Bash 함수를 사용하여 이름을 구성하면 고유성이 보장되고 `webappname99999` 형식이 됩니다.

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. 리소스 그룹을 만듭니다. 리소스 그룹은 그룹으로 관리할 Azure 리소스를 집계하는 수단을 제공합니다.

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    `az` 명령은 [Azure CLI](/cli/azure/)를 호출합니다. CLI는 로컬에서도 실행할 수 있지만, Cloud Shell에서 사용하면 시간과 구성이 절약됩니다.

    c. S1 계층에서 App Service 계획을 만듭니다. App Service 계획은 동일한 가격 책정 계층을 공유하는 웹앱 그룹화입니다. S1 계층은 무료가 아니지만 스테이징 슬롯 기능에 필요합니다.

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. 동일한 리소스 그룹의 App Service 계획을 사용하여 웹앱 리소스를 만듭니다.

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. 배포 자격 증명을 설정합니다. 해당 배포 자격 증명은 구독의 모든 웹앱에 적용됩니다. 사용자 이름에 특수 문자를 사용하면 안 됩니다.

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. 로컬 Git에서의 배포를 허용하고 ‘Git 배포 URL’을 표시하도록 웹앱을 구성합니다.  **이 URL은 나중에 참조하는 데 사용합니다**.

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. ‘웹앱 URL’을 표시합니다.  빈 웹앱을 확인하려면 이 URL로 이동합니다. **이 URL은 나중에 참조하는 데 사용합니다**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. 로컬 컴퓨터에서 명령 셸을 사용하여 웹앱의 프로젝트 폴더(예: `.\simple-feed-reader\SimpleFeedReader`)로 이동합니다. 다음 명령을 실행하여 배포 URL로 푸시하도록 Git을 설정합니다.

    a. 로컬 리포지토리에 대한 원격 URL을 추가합니다.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. 로컬 기본 분기(마스터)를 *azure-prod* 원격의 기본 분기(마스터)로 푸시합니다.

    ```console
    git push azure-prod master
    ```

    앞에서 만든 배포 자격 증명을 입력하라는 메시지가 표시됩니다. 명령 셸의 출력을 확인합니다. Azure에서 ASP.NET Core 앱을 원격으로 빌드합니다.

4. 브라우저에서 *웹앱 URL* 로 이동한 다음, 앱이 빌드되어 배포되었는지 확인합니다. `git commit`을 사용하여 추가 변경 내용을 로컬 Git 리포지토리로 커밋할 수 있습니다. 해당 변경 내용은 위의 `git push` 명령을 사용하여 Azure로 푸시됩니다.

## <a name="deployment-with-visual-studio"></a>Visual Studio를 사용한 배포

> *참고: 이 섹션의 내용은 Windows에만 적용됩니다. Linux 및 macOS 사용자는 아래 2단계에 설명된 대로 변경해야 합니다. 파일을 저장한 다음, `git commit`을 사용하여 변경 내용을 로컬 리포지토리로 커밋합니다. 마지막으로, 첫 번째 섹션과 같이 `git push`를 사용하여 변경 내용을 푸시합니다.*

앱이 명령 셸에서 이미 배포되었습니다. Visual Studio의 통합 도구를 사용하여 앱의 업데이트를 배포해 봅시다. Visual Studio는 내부적으로 명령줄 도구와 동일한 작업을 수행하지만, Visual Studio의 친숙한 UI 내에서 수행합니다.

1. Visual Studio에서 *SimpleFeedReader.sln* 을 엽니다.
2. 솔루션 탐색기에서 *Pages\Index.cshtml* 을 엽니다. `<h2>Simple Feed Reader</h2>`을 `<h2>Simple Feed Reader - V2</h2>`으로 변경합니다.
3. **Ctrl**+**Shift**+**B** 를 눌러 앱을 빌드합니다.
4. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 클릭합니다.

    ![마우스 오른쪽 단추 클릭, 게시를 보여 주는 스크린샷](./media/deploying-to-app-service/publish.png)
5. Visual Studio에서 새 App Service 리소스를 만들 수도 있지만, 이 업데이트는 기존 배포에 게시됩니다. **게시 대상 선택** 대화 상자의 왼쪽 목록에서 **App Service** 를 선택한 다음, **기존 항목 선택** 을 선택합니다. **게시** 를 클릭합니다.
6. **App Service** 대화 상자에서 Azure 구독을 만드는 데 사용된 Microsoft 또는 조직 계정이 오른쪽 위에 표시되는지 확인합니다. 표시되지 않는 경우, 드롭다운을 클릭하고 추가합니다.
7. 올바른 Azure **구독** 이 선택되었는지 확인합니다. **보기** 에서 **리소스 그룹** 을 선택합니다. **AzureTutorial** 리소스 그룹을 펼치고 기존 웹앱을 선택합니다. **확인** 을 클릭합니다.

    ![App Service 게시 대화 상자를 보여 주는 스크린샷](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio에서 앱을 빌드하고 Azure에 배포합니다. 웹앱 URL로 이동합니다. `<h2>` 요소 수정 내용이 라이브 상태인지 확인합니다.

![변경된 제목이 포함된 앱](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>배포 슬롯

배포 슬롯은 프로덕션 환경에서 실행되는 앱에 영향을 주지 않고 변경 내용 스테이징을 지원합니다. 품질 보증 팀이 스테이징된 앱 버전의 유효성을 검사한 후 프로덕션 슬롯과 스테이징 슬롯을 교환할 수 있습니다. 이런 방식으로 스테이징 중인 앱이 프로덕션으로 승격됩니다. 다음 단계에서는 스테이징 슬롯을 만들고 일부 변경 내용을 배포한 다음, 확인 후에 스테이징 슬롯을 프로덕션과 교환합니다.

1. 아직 로그인하지 않은 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에 로그인합니다.
2. 스테이징 슬롯을 만듭니다.

    a. 이름을 *staging* 으로 지정하여 배포 슬롯을 만듭니다.

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. 로컬 Git의 배포를 사용하고 **staging** 배포 URL을 가져오도록 스테이징 슬롯을 구성합니다. **이 URL은 나중에 참조하는 데 사용합니다**.

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. 스테이징 슬롯의 URL을 표시합니다. URL로 이동하여 빈 스테이징 슬롯을 확인합니다. **이 URL은 나중에 참조하는 데 사용합니다**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. 텍스트 편집기 또는 Visual Studio에서 `<h2>` 요소가 `<h2>Simple Feed Reader - V3</h2>`으로 표시되도록 *Pages/Index.cshtml* 을 다시 수정한 다음, 파일을 저장합니다.

4. Visual Studio *팀 탐색기* 탭의 **변경 내용** 페이지를 사용하거나 로컬 컴퓨터의 명령 셸을 통해 다음을 입력하여 파일을 로컬 Git 리포지토리로 커밋합니다.

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. 로컬 컴퓨터의 명령 셸을 사용하여 스테이징 배포 URL을 Git 원격으로 추가하고 커밋된 변경 내용을 푸시합니다.

    a. 스테이징용 원격 URL을 로컬 Git 리포지토리에 추가합니다.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. 로컬 기본 분기(마스터)를 *azure-staging* 원격의 기본 분기(마스터)로 푸시합니다.

    ```console
    git push azure-staging master
    ```

    Azure에서 앱을 빌드하고 배포하는 동안 기다립니다.

6. V3이 스테이징 슬롯에 배포되었는지 확인하려면 브라우저 창을 두 개 엽니다. 첫 번째 창에서 원래 웹앱 URL로 이동합니다. 두 번째 창에서 스테이징 웹앱 URL로 이동합니다. 프로덕션 URL은 앱 V2를 제공합니다. 스테이징 URL은 앱 V3을 제공합니다.

    ![브라우저 창을 비교하는 스크린샷](./media/deploying-to-app-service/ready-to-swap.png)

7. Cloud Shell에서 확인/준비된 스테이징 슬롯을 프로덕션으로 교환합니다.

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. 두 브라우저 창을 새로 고쳐 교환이 발생했는지 확인합니다.

    ![교환 후의 브라우저 창 비교](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>요약

이 섹션에서는 다음 작업을 완료했습니다.

* 샘플 앱을 다운로드하고 빌드했습니다.
* Azure Cloud Shell을 사용하여 Azure App Service 웹앱을 만들었습니다.
* Git을 사용하여 Azure에 샘플 앱을 배포했습니다.
* Visual Studio를 사용하여 앱의 변경 내용을 배포했습니다.
* 웹앱에 스테이징 슬롯을 추가했습니다.
* 스테이징 슬롯에 업데이트를 배포했습니다.
* 스테이징 및 프로덕션 슬롯을 교환했습니다.

다음 섹션에서는 Azure Pipelines를 사용하여 DevOps 파이프라인을 빌드하는 방법을 알아봅니다.

## <a name="additional-reading"></a>추가 참조 항목

* [Web Apps 개요](/azure/app-service/app-service-web-overview)
* [Azure App Service에서 .NET Core 및 SQL 데이터베이스 웹앱 빌드](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Azure App Service의 배포 자격 증명 구성](/azure/app-service/app-service-deployment-credentials)
* [Azure App Service에서 스테이징 환경 설정](/azure/app-service/web-sites-staged-publishing)
