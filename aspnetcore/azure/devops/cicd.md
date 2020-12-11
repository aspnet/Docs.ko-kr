---
title: 지속적인 통합 및 배포 - ASP.NET Core 및 Azure의 DevOps
author: CamSoper
description: ASP.NET Core 및 Azure를 사용하여 DevOps에서 지속적인 통합 및 배포
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/cicd
ms.openlocfilehash: 2ac7a130d223b21330d0a797c1d460fc0cf467d7
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901212"
---
# <a name="continuous-integration-and-deployment"></a>연속 통합 및 배포

이전 장에서는 Simple Feed Reader 앱에 대한 로컬 Git 리포지토리를 만들었습니다. 이 장에서는 해당 코드를 GitHub 리포지토리에 게시하고 Azure Pipelines를 사용하여 Azure DevOps Services 파이프라인을 생성합니다. 파이프라인은 앱의 지속적인 빌드 및 배포를 사용하도록 설정합니다. GitHub 리포지토리를 커밋하면 Azure 웹앱 스테이징 슬롯에 대한 빌드 및 배포가 트리거됩니다.

이 섹션에서는 다음 작업을 완료하게 됩니다.

* GitHub에 앱 코드 게시
* 로컬 Git 배포 연결 끊기
* Azure DevOps 조직 만들기
* Azure DevOps Services에서 팀 프로젝트 만들기
* 빌드 정의 만들기
* 릴리스 파이프라인 만들기
* GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포
* Azure Pipelines 파이프라인 검사

## <a name="publish-the-apps-code-to-github"></a>GitHub에 앱 코드 게시

1. 브라우저 창을 열고 `https://github.com`으로 이동합니다.
1. 머리글에서 **+** 드롭다운을 클릭하고 **새 리포지토리** 를 선택합니다.

    ![GitHub 새 리포지토리 옵션](media/cicd/github-new-repo.png)

1. **소유자** 드롭다운에서 계정을 선택하고 **리포지토리 이름** 텍스트 상자에 *simple-feed-reader* 를 입력합니다.
1. **리포지토리 만들기** 단추를 클릭합니다.
1. 로컬 컴퓨터의 명령 셸을 엽니다. *simple-feed-reader* Git 리포지토리가 저장된 디렉터리로 이동합니다.
1. 기존 *origin* 원격 항목의 이름을 *upstream* 으로 바꿉니다. 다음 명령을 실행합니다.

    ```console
    git remote rename origin upstream
    ```

1. GitHub에서 리포지토리의 복사본을 가리키는 새 *origin* 원격 항목을 추가합니다. 다음 명령을 실행합니다.

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. 로컬 Git 리포지토리를 새로 만든 GitHub 리포지토리에 게시합니다. 다음 명령을 실행합니다.

    ```console
    git push -u origin master
    ```

1. 브라우저 창을 열고 `https://github.com/<GitHub_username>/simple-feed-reader/`로 이동합니다. 코드가 GitHub 리포지토리에 나타나는지 확인합니다.

## <a name="disconnect-local-git-deployment"></a>로컬 Git 배포 연결 끊기

다음 단계를 수행하여 로컬 Git 배포를 제거합니다. Azure Pipelines(Azure DevOps 서비스)는 해당 기능을 대체하고 보강합니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 ‘스테이징’ *(mywebapp\<unique_number\>/staging)* 웹앱으로 이동합니다. 포털의 검색 상자에 *스테이징* 을 입력하여 웹앱을 신속하게 찾을 수 있습니다.

    ![스테이징 웹앱 검색 용어](media/cicd/portal-search-box.png)

