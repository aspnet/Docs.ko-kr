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
ms.openlocfilehash: 18b2c6ce27132844402f88b2817a07e3588d81c1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586269"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="6c7e0-103">연속 통합 및 배포</span><span class="sxs-lookup"><span data-stu-id="6c7e0-103">Continuous integration and deployment</span></span>

<span data-ttu-id="6c7e0-104">이전 장에서는 Simple Feed Reader 앱에 대한 로컬 Git 리포지토리를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="6c7e0-105">이 장에서는 해당 코드를 GitHub 리포지토리에 게시하고 Azure Pipelines를 사용하여 Azure DevOps Services 파이프라인을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="6c7e0-106">파이프라인은 앱의 지속적인 빌드 및 배포를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="6c7e0-107">GitHub 리포지토리를 커밋하면 Azure 웹앱 스테이징 슬롯에 대한 빌드 및 배포가 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="6c7e0-108">이 섹션에서는 다음 작업을 완료하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="6c7e0-109">GitHub에 앱 코드 게시</span><span class="sxs-lookup"><span data-stu-id="6c7e0-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="6c7e0-110">로컬 Git 배포 연결 끊기</span><span class="sxs-lookup"><span data-stu-id="6c7e0-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="6c7e0-111">Azure DevOps 조직 만들기</span><span class="sxs-lookup"><span data-stu-id="6c7e0-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="6c7e0-112">Azure DevOps Services에서 팀 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="6c7e0-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="6c7e0-113">빌드 정의 만들기</span><span class="sxs-lookup"><span data-stu-id="6c7e0-113">Create a build definition</span></span>
* <span data-ttu-id="6c7e0-114">릴리스 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="6c7e0-114">Create a release pipeline</span></span>
* <span data-ttu-id="6c7e0-115">GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포</span><span class="sxs-lookup"><span data-stu-id="6c7e0-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="6c7e0-116">Azure Pipelines 파이프라인 검사</span><span class="sxs-lookup"><span data-stu-id="6c7e0-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="6c7e0-117">GitHub에 앱 코드 게시</span><span class="sxs-lookup"><span data-stu-id="6c7e0-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="6c7e0-118">브라우저 창을 열고 `https://github.com`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="6c7e0-119">머리글에서 **+** 드롭다운을 클릭하고 **새 리포지토리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![GitHub 새 리포지토리 옵션](media/cicd/github-new-repo.png)

1. <span data-ttu-id="6c7e0-121">**소유자** 드롭다운에서 계정을 선택하고 **리포지토리 이름** 텍스트 상자에 *simple-feed-reader* 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="6c7e0-122">**리포지토리 만들기** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="6c7e0-123">로컬 컴퓨터의 명령 셸을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-123">Open your local machine's command shell.</span></span> <span data-ttu-id="6c7e0-124">*simple-feed-reader* Git 리포지토리가 저장된 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="6c7e0-125">기존 *origin* 원격 항목의 이름을 *upstream* 으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="6c7e0-126">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="6c7e0-127">GitHub에서 리포지토리의 복사본을 가리키는 새 *origin* 원격 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="6c7e0-128">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="6c7e0-129">로컬 Git 리포지토리를 새로 만든 GitHub 리포지토리에 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="6c7e0-130">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-130">Execute the following command:</span></span>

    ```console
    git push -u origin main
    ```

1. <span data-ttu-id="6c7e0-131">브라우저 창을 열고 `https://github.com/<GitHub_username>/simple-feed-reader/`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="6c7e0-132">코드가 GitHub 리포지토리에 나타나는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="6c7e0-133">로컬 Git 배포 연결 끊기</span><span class="sxs-lookup"><span data-stu-id="6c7e0-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="6c7e0-134">다음 단계를 수행하여 로컬 Git 배포를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="6c7e0-135">Azure Pipelines(Azure DevOps 서비스)는 해당 기능을 대체하고 보강합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="6c7e0-136">[Azure Portal](https://portal.azure.com/)을 열고 ‘스테이징’ *(mywebapp\<unique_number\>/staging)* 웹앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="6c7e0-137">포털의 검색 상자에 *스테이징* 을 입력하여 웹앱을 신속하게 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![스테이징 웹앱 검색 용어](media/cicd/portal-search-box.png)

