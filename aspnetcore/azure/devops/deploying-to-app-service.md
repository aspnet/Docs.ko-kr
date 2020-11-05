---
title: App Service에 앱 배포 - ASP.NET Core 및 Azure를 사용하는 DevOps
author: CamSoper
description: ASP.NET Core 및 Azure를 사용하는 DevOps의 첫 번째 단계로, Azure App Service에 ASP.NET Core 앱을 배포합니다.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-azurecli
ms.date: 10/24/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: 52c4905ecb3a76f1dd10629f834b2b541b698774
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052359"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="c3b18-103">App Service에 앱 배포</span><span class="sxs-lookup"><span data-stu-id="c3b18-103">Deploy an app to App Service</span></span>

<span data-ttu-id="c3b18-104">[Azure App Service](/azure/app-service/)는 Azure의 웹 호스팅 플랫폼입니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="c3b18-105">수동으로 또는 자동 프로세스를 통해 Azure App Service에 웹앱을 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="c3b18-106">이 가이드 섹션에서는 수동으로 또는 명령줄을 사용하여 스크립트를 통해 트리거하거나 Visual Studio를 사용하여 수동으로 트리거할 수 있는 배포 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="c3b18-107">이 섹션에서는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="c3b18-108">샘플 앱을 다운로드하고 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-108">Download and build the sample app.</span></span>
* <span data-ttu-id="c3b18-109">Azure Cloud Shell을 사용하여 Azure App Service 웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="c3b18-110">Git을 사용하여 Azure에 샘플 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="c3b18-111">Visual Studio를 사용하여 앱의 변경 내용을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="c3b18-112">웹앱에 스테이징 슬롯을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="c3b18-113">스테이징 슬롯에 업데이트를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="c3b18-114">스테이징 및 프로덕션 슬롯을 교환합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="c3b18-115">앱 다운로드 및 테스트</span><span class="sxs-lookup"><span data-stu-id="c3b18-115">Download and test the app</span></span>

