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
ms.openlocfilehash: 561f41dd9d50df0651c060fac4f75d9d59244029
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605648"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="c3770-103">ASP.NET Core와 함께 Visual Studio 및 Git을 사용하여 Azure에 지속적인 배포</span><span class="sxs-lookup"><span data-stu-id="c3770-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="c3770-104">작성자: [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="c3770-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="c3770-105">이 자습서에서는 Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 지속적인 배포를 사용하여 Visual Studio에서 Azure App Service에 배포하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="c3770-106">Azure DevOps Services를 사용하여 [Azure App Service](/azure/app-service/app-service-web-overview)에 대한 CD(지속적인 업데이트) 워크플로를 구성하는 방법을 보여 주는 [Azure Pipelines를 사용하여 첫 번째 파이프라인 만들기](/azure/devops/pipelines/get-started-yaml)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c3770-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="c3770-107">Azure Pipelines는 Azure DevOps Services의 서비스 중 하나로, Azure App Service에서 호스트되는 앱의 업데이트를 게시하는 강력한 배포 파이프라인을 간단하게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="c3770-108">파이프라인을 빌드하고, 테스트를 실행하고, 스테이징 슬롯에 배포하고, 프로덕션에 배포하도록 Azure Portal에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="c3770-109">이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="c3770-110">계정을 얻으려면 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F)하거나 [평가판에 등록](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F)합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c3770-111">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="c3770-111">Prerequisites</span></span>

<span data-ttu-id="c3770-112">이 자습서에서는 다음 소프트웨어가 설치되어 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="c3770-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3770-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="c3770-114">Windows용 [Git](https://git-scm.com/downloads)</span><span class="sxs-lookup"><span data-stu-id="c3770-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="c3770-115">ASP.NET Core 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="c3770-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="c3770-116">Visual Studio를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="c3770-117">**파일** 메뉴에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="c3770-118">**ASP.NET Core 웹 애플리케이션** 프로젝트 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="c3770-119">**설치됨** > **템플릿** > **Visual C#**  >  **.NET Core** 아래에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="c3770-120">프로젝트 이름을 `SampleWebAppDemo`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="c3770-121">**새 Git 리포지토리 만들기** 옵션을 선택하고 **확인** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![새 프로젝트 대화 상자](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="c3770-123">**새 ASP.NET Core 프로젝트** 대화 상자에서 ASP.NET Core **빈** 템플릿을 선택한 후 **확인** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![새 ASP.NET Core 프로젝트 대화 상자](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="c3770-125">최신 .NET Core 릴리스는 2.0입니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="c3770-126">로컬로 웹앱 실행</span><span class="sxs-lookup"><span data-stu-id="c3770-126">Running the web app locally</span></span>

1. <span data-ttu-id="c3770-127">Visual Studio에서 앱 만들기를 완료하면 **디버그** > **디버깅 시작** 을 선택하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="c3770-128">또는 **F5** 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="c3770-129">Visual Studio 및 새 앱을 초기화하는 데 시간이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="c3770-130">완료되면 브라우저에 실행 중인 앱이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-130">Once it's complete, the browser shows the running app.</span></span>

   ![브라우저 창에서는 'Hello World!'을 표시하는 애플리케이션이 실행 중이라고 표시합니다.](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="c3770-132">실행 중인 웹앱을 검토한 후 브라우저를 닫고 Visual Studio의 도구 모음에서 “디버깅 중지” 아이콘을 선택하여 앱을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="c3770-133">Azure Portal에서 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="c3770-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="c3770-134">다음 단계에서는 Azure Portal에서 웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="c3770-135">[Azure Portal](https://portal.azure.com)에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="c3770-136">포털 인터페이스의 왼쪽 위에 있는 **새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="c3770-137">**웹 + 모바일** > **웹앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Microsoft Azure Portal: 새 단추: Marketplace 아래의 웹 + 모바일: 추천 앱 아래의 Web App 단추](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="c3770-139">**Web App** 블레이드에서 **App Service 이름** 에 고유한 값을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Web App 블레이드](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="c3770-141">**App Service 이름** 이름은 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="c3770-142">이름이 제공되면 포털에서 이 규칙이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="c3770-143">다른 값을 제공하는 경우 이 자습서에서 표시되는 각 **SampleWebAppDemo** 를 해당 값으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="c3770-144">또한 **Web App** 블레이드에서 기존 **App Service 계획/위치** 를 선택하거나 새로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="c3770-145">새 플랜을 만들 경우 가격 책정 계층, 위치 및 기타 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="c3770-146">App Service 계획에 대한 자세한 내용은 [Azure App Service 계획 세부 개요](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c3770-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="c3770-147">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-147">Select **Create**.</span></span> <span data-ttu-id="c3770-148">Azure에서는 웹앱을 프로비전하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-148">Azure will provision and start the web app.</span></span>

   ![Azure Portal: 샘플 Web App 데모 01 Essentials 블레이드](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="c3770-150">새 웹앱에 대한 Git 게시 사용</span><span class="sxs-lookup"><span data-stu-id="c3770-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="c3770-151">Git은 Azure App Service 웹앱을 배포하는 데 사용할 수 있는 분산 버전 제어 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="c3770-152">웹앱 코드는 로컬 Git 리포지토리에 저장되고 코드는 원격 리포지토리로 푸시하여 Azure에 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="c3770-153">[Azure Portal](https://portal.azure.com)에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="c3770-154">**App Services** 를 선택하여 Azure 구독과 연결된 App Service 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="c3770-155">이 자습서의 이전 섹션에서 만든 웹앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="c3770-156">**배포** 블레이드에서 **배포 옵션** > **원본 선택** > **로컬 Git 리포지토리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![설정 블레이드: 배포 소스 블레이드: 소스 블레이드 선택](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="c3770-158">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-158">Select **OK**.</span></span>

1. <span data-ttu-id="c3770-159">웹앱 또는 다른 App Service 앱을 게시하기 위한 배포 자격 증명이 이전에 설정되지 않은 경우 지금 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="c3770-160">**설정** > **배포 자격 증명** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="c3770-161">**배포 자격 증명 설정** 블레이드가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="c3770-162">사용자 이름 및 암호를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-162">Create a user name and password.</span></span> <span data-ttu-id="c3770-163">나중에 Git을 설정할 때 사용할 암호를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="c3770-164">**저장** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-164">Select **Save**.</span></span>

1. <span data-ttu-id="c3770-165">**웹앱** 블레이드에서 **설정** > **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="c3770-166">배포할 원격 Git 리포지토리 URL이 **GIT URL** 아래에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="c3770-167">나중에 자습서에서 사용할 **GIT URL** 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Azure Portal: 애플리케이션 속성 블레이드](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="c3770-169">Azure App Service에 웹앱 게시</span><span class="sxs-lookup"><span data-stu-id="c3770-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="c3770-170">이 섹션에서는 Visual Studio를 사용하여 로컬 Git 리포지토리를 만들고 해당 리포지토리로부터 Azure에 푸시하여 웹앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="c3770-171">관련 단계에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-171">The steps involved include the following:</span></span>

* <span data-ttu-id="c3770-172">Azure에 로컬 리포지토리를 배포할 수 있도록 GIT URL 값을 사용하여 원격 리포지토리 설정을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="c3770-173">프로젝트에 변경 내용을 커밋합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-173">Commit project changes.</span></span>
* <span data-ttu-id="c3770-174">로컬 리포지토리에서 Azure의 원격 리포지토리로 프로젝트 변경 내용을 푸시합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="c3770-175">**솔루션 탐색기** 에서 **솔루션 'SampleWebAppDemo'** 를 마우스 오른쪽 단추로 클릭하고 **커밋** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="c3770-176">**팀 탐색기** 가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-176">The **Team Explorer** is displayed.</span></span>

   ![팀 탐색기 연결 탭](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="c3770-178">**팀 탐색기** 에서 **홈**(홈 아이콘) > **설정** > **리포지토리 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="c3770-179">**리포지토리 설정** 의 **원격** 섹션에서 **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="c3770-180">**원격 추가** 대화 상자가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="c3770-181">원격 리포지토리의 **이름** 을 **Azure SampleApp** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="c3770-182">**페치** 의 값을 이 자습서의 앞부분에 나오는 Azure에서 복사한 **Git URL** 로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="c3770-183">이 URL은 **.git** 로 끝납니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-183">Note that this is the URL that ends with **.git**.</span></span>

   ![원격 대화 상자 편집](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="c3770-185">대안으로 **명령 창** 을 열고, 프로젝트 디렉터리로 변경하고, 명령을 입력하여 **명령 창** 에서 원격 리포지토리를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="c3770-186">예:</span><span class="sxs-lookup"><span data-stu-id="c3770-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="c3770-187">**홈**(홈 아이콘) > **설정** > **전역 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="c3770-188">이름 및 전자 메일 주소가 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="c3770-189">필요한 경우 **업데이트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="c3770-190">**홈** > **변경 내용** 을 선택하여 **변경 내용** 보기로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="c3770-191">**Initial Push #1** 과 같은 커밋 메시지를 입력하고 **커밋** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="c3770-192">이렇게 하면 로컬에서 ‘커밋’이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-192">This action creates a *commit* locally.</span></span>

   ![팀 탐색기 연결 탭](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="c3770-194">대안으로 **명령 창** 을 열고, 프로젝트 디렉터리로 변경하고, git 명령을 입력하여 **명령 창** 에서 변경 내용을 커밋합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="c3770-195">예:</span><span class="sxs-lookup"><span data-stu-id="c3770-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="c3770-196">**홈** > **동기화** > **동작** > **명령 프롬프트 열기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="c3770-197">명령 프롬프트가 프로젝트 디렉터리에서 열립니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="c3770-198">명령 창에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp main`

1. <span data-ttu-id="c3770-199">Azure에서 이전에 만든 Azure **배포 자격 증명** 암호를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="c3770-200">이 명령은 로컬 프로젝트 파일을 Azure에 푸시하는 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="c3770-201">위 명령의 출력은 성공적으로 배포했다는 메시지와 함께 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      main -> main
   Branch main set up to track remote branch main from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="c3770-202">프로젝트의 협업이 필요한 경우 Azure로 푸시하기 전에 [GitHub](https://github.com)로 푸시하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="c3770-203">활성 배포 확인</span><span class="sxs-lookup"><span data-stu-id="c3770-203">Verify the Active Deployment</span></span>

<span data-ttu-id="c3770-204">로컬 환경에서 웹앱을 Azure로 성공적으로 전송했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="c3770-205">[Azure Portal](https://portal.azure.com)에서 웹앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="c3770-206">**배포** > **배포 옵션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-206">Select **Deployment** > **Deployment options**.</span></span>

![Azure Portal: 설정 블레이드: 성공적인 배포를 보여주는 배포 블레이드](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="c3770-208">Azure에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="c3770-208">Run the app in Azure</span></span>

<span data-ttu-id="c3770-209">이제 웹앱이 Azure에 배포되었으므로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="c3770-210">이는 두 가지 방법으로 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="c3770-211">Azure Portal에서 웹앱에 대한 웹앱 블레이드를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="c3770-212">**찾아보기** 를 선택하여 기본 브라우저에서 앱을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="c3770-213">브라우저를 열고 웹앱의 URL을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="c3770-214">예: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="c3770-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="c3770-215">웹앱 업데이트 및 다시 게시</span><span class="sxs-lookup"><span data-stu-id="c3770-215">Update the web app and republish</span></span>

<span data-ttu-id="c3770-216">로컬 코드를 변경한 후 다시 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="c3770-217">Visual Studio의 **솔루션 탐색기** 에서 *Startup.cs* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="c3770-218">`Configure` 메서드에서 `Response.WriteAsync` 메서드를 수정하여 다음과 같이 표시되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="c3770-219">변경 내용을 *Startup.cs* 에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="c3770-220">**솔루션 탐색기** 에서 **솔루션 'SampleWebAppDemo'** 를 마우스 오른쪽 단추로 클릭하고 **커밋** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="c3770-221">**팀 탐색기** 가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="c3770-222">`Update #2`와 같은 커밋 메시지를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="c3770-223">**커밋** 단추를 눌러 프로젝트 변경 내용을 커밋합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="c3770-224">**홈** > **동기화** > **작업** > **푸시** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="c3770-225">대안으로 **명령 창** 을 열고, 프로젝트 디렉터리를 변경하고, git 명령을 입력하여 **명령 창** 의 변경 내용을 푸시합니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="c3770-226">예:</span><span class="sxs-lookup"><span data-stu-id="c3770-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp main`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="c3770-227">Azure에서 업데이트된 웹앱 보기</span><span class="sxs-lookup"><span data-stu-id="c3770-227">View the updated web app in Azure</span></span>

<span data-ttu-id="c3770-228">Azure Portal의 웹앱 블레이드에서 **찾아보기** 를 선택하거나 브라우저를 열고 웹앱의 URL을 입력하여 업데이트된 웹앱을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="c3770-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="c3770-229">예: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="c3770-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3770-230">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c3770-230">Additional resources</span></span>

* [<span data-ttu-id="c3770-231">Azure Pipelines를 사용하여 첫 번째 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="c3770-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="c3770-232">프로젝트 Kudu</span><span class="sxs-lookup"><span data-stu-id="c3770-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