1. <span data-ttu-id="6c7e0-139">**배포 센터** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-139">Click **Deployment Center**.</span></span> <span data-ttu-id="6c7e0-140">새 패널이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-140">A new panel appears.</span></span> <span data-ttu-id="6c7e0-141">**연결 끊기** 를 클릭하여 이전 장에 추가된 로컬 Git 소스 제어 구성을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="6c7e0-142">**예** 단추를 클릭하여 제거 작업을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="6c7e0-143">*mywebapp<unique_number>* App Service로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="6c7e0-144">미리 알림으로 포털의 검색 상자를 사용하여 App Service를 신속하게 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="6c7e0-145">**배포 센터** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-145">Click **Deployment Center**.</span></span> <span data-ttu-id="6c7e0-146">새 패널이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-146">A new panel appears.</span></span> <span data-ttu-id="6c7e0-147">**연결 끊기** 를 클릭하여 이전 장에 추가된 로컬 Git 소스 제어 구성을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="6c7e0-148">**예** 단추를 클릭하여 제거 작업을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="6c7e0-149">Azure DevOps 조직 만들기</span><span class="sxs-lookup"><span data-stu-id="6c7e0-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="6c7e0-150">브라우저를 열고 [Azure DevOps 조직 만들기 페이지](https://go.microsoft.com/fwlink/?LinkId=307137)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="6c7e0-151">**기억하기 쉬운 이름 선택** 텍스트 상자에 고유한 이름을 입력하여 Azure DevOps 조직에 액세스하기 위한 URL을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="6c7e0-152">코드가 GitHub 리포지토리에서 호스트되므로 **Git** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="6c7e0-153">**계속** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-153">Click the **Continue** button.</span></span> <span data-ttu-id="6c7e0-154">잠시 지연된 후 *MyFirstProject* 라는 계정 및 팀 프로젝트가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Azure DevOps 조직 만들기 페이지](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="6c7e0-156">Azure DevOps 조직 및 프로젝트를 사용할 준비가 되었음을 나타내는 확인 메일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="6c7e0-157">**프로젝트 시작** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-157">Click the **Start your project** button:</span></span>

    ![프로젝트 시작 단추](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="6c7e0-159">브라우저에서 *\<account_name\>.visualstudio.com* 이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="6c7e0-160">*MyFirstProject* 링크를 클릭하여 프로젝트의 DevOps 파이프라인 구성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="6c7e0-161">Azure Pipelines 파이프라인 구성</span><span class="sxs-lookup"><span data-stu-id="6c7e0-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="6c7e0-162">세 가지 고유한 단계를 완료해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="6c7e0-163">다음 세 섹션의 단계를 완료하면 운영 DevOps 파이프라인이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="6c7e0-164">Azure DevOps에 GitHub 리포지토리에 대한 액세스 권한 부여</span><span class="sxs-lookup"><span data-stu-id="6c7e0-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="6c7e0-165">**or build code from an external repository** Accordion을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="6c7e0-166">**빌드 설정** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-166">Click the **Setup Build** button:</span></span>

    ![빌드 설정 단추](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="6c7e0-168">**원본 선택** 섹션에서 **GitHub** 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![원본 선택 - GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="6c7e0-170">Azure DevOps가 GitHub 리포지토리에 액세스하려면 먼저 권한 부여가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="6c7e0-171">**연결 이름** 텍스트 상자에 *<GitHub_username> GitHub 연결* 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="6c7e0-172">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="6c7e0-172">For example:</span></span>

    ![GitHub 연결 이름](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="6c7e0-174">GitHub 계정에서 2단계 인증을 사용하도록 설정한 경우 개인용 액세스 토큰이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="6c7e0-175">이 경우 **GitHub 개인용 액세스 토큰으로 권한 부여** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="6c7e0-176">도움말을 보려면 [공식 GitHub 개인용 액세스 토큰 만들기 지침](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="6c7e0-177">*리포지토리* 사용 권한 범위만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="6c7e0-178">그렇지 않으면 **OAuth를 사용하여 권한 부여** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="6c7e0-179">메시지가 표시되면 GitHub 계정에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="6c7e0-180">그런 다음, 권한 부여를 선택하여 Azure DevOps 조직에 대한 액세스 권한을 부여합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="6c7e0-181">성공하면 새 서비스 엔드포인트가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="6c7e0-182">**리포지토리** 단추 옆에 있는 줄임표 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="6c7e0-183">목록에서 *<GitHub_username>/simple-feed-reader* 리포지토리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="6c7e0-184">**선택** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="6c7e0-185">**수동 및 예약된 빌드의 기본 분기** 드롭다운에서 기본 분기(*main*)를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-185">Select the default branch (*main*) from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="6c7e0-186">**계속** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-186">Click the **Continue** button.</span></span> <span data-ttu-id="6c7e0-187">템플릿 선택 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="6c7e0-188">빌드 정의 만들기</span><span class="sxs-lookup"><span data-stu-id="6c7e0-188">Create the build definition</span></span>

1. <span data-ttu-id="6c7e0-189">템플릿 선택 페이지의 검색 상자에 *ASP.NET Core* 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![템플릿 페이지의 ASP.NET Core 검색](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="6c7e0-191">템플릿 검색 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-191">The template search results appear.</span></span> <span data-ttu-id="6c7e0-192">**ASP.NET Core** 템플릿 위로 마우스를 이동하고 **적용** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="6c7e0-193">빌드 정의의 **작업** 탭이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="6c7e0-194">**트리거** 탭을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="6c7e0-195">**지속적인 통합 사용** 상자를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="6c7e0-196">**분기 필터** 섹션에서 **형식** 드롭다운이 *포함* 으로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="6c7e0-197">**분기 사양** 드롭다운을 *main* 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-197">Set the **Branch specification** drop-down to *main*.</span></span>

    ![지속적인 통합 사용 설정](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="6c7e0-199">이러한 설정으로 인해 변경 내용이 GitHub 리포지토리의 기본 분기(*main*)로 푸시될 때 빌드가 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-199">These settings cause a build to trigger when any change is pushed to the default branch (*main*) of the GitHub repository.</span></span> <span data-ttu-id="6c7e0-200">지속적인 통합은 [GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포](#commit-changes-to-github-and-automatically-deploy-to-azure) 섹션에서 테스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="6c7e0-201">**저장 및 큐에 넣기** 단추를 클릭하고 **저장** 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![저장 단추](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="6c7e0-203">다음과 같은 모달 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-203">The following modal dialog appears:</span></span>

    ![빌드 정의 저장 - 모달 대화 상자](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="6c7e0-205">기본 폴더인 *\\* 를 사용하고 **저장** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="6c7e0-206">릴리스 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="6c7e0-206">Create the release pipeline</span></span>

1. <span data-ttu-id="6c7e0-207">팀 프로젝트의 **릴리스** 탭을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="6c7e0-208">**새 파이프라인** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-208">Click the **New pipeline** button.</span></span>

    ![릴리스 탭 - 새 정의 단추](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="6c7e0-210">템플릿 선택 창이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="6c7e0-211">템플릿 선택 페이지에서 검색 상자에 *App Service* 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![릴리스 파이프라인 템플릿 검색 상자](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="6c7e0-213">템플릿 검색 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-213">The template search results appear.</span></span> <span data-ttu-id="6c7e0-214">**Azure App Service 배포 및 슬롯** 템플릿 위로 마우스를 가져간 후 **적용** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="6c7e0-215">릴리스 파이프라인의 **파이프라인** 탭이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![릴리스 파이프라인 파이프라인 탭](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="6c7e0-217">**아티팩트** 섹션에서 **추가** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="6c7e0-218">**아티팩트 추가** 패널이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-218">The **Add artifact** panel appears:</span></span>

    ![릴리스 파이프라인 - 아티팩트 추가 패널](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="6c7e0-220">**원본 유형** 섹션에서 **빌드** 타일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="6c7e0-221">이 형식을 사용하면 빌드 정의에 릴리스 파이프라인을 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="6c7e0-222">**프로젝트** 드롭다운에서 *MyFirstProject* 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="6c7e0-223">**원본(빌드 정의)** 드롭다운에서 빌드 정의 이름 *MyFirstProject-ASP.NET Core-CI* 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="6c7e0-224">**기본 버전** 드롭다운에서 *최신* 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="6c7e0-225">이 옵션은 가장 최근의 빌드 정의 실행을 통해 생성된 아티팩트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="6c7e0-226">**원본 별칭** 텍스트 상자의 텍스트를 *Drop* 으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="6c7e0-227">**추가** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-227">Click the **Add** button.</span></span> <span data-ttu-id="6c7e0-228">**아티팩트** 섹션이 업데이트되어 변경 내용을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="6c7e0-229">번개 모양 아이콘을 클릭하여 지속적인 배포를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![릴리스 파이프라인 아티팩트 - 번개 모양 아이콘](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="6c7e0-231">이 옵션을 사용하도록 설정하면 새 빌드를 사용할 수 있을 때마다 배포가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="6c7e0-232">**지속적인 배포 트리거** 패널이 오른쪽에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="6c7e0-233">설정/해제 단추를 클릭하여 기능을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="6c7e0-234">**끌어오기 요청 트리거** 를 사용하도록 설정할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="6c7e0-235">**빌드 분기 필터** 섹션에서 **추가** 드롭다운을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="6c7e0-236">**빌드 정의의 기본 분기** 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="6c7e0-237">이 필터를 설정하면 GitHub 리포지토리 기본 분기(*main*)의 빌드에 대해서만 릴리스가 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-237">This filter causes the release to trigger only for a build from the GitHub repository's default branch (*main*).</span></span>
1. <span data-ttu-id="6c7e0-238">**저장** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-238">Click the **Save** button.</span></span> <span data-ttu-id="6c7e0-239">결과 **저장** 모달 대화 상자에서 **확인** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="6c7e0-240">**환경 1** 상자를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="6c7e0-241">**환경** 패널이 오른쪽에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="6c7e0-242">**환경 이름** 텍스트 상자의 *환경 1* 텍스트를 *프로덕션* 으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![릴리스 파이프라인 - 환경 이름 텍스트 상자](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="6c7e0-244">**프로덕션** 상자에서 **1단계, 2개 작업** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![릴리스 파이프라인 - Production environment link.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="6c7e0-246">환경의 **작업** 탭이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="6c7e0-247">**슬롯에 Azure App Service 배포** 작업을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="6c7e0-248">해당 설정이 오른쪽의 패널에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="6c7e0-249">**Azure 구독** 드롭다운에서 App Service와 연결된 Azure 구독을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="6c7e0-250">이 구독을 선택했으면 **권한 부여** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="6c7e0-251">**앱 유형** 드롭다운에서 *웹앱* 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="6c7e0-252">**App Service 이름** 드롭다운에서 *mywebapp/<unique_number/>* 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="6c7e0-253">**리소스 그룹** 드롭다운에서 *AzureTutorial* 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="6c7e0-254">**슬롯** 드롭다운에서 *스테이징* 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="6c7e0-255">**저장** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="6c7e0-256">기본 릴리스 파이프라인 이름 위로 마우스를 가져갑니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="6c7e0-257">연필 아이콘을 클릭하여 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="6c7e0-258">*MyFirstProject-ASP.NET Core-CD* 를 이름으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![릴리스 파이프라인 이름](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="6c7e0-260">**저장** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="6c7e0-261">GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포</span><span class="sxs-lookup"><span data-stu-id="6c7e0-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="6c7e0-262">Visual Studio에서 *SimpleFeedReader.sln* 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="6c7e0-263">솔루션 탐색기에서 *Pages\Index.cshtml* 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="6c7e0-264">`<h2>Simple Feed Reader - V3</h2>`을 `<h2>Simple Feed Reader - V4</h2>`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="6c7e0-265">**Ctrl**+**Shift**+**B** 를 눌러 앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="6c7e0-266">GitHub 리포지토리에 파일을 커밋합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="6c7e0-267">Visual Studio *팀 탐색기* 탭의 **변경** 페이지를 사용하거나 로컬 컴퓨터의 명령 셸을 사용하여 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="6c7e0-268">기본 분기(*main*)의 변경 내용을 GitHub 리포지토리의 *origin* 원격으로 푸시합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-268">Push the change in the default branch (*main*) to the *origin* remote of your GitHub repository.</span></span> <span data-ttu-id="6c7e0-269">다음 명령에서 자리 표시자 `{BRANCH}`를 기본 분기로 바꿉니다(`main` 사용).</span><span class="sxs-lookup"><span data-stu-id="6c7e0-269">In the following command, replace the placeholder `{BRANCH}` with the default branch (use `main`):</span></span>

    ```console
    git push origin {BRANCH}
    ```

    <span data-ttu-id="6c7e0-270">커밋이 GitHub 리포지토리의 기본 분기(*main*)에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-270">The commit appears in the GitHub repository's default branch (*main*):</span></span>

    ![기본 분기(main)의 GitHub 커밋](media/cicd/github-commit.png)

    <span data-ttu-id="6c7e0-272">빌드 정의의 **트리거** 탭에서 연속 통합을 사용하도록 설정했으므로 빌드가 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-272">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![지속적인 통합 사용](media/cicd/enable-ci.png)

1. <span data-ttu-id="6c7e0-274">Azure DevOps Services의 **Azure Pipelines** > **빌드** 페이지의 **큐 대기** 탭으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-274">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="6c7e0-275">큐 대기 빌드는 빌드를 트리거한 분기 및 커밋을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-275">The queued build shows the branch and commit that triggered the build:</span></span>

    ![큐 대기 빌드](media/cicd/build-queued.png)

1. <span data-ttu-id="6c7e0-277">빌드가 성공적으로 완료되면 Azure로 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-277">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="6c7e0-278">브라우저에서 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-278">Navigate to the app in the browser.</span></span> <span data-ttu-id="6c7e0-279">"V4" 텍스트가 제목에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-279">Notice that the "V4" text appears in the heading:</span></span>

    ![업데이트된 앱](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="6c7e0-281">Azure Pipelines 파이프라인 검사</span><span class="sxs-lookup"><span data-stu-id="6c7e0-281">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="6c7e0-282">빌드 정의</span><span class="sxs-lookup"><span data-stu-id="6c7e0-282">Build definition</span></span>

<span data-ttu-id="6c7e0-283">이름이 *MyFirstProject-ASP.NET Core-CI* 인 빌드 정의를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-283">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="6c7e0-284">완료되면 빌드는 게시할 자산을 포함하는 *.zip* 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-284">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="6c7e0-285">릴리스 파이프라인은 해당 자산을 Azure에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-285">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="6c7e0-286">빌드 정의의 **작업** 탭에는 사용되는 개별 단계가 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-286">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="6c7e0-287">5개의 빌드 작업이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-287">There are five build tasks.</span></span>

![빌드 정의 작업](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="6c7e0-289">**복원** &mdash; `dotnet restore` 명령을 실행하여 앱의 NuGet 패키지를 복원합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-289">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="6c7e0-290">사용되는 기본 패키지 피드는 nuget.org입니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-290">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="6c7e0-291">**빌드** &mdash; `dotnet build --configuration release` 명령을 실행하여 앱의 코드를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-291">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="6c7e0-292">이 `--configuration` 옵션은 프로덕션 환경에 배포하는 데 적합한 최적화된 버전의 코드를 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-292">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="6c7e0-293">예를 들어, 디버그 구성이 필요한 경우 빌드 정의의 **변수** 탭에서 *BuildConfiguration* 변수를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-293">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="6c7e0-294">**테스트** &mdash; `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` 명령을 실행하여 앱의 단위 테스트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-294">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="6c7e0-295">단위 테스트는 `**/*Tests/*.csproj` glob 패턴과 일치하는 모든 C# 프로젝트 내에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-295">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="6c7e0-296">테스트 결과는 `--results-directory` 옵션으로 지정된 위치에 있는 *.trx* 파일에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-296">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="6c7e0-297">테스트가 실패하면 빌드가 실패하고 배포되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-297">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="6c7e0-298">단위 테스트가 작동하는지 확인하려면 *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* 를 수정하여 테스트 중 하나를 의도적으로 중단합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-298">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="6c7e0-299">예를 들어 `Returns_News_Stories_Given_Valid_Uri` 메서드에서 `Assert.True(result.Count > 0);`을 `Assert.False(result.Count > 0);`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-299">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="6c7e0-300">변경 내용을 커밋하고 GitHub로 푸시합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-300">Commit and push the change to GitHub.</span></span> <span data-ttu-id="6c7e0-301">빌드가 트리거되고 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-301">The build is triggered and fails.</span></span> <span data-ttu-id="6c7e0-302">빌드 파이프라인 상태가 **실패** 로 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-302">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="6c7e0-303">변경을 되돌리고, 커밋한 후 다시 푸시합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-303">Revert the change, commit, and push again.</span></span> <span data-ttu-id="6c7e0-304">빌드가 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-304">The build succeeds.</span></span>

1. <span data-ttu-id="6c7e0-305">**게시** &mdash; `dotnet publish --configuration release --output <local_path_on_build_agent>` 명령을 실행하여 배포할 아티팩트가 포함된 *.zip* 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-305">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="6c7e0-306">`--output` 옵션은 *.zip* 파일의 게시 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-306">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="6c7e0-307">이 위치는 `$(build.artifactstagingdirectory)`라는 [미리 정의된 변수](/azure/devops/pipelines/build/variables)를 전달하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-307">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="6c7e0-308">해당 변수는 빌드 에이전트의 *c:\agent\_work\1\a* 와 같은 로컬 경로로 확장됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-308">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="6c7e0-309">**아티팩트 게시** &mdash; **게시** 작업에 의해 생성된 *.zip* 파일을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-309">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="6c7e0-310">이 작업은 *.zip* 파일 위치를 미리 정의된 변수 `$(build.artifactstagingdirectory)`에 해당하는 매개 변수로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-310">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="6c7e0-311">*.zip* 파일은 *drop* 이라는 폴더로 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-311">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="6c7e0-312">빌드 정의의 **요약** 링크를 클릭하여 정의가 포함된 빌드 기록을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-312">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![빌드 정의 기록을 보여 주는 스크린샷](media/cicd/build-definition-summary.png)

<span data-ttu-id="6c7e0-314">결과 페이지에서 고유한 빌드 번호에 해당하는 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-314">On the resulting page, click the link corresponding to the unique build number:</span></span>

![빌드 정의 요약 페이지를 보여 주는 스크린샷](media/cicd/build-definition-completed.png)

<span data-ttu-id="6c7e0-316">이 특정 빌드의 요약이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-316">A summary of this specific build is displayed.</span></span> <span data-ttu-id="6c7e0-317">**아티팩트** 탭을 클릭하고 빌드에서 생성된 *drop* 폴더가 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-317">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![빌드 정의 아티팩트 - drop 폴더를 보여 주는 스크린샷](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="6c7e0-319">**다운로드** 및 **탐색** 링크를 사용하여 게시된 아티팩트를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-319">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="6c7e0-320">릴리스 파이프라인</span><span class="sxs-lookup"><span data-stu-id="6c7e0-320">Release pipeline</span></span>

<span data-ttu-id="6c7e0-321">이름이 *MyFirstProject-ASP.NET Core-CD* 인 릴리스 파이프라인을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-321">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![릴리스 파이프라인 개요를 보여 주는 스크린샷](media/cicd/release-definition-overview.png)

<span data-ttu-id="6c7e0-323">릴리스 파이프라인의 두 가지 주요 구성 요소는 **아티팩트** 및 **환경** 입니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-323">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="6c7e0-324">**아티팩트** 섹션의 상자를 클릭하면 다음 패널이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-324">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![릴리스 파이프라인 아티팩트를 보여 주는 스크린샷](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="6c7e0-326">**원본(빌드 정의)** 값은 이 릴리스 파이프라인이 연결된 빌드 정의를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-326">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="6c7e0-327">빌드 정의의 성공적인 실행으로 생성된 *.zip* 파일은 Azure에 배포하기 위해 *프로덕션* 환경에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-327">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="6c7e0-328">*프로덕션* 환경 상자의 *1단계, 2개 작업* 링크를 클릭하여 릴리스 파이프라인 작업을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-328">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![릴리스 파이프라인 작업을 보여 주는 스크린샷](media/cicd/release-definition-tasks.png)

<span data-ttu-id="6c7e0-330">릴리스 파이프라인은 다음 두 작업으로 구성됩니다. *슬롯에 Azure App Service 배포* 및 *Azure App Service 관리 - 슬롯 교환*.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-330">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="6c7e0-331">첫 번째 작업을 클릭하면 다음과 같은 작업 구성이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-331">Clicking the first task reveals the following task configuration:</span></span>

![릴리스 파이프라인 배포 작업을 보여 주는 스크린샷](media/cicd/release-definition-task1.png)

<span data-ttu-id="6c7e0-333">Azure 구독, 서비스 유형, 웹앱 이름, 리소스 그룹 및 배포 슬롯은 배포 작업에 정의되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-333">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="6c7e0-334">**패키지 또는 폴더** 텍스트 상자에는 *mywebapp\<unique_number\>* 웹앱의 ‘스테이징’ 슬롯에 추출 및 배포할 *.zip* 파일 경로가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-334">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="6c7e0-335">슬롯 교환 작업을 클릭하면 다음과 같은 작업 구성이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-335">Clicking the slot swap task reveals the following task configuration:</span></span>

![릴리스 파이프라인 슬롯 교환 작업을 보여 주는 스크린샷](media/cicd/release-definition-task2.png)

<span data-ttu-id="6c7e0-337">구독, 리소스 그룹, 서비스 유형, 웹앱 이름 및 배포 슬롯 정보가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-337">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="6c7e0-338">**프로덕션과 교환** 확인란이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-338">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="6c7e0-339">따라서 *스테이징* 슬롯에 배포된 비트가 프로덕션 환경으로 교체됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c7e0-339">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="6c7e0-340">추가 참조 항목</span><span class="sxs-lookup"><span data-stu-id="6c7e0-340">Additional reading</span></span>

* [<span data-ttu-id="6c7e0-341">Azure Pipelines를 사용하여 첫 번째 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="6c7e0-341">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="6c7e0-342">빌드 및 .NET Core 프로젝트</span><span class="sxs-lookup"><span data-stu-id="6c7e0-342">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="6c7e0-343">Azure Pipelines를 사용하여 웹앱 배포</span><span class="sxs-lookup"><span data-stu-id="6c7e0-343">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
