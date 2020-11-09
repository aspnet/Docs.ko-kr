---
title: Visual Studio Code를 사용하여 Azure에 ASP.NET Core 앱 게시
author: rick-anderson
description: Visual Studio Code를 사용하여 Azure App Service에 ASP.NET Core 앱을 게시하는 방법 알아보기
ms.author: riserrad
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: a5a863682655517e27f6540af76342f95d4ecae0
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061264"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="45df0-103">Visual Studio Code를 사용하여 Azure에 ASP.NET Core 앱 게시</span><span class="sxs-lookup"><span data-stu-id="45df0-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="45df0-104">작성자: [Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="45df0-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="45df0-105">App Service 배포 문제를 해결하려면 <xref:test/troubleshoot-azure-iis>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="45df0-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="45df0-106">소개</span><span class="sxs-lookup"><span data-stu-id="45df0-106">Intro</span></span>

<span data-ttu-id="45df0-107">이 자습서에서는 ASP.Net Core MVC 애플리케이션을 만들고 Visual Studio Code 내에 배포하는 방법을 배웁니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="45df0-108">설치</span><span class="sxs-lookup"><span data-stu-id="45df0-108">Set up</span></span>

- <span data-ttu-id="45df0-109">계정이 없는 경우 [체험 Azure 계정](https://azure.microsoft.com/free/dotnet/)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="45df0-110">[.NET Core SDK](https://dotnet.microsoft.com/download) 설치</span><span class="sxs-lookup"><span data-stu-id="45df0-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="45df0-111">[Visual Studio Code](https://code.visualstudio.com/Download) 설치</span><span class="sxs-lookup"><span data-stu-id="45df0-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="45df0-112">Visual Studio Code에 [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 설치</span><span class="sxs-lookup"><span data-stu-id="45df0-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="45df0-113">계속하기 전에 [Azure App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)을 Visual Studio Code에 설치 및 구성</span><span class="sxs-lookup"><span data-stu-id="45df0-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="45df0-114">ASP.Net Core MVC 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="45df0-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="45df0-115">터미널을 사용하여 프로젝트를 만들 폴더로 이동하고 다음 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="45df0-116">폴더 구조는 다음과 유사해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-116">You'll have a folder structure similar to the following:</span></span>

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

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="45df0-117">Visual Studio Code로 열기</span><span class="sxs-lookup"><span data-stu-id="45df0-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="45df0-118">프로젝트를 만든 후 아래 옵션 중 하나를 사용하여 Visual Studio Code로 프로젝트를 열 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="45df0-119">명령줄을 통해</span><span class="sxs-lookup"><span data-stu-id="45df0-119">Through the command line</span></span>

<span data-ttu-id="45df0-120">프로젝트를 만든 폴더 내에서 다음 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="45df0-121">아래 명령이 작동하지 않는 경우 [이 링크](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform)를 참조하여 설치가 제대로 구성되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="45df0-122">Visual Studio Code 인터페이스를 통해</span><span class="sxs-lookup"><span data-stu-id="45df0-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="45df0-123">Visual Studio Code 열기</span><span class="sxs-lookup"><span data-stu-id="45df0-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="45df0-124">메뉴에서 `File > Open Folder` 선택</span><span class="sxs-lookup"><span data-stu-id="45df0-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="45df0-125">MVC 프로젝트를 만든 폴더의 루트 선택</span><span class="sxs-lookup"><span data-stu-id="45df0-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="45df0-126">프로젝트 폴더를 열면 빌드 및 디버그에 필요한 자산이 누락되었다는 메시지를 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="45df0-127">추가하려면 도움말을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-127">Accept the help to add them.</span></span>

![프로젝트가 로드된 Visual Studio Code 인터페이스](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="45df0-129">프로젝트 구조 아래에 `.vscode` 폴더가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="45df0-130">다음 파일이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="45df0-131">.NET Core 웹앱을 빌드하고 디버깅하는 데 도움이 되는 유틸리티 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="45df0-132">앱 실행</span><span class="sxs-lookup"><span data-stu-id="45df0-132">Run the app</span></span>

<span data-ttu-id="45df0-133">Azure에 앱을 배포하기 전에 로컬 머신에서 제대로 실행되고 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="45df0-134">F5를 눌러 프로젝트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-134">Press F5 to run the project</span></span>

<span data-ttu-id="45df0-135">웹앱은 기본 브라우저의 새 탭에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="45df0-136">시작하자마자 개인 정보 경고가 나타날 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="45df0-137">이는 앱이 HTTP 및 HTTPS를 사용하여 시작되며, 기본적으로 HTTPS 엔드포인트로 이동하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![앱을 로컬로 디버깅하는 동안 개인 정보 경고](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="45df0-139">디버깅 세션을 유지하려면 `Advanced`를 클릭한 다음, `Continue to localhost (unsafe)`를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="45df0-140">로컬로 배포 패키지 생성</span><span class="sxs-lookup"><span data-stu-id="45df0-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="45df0-141">Visual Studio Code 터미널 열기</span><span class="sxs-lookup"><span data-stu-id="45df0-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="45df0-142">`publish`라는 하위 폴더에 `Release` 패키지를 생성하려면 다음 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="45df0-143">프로젝트 구조 아래에 새 `publish` 폴더가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-143">A new `publish` folder will be created under the project structure</span></span>

![게시 폴더 구조](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="45df0-145">Azure App Service에 게시</span><span class="sxs-lookup"><span data-stu-id="45df0-145">Publish to Azure App Service</span></span>

<span data-ttu-id="45df0-146">Visual Studio Code용 Azure App Service 확장을 활용하여 아래 단계에 따라 웹 사이트를 Azure App Service에 직접 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="45df0-147">새 웹앱을 만드는 경우</span><span class="sxs-lookup"><span data-stu-id="45df0-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="45df0-148">`publish` 폴더를 마우스 오른쪽 단추로 클릭하고 `Deploy to Web App...`을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="45df0-149">웹앱을 만들 구독을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="45df0-150">`Create New Web App` 선택</span><span class="sxs-lookup"><span data-stu-id="45df0-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="45df0-151">웹앱의 이름 입력</span><span class="sxs-lookup"><span data-stu-id="45df0-151">Enter a name for the Web App</span></span>

<span data-ttu-id="45df0-152">이 확장은 새 웹앱을 만들고 자동으로 패키지 배포를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="45df0-153">배포가 완료되면 `Browse Website`를 클릭하여 배포의 유효성 검사를 합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![배포 성공 메시지](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="45df0-155">`Browse Website`를 클릭하면 기본 브라우저를 사용하여 탐색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![새 웹앱이 성공적으로 배포되었습니다.](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="45df0-157">기존 웹앱에 배포하는 경우</span><span class="sxs-lookup"><span data-stu-id="45df0-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="45df0-158">`publish` 폴더를 마우스 오른쪽 단추로 클릭하고 `Deploy to Web App...`을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="45df0-159">기존 웹앱이 있는 구독 선택</span><span class="sxs-lookup"><span data-stu-id="45df0-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="45df0-160">목록에서 웹앱 선택</span><span class="sxs-lookup"><span data-stu-id="45df0-160">Select the Web App from the list</span></span>
- <span data-ttu-id="45df0-161">Visual Studio Code에서 기존 콘텐츠를 덮어쓸지 여부를 묻는 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="45df0-162">확인하려면 `Deploy` 클릭</span><span class="sxs-lookup"><span data-stu-id="45df0-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="45df0-163">이 확장은 업데이트된 콘텐츠를 웹앱에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="45df0-164">작업이 완료되면 `Browse Website`를 클릭하여 배포의 유효성 검사를 합니다.</span><span class="sxs-lookup"><span data-stu-id="45df0-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![기존 웹앱이 성공적으로 배포되었습니다.](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="45df0-166">다음 단계</span><span class="sxs-lookup"><span data-stu-id="45df0-166">Next steps</span></span>

- [<span data-ttu-id="45df0-167">첫 번째 Azure DevOps 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="45df0-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="45df0-168">추가 자료</span><span class="sxs-lookup"><span data-stu-id="45df0-168">Additional resources</span></span>

- [<span data-ttu-id="45df0-169">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="45df0-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="45df0-170">Azure 리소스 그룹</span><span class="sxs-lookup"><span data-stu-id="45df0-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