1. **배포 센터** 를 클릭합니다. 새 패널이 나타납니다. **연결 끊기** 를 클릭하여 이전 장에 추가된 로컬 Git 소스 제어 구성을 제거합니다. **예** 단추를 클릭하여 제거 작업을 확인합니다.
1. *mywebapp<unique_number>* App Service로 이동합니다. 미리 알림으로 포털의 검색 상자를 사용하여 App Service를 신속하게 찾을 수 있습니다.
1. **배포 센터** 를 클릭합니다. 새 패널이 나타납니다. **연결 끊기** 를 클릭하여 이전 장에 추가된 로컬 Git 소스 제어 구성을 제거합니다. **예** 단추를 클릭하여 제거 작업을 확인합니다.

## <a name="create-an-azure-devops-organization"></a>Azure DevOps 조직 만들기

1. 브라우저를 열고 [Azure DevOps 조직 만들기 페이지](https://go.microsoft.com/fwlink/?LinkId=307137)로 이동합니다.
1. **기억하기 쉬운 이름 선택** 텍스트 상자에 고유한 이름을 입력하여 Azure DevOps 조직에 액세스하기 위한 URL을 구성합니다.
1. 코드가 GitHub 리포지토리에서 호스트되므로 **Git** 라디오 단추를 선택합니다.
1. **계속** 단추를 클릭합니다. 잠시 지연된 후 *MyFirstProject* 라는 계정 및 팀 프로젝트가 생성됩니다.

    ![Azure DevOps 조직 만들기 페이지](media/cicd/vsts-account-creation.png)

1. Azure DevOps 조직 및 프로젝트를 사용할 준비가 되었음을 나타내는 확인 메일을 엽니다. **프로젝트 시작** 단추를 클릭합니다.

    ![프로젝트 시작 단추](media/cicd/vsts-start-project.png)

1. 브라우저에서 *\<account_name\>.visualstudio.com* 이 열립니다. *MyFirstProject* 링크를 클릭하여 프로젝트의 DevOps 파이프라인 구성을 시작합니다.

## <a name="configure-the-azure-pipelines-pipeline"></a>Azure Pipelines 파이프라인 구성

세 가지 고유한 단계를 완료해야 합니다. 다음 세 섹션의 단계를 완료하면 운영 DevOps 파이프라인이 발생합니다.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Azure DevOps에 GitHub 리포지토리에 대한 액세스 권한 부여

1. **or build code from an external repository** Accordion을 확장합니다. **빌드 설정** 단추를 클릭합니다.

    ![빌드 설정 단추](media/cicd/vsts-setup-build.png)

1. **원본 선택** 섹션에서 **GitHub** 옵션을 선택합니다.

    ![원본 선택 - GitHub](media/cicd/vsts-select-source.png)

1. Azure DevOps가 GitHub 리포지토리에 액세스하려면 먼저 권한 부여가 필요합니다. **연결 이름** 텍스트 상자에 *<GitHub_username> GitHub 연결* 을 입력합니다. 예를 들어:

    ![GitHub 연결 이름](media/cicd/vsts-repo-authz.png)

1. GitHub 계정에서 2단계 인증을 사용하도록 설정한 경우 개인용 액세스 토큰이 필요합니다. 이 경우 **GitHub 개인용 액세스 토큰으로 권한 부여** 링크를 클릭합니다. 도움말을 보려면 [공식 GitHub 개인용 액세스 토큰 만들기 지침](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)을 참조하세요. *리포지토리* 사용 권한 범위만 필요합니다. 그렇지 않으면 **OAuth를 사용하여 권한 부여** 단추를 클릭합니다.
1. 메시지가 표시되면 GitHub 계정에 로그인합니다. 그런 다음, 권한 부여를 선택하여 Azure DevOps 조직에 대한 액세스 권한을 부여합니다. 성공하면 새 서비스 엔드포인트가 만들어집니다.
1. **리포지토리** 단추 옆에 있는 줄임표 단추를 클릭합니다. 목록에서 *<GitHub_username>/simple-feed-reader* 리포지토리를 선택합니다. **선택** 단추를 클릭합니다.
1. **수동 및 예약된 빌드의 기본 분기** 드롭다운에서 기본 분기(마스터)를 선택합니다. **계속** 단추를 클릭합니다. 템플릿 선택 페이지가 나타납니다.

### <a name="create-the-build-definition"></a>빌드 정의 만들기

1. 템플릿 선택 페이지의 검색 상자에 *ASP.NET Core* 를 입력합니다.

    ![템플릿 페이지의 ASP.NET Core 검색](media/cicd/vsts-template-selection.png)

1. 템플릿 검색 결과가 표시됩니다. **ASP.NET Core** 템플릿 위로 마우스를 이동하고 **적용** 단추를 클릭합니다.
1. 빌드 정의의 **작업** 탭이 나타납니다. **트리거** 탭을 클릭합니다.
1. **지속적인 통합 사용** 상자를 선택합니다. **분기 필터** 섹션에서 **형식** 드롭다운이 *포함* 으로 설정되어 있는지 확인합니다. **분기 사양** 드롭다운을 *마스터* 로 설정합니다.

    ![지속적인 통합 사용 설정](media/cicd/vsts-enable-ci.png)

    이러한 설정으로 인해 변경 내용이 GitHub 리포지토리의 기본 분기(마스터)로 푸시될 때 빌드가 트리거됩니다. 지속적인 통합은 [GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포](#commit-changes-to-github-and-automatically-deploy-to-azure) 섹션에서 테스트됩니다.

1. **저장 및 큐에 넣기** 단추를 클릭하고 **저장** 옵션을 선택합니다.

    ![저장 단추](media/cicd/vsts-save-build.png)

1. 다음과 같은 모달 대화 상자가 나타납니다.

    ![빌드 정의 저장 - 모달 대화 상자](media/cicd/vsts-save-modal.png)

    기본 폴더인 *\\* 를 사용하고 **저장** 단추를 클릭합니다.

### <a name="create-the-release-pipeline"></a>릴리스 파이프라인 만들기

1. 팀 프로젝트의 **릴리스** 탭을 클릭합니다. **새 파이프라인** 단추를 클릭합니다.

    ![릴리스 탭 - 새 정의 단추](media/cicd/vsts-new-release-definition.png)

    템플릿 선택 창이 나타납니다.

1. 템플릿 선택 페이지에서 검색 상자에 *App Service* 를 입력합니다.

    ![릴리스 파이프라인 템플릿 검색 상자](media/cicd/vsts-release-template-search.png)

1. 템플릿 검색 결과가 표시됩니다. **Azure App Service 배포 및 슬롯** 템플릿 위로 마우스를 가져간 후 **적용** 단추를 클릭합니다. 릴리스 파이프라인의 **파이프라인** 탭이 나타납니다.

    ![릴리스 파이프라인 파이프라인 탭](media/cicd/vsts-release-definition-pipeline.png)

1. **아티팩트** 섹션에서 **추가** 단추를 클릭합니다. **아티팩트 추가** 패널이 나타납니다.

    ![릴리스 파이프라인 - 아티팩트 추가 패널](media/cicd/vsts-release-add-artifact.png)

1. **원본 유형** 섹션에서 **빌드** 타일을 선택합니다. 이 형식을 사용하면 빌드 정의에 릴리스 파이프라인을 연결할 수 있습니다.
1. **프로젝트** 드롭다운에서 *MyFirstProject* 를 선택합니다.
1. **원본(빌드 정의)** 드롭다운에서 빌드 정의 이름 *MyFirstProject-ASP.NET Core-CI* 를 선택합니다.
1. **기본 버전** 드롭다운에서 *최신* 을 선택합니다. 이 옵션은 가장 최근의 빌드 정의 실행을 통해 생성된 아티팩트를 빌드합니다.
1. **원본 별칭** 텍스트 상자의 텍스트를 *Drop* 으로 바꿉니다.
1. **추가** 단추를 클릭합니다. **아티팩트** 섹션이 업데이트되어 변경 내용을 표시합니다.
1. 번개 모양 아이콘을 클릭하여 지속적인 배포를 사용하도록 설정합니다.

    ![릴리스 파이프라인 아티팩트 - 번개 모양 아이콘](media/cicd/vsts-artifacts-lightning-bolt.png)

    이 옵션을 사용하도록 설정하면 새 빌드를 사용할 수 있을 때마다 배포가 발생합니다.
1. **지속적인 배포 트리거** 패널이 오른쪽에 나타납니다. 설정/해제 단추를 클릭하여 기능을 사용하도록 설정합니다. **끌어오기 요청 트리거** 를 사용하도록 설정할 필요는 없습니다.
1. **빌드 분기 필터** 섹션에서 **추가** 드롭다운을 클릭합니다. **빌드 정의의 기본 분기** 옵션을 선택합니다. 이 필터를 설정하면 GitHub 리포지토리 기본 분기(마스터)의 빌드에 대해서만 릴리스가 트리거됩니다.
1. **저장** 단추를 클릭합니다. 결과 **저장** 모달 대화 상자에서 **확인** 단추를 클릭합니다.
1. **환경 1** 상자를 클릭합니다. **환경** 패널이 오른쪽에 나타납니다. **환경 이름** 텍스트 상자의 *환경 1* 텍스트를 *프로덕션* 으로 변경합니다.

   ![릴리스 파이프라인 - 환경 이름 텍스트 상자](media/cicd/vsts-environment-name-textbox.png)

1. **프로덕션** 상자에서 **1단계, 2개 작업** 링크를 클릭합니다.

    ![릴리스 파이프라인 - Production environment link.png](media/cicd/vsts-production-link.png)

    환경의 **작업** 탭이 나타납니다.
1. **슬롯에 Azure App Service 배포** 작업을 클릭합니다. 해당 설정이 오른쪽의 패널에 표시됩니다.
1. **Azure 구독** 드롭다운에서 App Service와 연결된 Azure 구독을 선택합니다. 이 구독을 선택했으면 **권한 부여** 단추를 클릭합니다.
1. **앱 유형** 드롭다운에서 *웹앱* 을 선택합니다.
1. **App Service 이름** 드롭다운에서 *mywebapp/<unique_number/>* 를 선택합니다.
1. **리소스 그룹** 드롭다운에서 *AzureTutorial* 을 선택합니다.
1. **슬롯** 드롭다운에서 *스테이징* 을 선택합니다.
1. **저장** 단추를 클릭합니다.
1. 기본 릴리스 파이프라인 이름 위로 마우스를 가져갑니다. 연필 아이콘을 클릭하여 편집합니다. *MyFirstProject-ASP.NET Core-CD* 를 이름으로 사용합니다.

    ![릴리스 파이프라인 이름](media/cicd/vsts-release-definition-name.png)

1. **저장** 단추를 클릭합니다.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포

1. Visual Studio에서 *SimpleFeedReader.sln* 을 엽니다.
1. 솔루션 탐색기에서 *Pages\Index.cshtml* 을 엽니다. `<h2>Simple Feed Reader - V3</h2>`을 `<h2>Simple Feed Reader - V4</h2>`으로 변경합니다.
1. **Ctrl**+**Shift**+**B** 를 눌러 앱을 빌드합니다.
1. GitHub 리포지토리에 파일을 커밋합니다. Visual Studio *팀 탐색기* 탭의 **변경** 페이지를 사용하거나 로컬 컴퓨터의 명령 셸을 사용하여 다음을 실행합니다.

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. 기본 분기(마스터)의 변경 내용을 GitHub 리포지토리의 *origin* 원격으로 푸시합니다. 다음 명령에서 자리 표시자 `{BRANCH}`를 기본 분기로 바꿉니다(`master` 사용).

    ```console
    git push origin {BRANCH}
    ```

    커밋이 GitHub 리포지토리의 기본 분기(마스터)에 표시됩니다.

    ![기본 분기(마스터)의 GitHub 커밋](media/cicd/github-commit.png)

    빌드 정의의 **트리거** 탭에서 연속 통합을 사용하도록 설정했으므로 빌드가 트리거됩니다.

    ![지속적인 통합 사용](media/cicd/enable-ci.png)

1. Azure DevOps Services의 **Azure Pipelines** > **빌드** 페이지의 **큐 대기** 탭으로 이동합니다. 큐 대기 빌드는 빌드를 트리거한 분기 및 커밋을 보여 줍니다.

    ![큐 대기 빌드](media/cicd/build-queued.png)

1. 빌드가 성공적으로 완료되면 Azure로 배포됩니다. 브라우저에서 앱으로 이동합니다. "V4" 텍스트가 제목에 표시됩니다.

    ![업데이트된 앱](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Azure Pipelines 파이프라인 검사

### <a name="build-definition"></a>빌드 정의

이름이 *MyFirstProject-ASP.NET Core-CI* 인 빌드 정의를 만들었습니다. 완료되면 빌드는 게시할 자산을 포함하는 *.zip* 파일을 생성합니다. 릴리스 파이프라인은 해당 자산을 Azure에 배포합니다.

빌드 정의의 **작업** 탭에는 사용되는 개별 단계가 나열됩니다. 5개의 빌드 작업이 있습니다.

![빌드 정의 작업](media/cicd/build-definition-tasks.png)

1. **복원** &mdash; `dotnet restore` 명령을 실행하여 앱의 NuGet 패키지를 복원합니다. 사용되는 기본 패키지 피드는 nuget.org입니다.
1. **빌드** &mdash; `dotnet build --configuration release` 명령을 실행하여 앱의 코드를 컴파일합니다. 이 `--configuration` 옵션은 프로덕션 환경에 배포하는 데 적합한 최적화된 버전의 코드를 생성하는 데 사용됩니다. 예를 들어, 디버그 구성이 필요한 경우 빌드 정의의 **변수** 탭에서 *BuildConfiguration* 변수를 수정합니다.
1. **테스트** &mdash; `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` 명령을 실행하여 앱의 단위 테스트를 실행합니다. 단위 테스트는 `**/*Tests/*.csproj` glob 패턴과 일치하는 모든 C# 프로젝트 내에서 실행됩니다. 테스트 결과는 `--results-directory` 옵션으로 지정된 위치에 있는 *.trx* 파일에 저장됩니다. 테스트가 실패하면 빌드가 실패하고 배포되지 않습니다.

    > [!NOTE]
    > 단위 테스트가 작동하는지 확인하려면 *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* 를 수정하여 테스트 중 하나를 의도적으로 중단합니다. 예를 들어 `Returns_News_Stories_Given_Valid_Uri` 메서드에서 `Assert.True(result.Count > 0);`을 `Assert.False(result.Count > 0);`으로 변경합니다. 변경 내용을 커밋하고 GitHub로 푸시합니다. 빌드가 트리거되고 실패합니다. 빌드 파이프라인 상태가 **실패** 로 변경됩니다. 변경을 되돌리고, 커밋한 후 다시 푸시합니다. 빌드가 성공합니다.

1. **게시** &mdash; `dotnet publish --configuration release --output <local_path_on_build_agent>` 명령을 실행하여 배포할 아티팩트가 포함된 *.zip* 파일을 생성합니다. `--output` 옵션은 *.zip* 파일의 게시 위치를 지정합니다. 이 위치는 `$(build.artifactstagingdirectory)`라는 [미리 정의된 변수](/azure/devops/pipelines/build/variables)를 전달하여 지정합니다. 해당 변수는 빌드 에이전트의 *c:\agent\_work\1\a* 와 같은 로컬 경로로 확장됩니다.
1. **아티팩트 게시** &mdash; **게시** 작업에 의해 생성된 *.zip* 파일을 게시합니다. 이 작업은 *.zip* 파일 위치를 미리 정의된 변수 `$(build.artifactstagingdirectory)`에 해당하는 매개 변수로 사용합니다. *.zip* 파일은 *drop* 이라는 폴더로 게시됩니다.

빌드 정의의 **요약** 링크를 클릭하여 정의가 포함된 빌드 기록을 볼 수 있습니다.

![빌드 정의 기록을 보여 주는 스크린샷](media/cicd/build-definition-summary.png)

결과 페이지에서 고유한 빌드 번호에 해당하는 링크를 클릭합니다.

![빌드 정의 요약 페이지를 보여 주는 스크린샷](media/cicd/build-definition-completed.png)

이 특정 빌드의 요약이 표시됩니다. **아티팩트** 탭을 클릭하고 빌드에서 생성된 *drop* 폴더가 나열됩니다.

![빌드 정의 아티팩트 - drop 폴더를 보여 주는 스크린샷](media/cicd/build-definition-artifacts.png)

**다운로드** 및 **탐색** 링크를 사용하여 게시된 아티팩트를 검사합니다.

### <a name="release-pipeline"></a>릴리스 파이프라인

이름이 *MyFirstProject-ASP.NET Core-CD* 인 릴리스 파이프라인을 만들었습니다.

![릴리스 파이프라인 개요를 보여 주는 스크린샷](media/cicd/release-definition-overview.png)

릴리스 파이프라인의 두 가지 주요 구성 요소는 **아티팩트** 및 **환경** 입니다. **아티팩트** 섹션의 상자를 클릭하면 다음 패널이 표시됩니다.

![릴리스 파이프라인 아티팩트를 보여 주는 스크린샷](media/cicd/release-definition-artifacts.png)

**원본(빌드 정의)** 값은 이 릴리스 파이프라인이 연결된 빌드 정의를 나타냅니다. 빌드 정의의 성공적인 실행으로 생성된 *.zip* 파일은 Azure에 배포하기 위해 *프로덕션* 환경에 제공됩니다. *프로덕션* 환경 상자의 *1단계, 2개 작업* 링크를 클릭하여 릴리스 파이프라인 작업을 봅니다.

![릴리스 파이프라인 작업을 보여 주는 스크린샷](media/cicd/release-definition-tasks.png)

릴리스 파이프라인은 다음 두 작업으로 구성됩니다. *슬롯에 Azure App Service 배포* 및 *Azure App Service 관리 - 슬롯 교환*. 첫 번째 작업을 클릭하면 다음과 같은 작업 구성이 표시됩니다.

![릴리스 파이프라인 배포 작업을 보여 주는 스크린샷](media/cicd/release-definition-task1.png)

Azure 구독, 서비스 유형, 웹앱 이름, 리소스 그룹 및 배포 슬롯은 배포 작업에 정의되어 있습니다. **패키지 또는 폴더** 텍스트 상자에는 *mywebapp\<unique_number\>* 웹앱의 ‘스테이징’ 슬롯에 추출 및 배포할 *.zip* 파일 경로가 포함됩니다.

슬롯 교환 작업을 클릭하면 다음과 같은 작업 구성이 표시됩니다.

![릴리스 파이프라인 슬롯 교환 작업을 보여 주는 스크린샷](media/cicd/release-definition-task2.png)

구독, 리소스 그룹, 서비스 유형, 웹앱 이름 및 배포 슬롯 정보가 제공됩니다. **프로덕션과 교환** 확인란이 선택됩니다. 따라서 *스테이징* 슬롯에 배포된 비트가 프로덕션 환경으로 교체됩니다.

## <a name="additional-reading"></a>추가 참조 항목

* [Azure Pipelines를 사용하여 첫 번째 파이프라인 만들기](/azure/devops/pipelines/get-started-yaml)
* [빌드 및 .NET Core 프로젝트](/azure/devops/pipelines/languages/dotnet-core)
* [Azure Pipelines를 사용하여 웹앱 배포](/azure/devops/pipelines/targets/webapp)
