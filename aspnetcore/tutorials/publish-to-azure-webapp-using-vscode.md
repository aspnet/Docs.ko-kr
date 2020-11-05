---
title: Visual Studio Code를 사용하여 Azure에 ASP.NET Core 앱 게시
author: rick-anderson
description: Visual Studio Code를 사용하여 Azure App Service에 ASP.NET Core 앱을 게시하는 방법 알아보기
ms.author: riserrad
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: a5a863682655517e27f6540af76342f95d4ecae0
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061264"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Visual Studio Code를 사용하여 Azure에 ASP.NET Core 앱 게시

작성자: [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

App Service 배포 문제를 해결하려면 <xref:test/troubleshoot-azure-iis>을 참조하세요.

## <a name="intro"></a>소개

이 자습서에서는 ASP.Net Core MVC 애플리케이션을 만들고 Visual Studio Code 내에 배포하는 방법을 배웁니다.

## <a name="set-up"></a>설치

- 계정이 없는 경우 [체험 Azure 계정](https://azure.microsoft.com/free/dotnet/)을 엽니다.
- [.NET Core SDK](https://dotnet.microsoft.com/download) 설치
- [Visual Studio Code](https://code.visualstudio.com/Download) 설치
  - Visual Studio Code에 [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 설치
  - 계속하기 전에 [Azure App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)을 Visual Studio Code에 설치 및 구성

## <a name="create-an-aspnet-core-mvc-project"></a>ASP.Net Core MVC 프로젝트 만들기

터미널을 사용하여 프로젝트를 만들 폴더로 이동하고 다음 명령을 사용합니다.

```dotnetcli
dotnet new mvc
```

폴더 구조는 다음과 유사해야 합니다.

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a>Visual Studio Code로 열기

프로젝트를 만든 후 아래 옵션 중 하나를 사용하여 Visual Studio Code로 프로젝트를 열 수 있습니다.

### <a name="through-the-command-line"></a>명령줄을 통해

프로젝트를 만든 폴더 내에서 다음 명령을 사용합니다.

```cmd
> code .
```

아래 명령이 작동하지 않는 경우 [이 링크](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform)를 참조하여 설치가 제대로 구성되었는지 확인합니다.

### <a name="through-visual-studio-code-interface"></a>Visual Studio Code 인터페이스를 통해

- Visual Studio Code 열기
- 메뉴에서 `File > Open Folder` 선택
- MVC 프로젝트를 만든 폴더의 루트 선택

프로젝트 폴더를 열면 빌드 및 디버그에 필요한 자산이 누락되었다는 메시지를 받게 됩니다. 추가하려면 도움말을 수락합니다.

![프로젝트가 로드된 Visual Studio Code 인터페이스](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

프로젝트 구조 아래에 `.vscode` 폴더가 생성됩니다. 다음 파일이 포함됩니다.

```cmd
launch.json
tasks.json
```

.NET Core 웹앱을 빌드하고 디버깅하는 데 도움이 되는 유틸리티 파일입니다.

## <a name="run-the-app"></a>앱 실행

Azure에 앱을 배포하기 전에 로컬 머신에서 제대로 실행되고 있는지 확인합니다.

- F5를 눌러 프로젝트를 실행합니다.

웹앱은 기본 브라우저의 새 탭에서 실행됩니다. 시작하자마자 개인 정보 경고가 나타날 수 있습니다. 이는 앱이 HTTP 및 HTTPS를 사용하여 시작되며, 기본적으로 HTTPS 엔드포인트로 이동하기 때문입니다.

![앱을 로컬로 디버깅하는 동안 개인 정보 경고](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

디버깅 세션을 유지하려면 `Advanced`를 클릭한 다음, `Continue to localhost (unsafe)`를 클릭합니다.

## <a name="generate-the-deployment-package-locally"></a>로컬로 배포 패키지 생성

- Visual Studio Code 터미널 열기
- `publish`라는 하위 폴더에 `Release` 패키지를 생성하려면 다음 명령을 사용합니다.
  - `dotnet publish -c Release -o ./publish`
- 프로젝트 구조 아래에 새 `publish` 폴더가 생성됩니다.

![게시 폴더 구조](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Azure App Service에 게시

Visual Studio Code용 Azure App Service 확장을 활용하여 아래 단계에 따라 웹 사이트를 Azure App Service에 직접 게시합니다.

### <a name="if-youre-creating-a-new-web-app"></a>새 웹앱을 만드는 경우

- `publish` 폴더를 마우스 오른쪽 단추로 클릭하고 `Deploy to Web App...`을 선택합니다.
- 웹앱을 만들 구독을 선택합니다.
- `Create New Web App` 선택
- 웹앱의 이름 입력

이 확장은 새 웹앱을 만들고 자동으로 패키지 배포를 시작합니다. 배포가 완료되면 `Browse Website`를 클릭하여 배포의 유효성 검사를 합니다.

![배포 성공 메시지](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

`Browse Website`를 클릭하면 기본 브라우저를 사용하여 탐색할 수 있습니다.

![새 웹앱이 성공적으로 배포되었습니다.](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>기존 웹앱에 배포하는 경우

- `publish` 폴더를 마우스 오른쪽 단추로 클릭하고 `Deploy to Web App...`을 선택합니다.
- 기존 웹앱이 있는 구독 선택
- 목록에서 웹앱 선택
- Visual Studio Code에서 기존 콘텐츠를 덮어쓸지 여부를 묻는 메시지가 표시됩니다. 확인하려면 `Deploy` 클릭

이 확장은 업데이트된 콘텐츠를 웹앱에 배포합니다. 작업이 완료되면 `Browse Website`를 클릭하여 배포의 유효성 검사를 합니다.

![기존 웹앱이 성공적으로 배포되었습니다.](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>다음 단계

- [첫 번째 Azure DevOps 파이프라인 만들기](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>추가 자료

- [Azure App Service](/azure/app-service/app-service-web-overview)
- [Azure 리소스 그룹](/azure/azure-resource-manager/resource-group-overview#resource-groups)