<span data-ttu-id="c3b18-116">이 가이드에서 사용하는 앱은 미리 빌드된 ASP.NET Core 앱인 [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/)입니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="c3b18-117">`Microsoft.SyndicationFeed.ReaderWriter` API를 사용하여 RSS/Atom 피드를 검색하고 목록에 뉴스 항목을 표시하는 :::no-loc(Razor)::: Pages 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-117">It's a :::no-loc(Razor)::: Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="c3b18-118">자유롭게 코드를 검토할 수 있지만, 이 앱에 특별한 기능이 있는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="c3b18-119">설명을 위해 빌드된 간단한 ASP.NET Core 앱일 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="c3b18-120">명령 셸에서 코드를 다운로드하고 프로젝트를 빌드한 후, 다음과 같이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="c3b18-121">*참고: Linux/macOS 사용자는 경로를 적절하게 변경해야 합니다. 예를 들어 백슬래시(`\`) 대신 슬래시(`/`)를 사용해야 합니다.*</span><span class="sxs-lookup"><span data-stu-id="c3b18-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="c3b18-122">로컬 컴퓨터의 폴더에 코드를 복제합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="c3b18-123">작업 폴더를 생성된 *simple-feed-reader* 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="c3b18-124">패키지를 복원하고 솔루션을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="c3b18-125">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![dotnet run 명령 성공](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="c3b18-127">브라우저를 열고 `http://localhost:5000` 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="c3b18-128">앱에서 배포 피드 URL을 입력하거나 붙여넣고 뉴스 항목 목록을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![RSS 피드 콘텐츠를 표시하는 앱](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="c3b18-130">앱이 제대로 작동하는 것을 확인했으면 명령 셸에서 **Ctrl**+**C** 를 눌러 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="c3b18-131">Azure App Service 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="c3b18-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="c3b18-132">앱을 배포하려면 App Service [웹앱](/azure/app-service/app-service-web-overview)을 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="c3b18-133">웹앱을 만든 후에 Git을 사용하여 로컬 컴퓨터에서 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="c3b18-134">[Azure Cloud Shell](https://shell.azure.com/bash)에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="c3b18-135">참고: 처음으로 로그인하면 Cloud Shell에서 구성 파일에 사용할 스토리지 계정을 만들라는 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="c3b18-136">기본값을 그대로 적용하거나 고유 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="c3b18-137">다음 단계에서 Cloud Shell을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="c3b18-138">a.</span><span class="sxs-lookup"><span data-stu-id="c3b18-138">a.</span></span> <span data-ttu-id="c3b18-139">웹앱의 이름을 저장할 변수를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="c3b18-140">기본 URL에 사용하려면 이름이 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="c3b18-141">`$RANDOM` Bash 함수를 사용하여 이름을 구성하면 고유성이 보장되고 `webappname99999` 형식이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="c3b18-142">b.</span><span class="sxs-lookup"><span data-stu-id="c3b18-142">b.</span></span> <span data-ttu-id="c3b18-143">리소스 그룹을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-143">Create a resource group.</span></span> <span data-ttu-id="c3b18-144">리소스 그룹은 그룹으로 관리할 Azure 리소스를 집계하는 수단을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="c3b18-145">`az` 명령은 [Azure CLI](/cli/azure/)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="c3b18-146">CLI는 로컬에서도 실행할 수 있지만, Cloud Shell에서 사용하면 시간과 구성이 절약됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="c3b18-147">c.</span><span class="sxs-lookup"><span data-stu-id="c3b18-147">c.</span></span> <span data-ttu-id="c3b18-148">S1 계층에서 App Service 계획을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="c3b18-149">App Service 계획은 동일한 가격 책정 계층을 공유하는 웹앱 그룹화입니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="c3b18-150">S1 계층은 무료가 아니지만 스테이징 슬롯 기능에 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="c3b18-151">d.</span><span class="sxs-lookup"><span data-stu-id="c3b18-151">d.</span></span> <span data-ttu-id="c3b18-152">동일한 리소스 그룹의 App Service 계획을 사용하여 웹앱 리소스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="c3b18-153">e.</span><span class="sxs-lookup"><span data-stu-id="c3b18-153">e.</span></span> <span data-ttu-id="c3b18-154">배포 자격 증명을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-154">Set the deployment credentials.</span></span> <span data-ttu-id="c3b18-155">해당 배포 자격 증명은 구독의 모든 웹앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="c3b18-156">사용자 이름에 특수 문자를 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="c3b18-157">f.</span><span class="sxs-lookup"><span data-stu-id="c3b18-157">f.</span></span> <span data-ttu-id="c3b18-158">로컬 Git에서의 배포를 허용하고 ‘Git 배포 URL’을 표시하도록 웹앱을 구성합니다. </span><span class="sxs-lookup"><span data-stu-id="c3b18-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="c3b18-159">**이 URL은 나중에 참조하는 데 사용합니다**.</span><span class="sxs-lookup"><span data-stu-id="c3b18-159">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="c3b18-160">g.</span><span class="sxs-lookup"><span data-stu-id="c3b18-160">g.</span></span> <span data-ttu-id="c3b18-161">‘웹앱 URL’을 표시합니다. </span><span class="sxs-lookup"><span data-stu-id="c3b18-161">Display the *web app URL*.</span></span> <span data-ttu-id="c3b18-162">빈 웹앱을 확인하려면 이 URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="c3b18-163">**이 URL은 나중에 참조하는 데 사용합니다**.</span><span class="sxs-lookup"><span data-stu-id="c3b18-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="c3b18-164">로컬 컴퓨터에서 명령 셸을 사용하여 웹앱의 프로젝트 폴더(예: `.\simple-feed-reader\SimpleFeedReader`)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="c3b18-165">다음 명령을 실행하여 배포 URL로 푸시하도록 Git을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="c3b18-166">a.</span><span class="sxs-lookup"><span data-stu-id="c3b18-166">a.</span></span> <span data-ttu-id="c3b18-167">로컬 리포지토리에 대한 원격 URL을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="c3b18-168">b.</span><span class="sxs-lookup"><span data-stu-id="c3b18-168">b.</span></span> <span data-ttu-id="c3b18-169">로컬 ‘마스터’ 분기를 *azure-prod* 원격의 ‘마스터’ 분기로 푸시합니다.  </span><span class="sxs-lookup"><span data-stu-id="c3b18-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="c3b18-170">앞에서 만든 배포 자격 증명을 입력하라는 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="c3b18-171">명령 셸의 출력을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-171">Observe the output in the command shell.</span></span> <span data-ttu-id="c3b18-172">Azure에서 ASP.NET Core 앱을 원격으로 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="c3b18-173">브라우저에서 *웹앱 URL* 로 이동한 다음, 앱이 빌드되어 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="c3b18-174">`git commit`을 사용하여 추가 변경 내용을 로컬 Git 리포지토리로 커밋할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="c3b18-175">해당 변경 내용은 위의 `git push` 명령을 사용하여 Azure로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="c3b18-176">Visual Studio를 사용한 배포</span><span class="sxs-lookup"><span data-stu-id="c3b18-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="c3b18-177">*참고: 이 섹션의 내용은 Windows에만 적용됩니다. Linux 및 macOS 사용자는 아래 2단계에 설명된 대로 변경해야 합니다. 파일을 저장한 다음, `git commit`을 사용하여 변경 내용을 로컬 리포지토리로 커밋합니다. 마지막으로, 첫 번째 섹션과 같이 `git push`를 사용하여 변경 내용을 푸시합니다.*</span><span class="sxs-lookup"><span data-stu-id="c3b18-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="c3b18-178">앱이 명령 셸에서 이미 배포되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="c3b18-179">Visual Studio의 통합 도구를 사용하여 앱의 업데이트를 배포해 봅시다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="c3b18-180">Visual Studio는 내부적으로 명령줄 도구와 동일한 작업을 수행하지만, Visual Studio의 친숙한 UI 내에서 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="c3b18-181">Visual Studio에서 *SimpleFeedReader.sln* 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="c3b18-182">솔루션 탐색기에서 *Pages\Index.cshtml* 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="c3b18-183">`<h2>Simple Feed Reader</h2>`을 `<h2>Simple Feed Reader - V2</h2>`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="c3b18-184">**Ctrl**+**Shift**+**B** 를 눌러 앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="c3b18-185">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![마우스 오른쪽 단추 클릭, 게시를 보여 주는 스크린샷](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="c3b18-187">Visual Studio에서 새 App Service 리소스를 만들 수도 있지만, 이 업데이트는 기존 배포에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="c3b18-188">**게시 대상 선택** 대화 상자의 왼쪽 목록에서 **App Service** 를 선택한 다음, **기존 항목 선택** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="c3b18-189">**게시** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-189">Click **Publish**.</span></span>
6. <span data-ttu-id="c3b18-190">**App Service** 대화 상자에서 Azure 구독을 만드는 데 사용된 Microsoft 또는 조직 계정이 오른쪽 위에 표시되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="c3b18-191">표시되지 않는 경우, 드롭다운을 클릭하고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="c3b18-192">올바른 Azure **구독** 이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="c3b18-193">**보기** 에서 **리소스 그룹** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-193">For **View** , select **Resource Group**.</span></span> <span data-ttu-id="c3b18-194">**AzureTutorial** 리소스 그룹을 펼치고 기존 웹앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="c3b18-195">**확인** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-195">Click **OK**.</span></span>

    ![App Service 게시 대화 상자를 보여 주는 스크린샷](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="c3b18-197">Visual Studio에서 앱을 빌드하고 Azure에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="c3b18-198">웹앱 URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-198">Browse to the web app URL.</span></span> <span data-ttu-id="c3b18-199">`<h2>` 요소 수정 내용이 라이브 상태인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-199">Validate that the `<h2>` element modification is live.</span></span>

![변경된 제목이 포함된 앱](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="c3b18-201">배포 슬롯</span><span class="sxs-lookup"><span data-stu-id="c3b18-201">Deployment slots</span></span>

<span data-ttu-id="c3b18-202">배포 슬롯은 프로덕션 환경에서 실행되는 앱에 영향을 주지 않고 변경 내용 스테이징을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="c3b18-203">품질 보증 팀이 스테이징된 앱 버전의 유효성을 검사한 후 프로덕션 슬롯과 스테이징 슬롯을 교환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="c3b18-204">이런 방식으로 스테이징 중인 앱이 프로덕션으로 승격됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="c3b18-205">다음 단계에서는 스테이징 슬롯을 만들고 일부 변경 내용을 배포한 다음, 확인 후에 스테이징 슬롯을 프로덕션과 교환합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="c3b18-206">아직 로그인하지 않은 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="c3b18-207">스테이징 슬롯을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-207">Create the staging slot.</span></span>

    <span data-ttu-id="c3b18-208">a.</span><span class="sxs-lookup"><span data-stu-id="c3b18-208">a.</span></span> <span data-ttu-id="c3b18-209">이름을 *staging* 으로 지정하여 배포 슬롯을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-209">Create a deployment slot with the name *staging*.</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="c3b18-210">b.</span><span class="sxs-lookup"><span data-stu-id="c3b18-210">b.</span></span> <span data-ttu-id="c3b18-211">로컬 Git의 배포를 사용하고 **staging** 배포 URL을 가져오도록 스테이징 슬롯을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="c3b18-212">**이 URL은 나중에 참조하는 데 사용합니다**.</span><span class="sxs-lookup"><span data-stu-id="c3b18-212">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="c3b18-213">c.</span><span class="sxs-lookup"><span data-stu-id="c3b18-213">c.</span></span> <span data-ttu-id="c3b18-214">스테이징 슬롯의 URL을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-214">Display the staging slot's URL.</span></span> <span data-ttu-id="c3b18-215">URL로 이동하여 빈 스테이징 슬롯을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="c3b18-216">**이 URL은 나중에 참조하는 데 사용합니다**.</span><span class="sxs-lookup"><span data-stu-id="c3b18-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="c3b18-217">텍스트 편집기 또는 Visual Studio에서 `<h2>` 요소가 `<h2>Simple Feed Reader - V3</h2>`으로 표시되도록 *Pages/Index.cshtml* 을 다시 수정한 다음, 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="c3b18-218">Visual Studio *팀 탐색기* 탭의 **변경 내용** 페이지를 사용하거나 로컬 컴퓨터의 명령 셸을 통해 다음을 입력하여 파일을 로컬 Git 리포지토리로 커밋합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="c3b18-219">로컬 컴퓨터의 명령 셸을 사용하여 스테이징 배포 URL을 Git 원격으로 추가하고 커밋된 변경 내용을 푸시합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="c3b18-220">a.</span><span class="sxs-lookup"><span data-stu-id="c3b18-220">a.</span></span> <span data-ttu-id="c3b18-221">스테이징용 원격 URL을 로컬 Git 리포지토리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="c3b18-222">b.</span><span class="sxs-lookup"><span data-stu-id="c3b18-222">b.</span></span> <span data-ttu-id="c3b18-223">로컬 ‘마스터’ 분기를 *azure-staging* 원격의 ‘마스터’ 분기로 푸시합니다.  </span><span class="sxs-lookup"><span data-stu-id="c3b18-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="c3b18-224">Azure에서 앱을 빌드하고 배포하는 동안 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="c3b18-225">V3이 스테이징 슬롯에 배포되었는지 확인하려면 브라우저 창을 두 개 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="c3b18-226">첫 번째 창에서 원래 웹앱 URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="c3b18-227">두 번째 창에서 스테이징 웹앱 URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="c3b18-228">프로덕션 URL은 앱 V2를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="c3b18-229">스테이징 URL은 앱 V3을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-229">The staging URL serves V3 of the app.</span></span>

    ![브라우저 창을 비교하는 스크린샷](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="c3b18-231">Cloud Shell에서 확인/준비된 스테이징 슬롯을 프로덕션으로 교환합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="c3b18-232">두 브라우저 창을 새로 고쳐 교환이 발생했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![교환 후의 브라우저 창 비교](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="c3b18-234">요약</span><span class="sxs-lookup"><span data-stu-id="c3b18-234">Summary</span></span>

<span data-ttu-id="c3b18-235">이 섹션에서는 다음 작업을 완료했습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="c3b18-236">샘플 앱을 다운로드하고 빌드했습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="c3b18-237">Azure Cloud Shell을 사용하여 Azure App Service 웹앱을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="c3b18-238">Git을 사용하여 Azure에 샘플 앱을 배포했습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="c3b18-239">Visual Studio를 사용하여 앱의 변경 내용을 배포했습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="c3b18-240">웹앱에 스테이징 슬롯을 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="c3b18-241">스테이징 슬롯에 업데이트를 배포했습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="c3b18-242">스테이징 및 프로덕션 슬롯을 교환했습니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="c3b18-243">다음 섹션에서는 Azure Pipelines를 사용하여 DevOps 파이프라인을 빌드하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="c3b18-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="c3b18-244">추가 참조 항목</span><span class="sxs-lookup"><span data-stu-id="c3b18-244">Additional reading</span></span>

* [<span data-ttu-id="c3b18-245">Web Apps 개요</span><span class="sxs-lookup"><span data-stu-id="c3b18-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="c3b18-246">Azure App Service에서 .NET Core 및 SQL 데이터베이스 웹앱 빌드</span><span class="sxs-lookup"><span data-stu-id="c3b18-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="c3b18-247">Azure App Service의 배포 자격 증명 구성</span><span class="sxs-lookup"><span data-stu-id="c3b18-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="c3b18-248">Azure App Service에서 스테이징 환경 설정</span><span class="sxs-lookup"><span data-stu-id="c3b18-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
