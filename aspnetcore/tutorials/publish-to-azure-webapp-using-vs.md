---
title: Visual Studio를 사용하여 Azure에 ASP.NET Core 앱 게시
author: rick-anderson
description: Visual Studio를 사용하여 Azure App Service에 ASP.NET Core 앱을 게시하는 방법을 알아봅니다.
ms.author: riande
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060224"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="e5725-103">Visual Studio를 사용하여 Azure에 ASP.NET Core 앱 게시</span><span class="sxs-lookup"><span data-stu-id="e5725-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="e5725-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e5725-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="e5725-105">macOS에서 작업하는 경우 [Mac용 Visual Studio를 사용하여 Azure App Service에 웹 앱 게시](/visualstudio/mac/publish-app-svc?view=vsmac-2019)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5725-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="e5725-106">App Service 배포 문제를 해결하려면 <xref:test/troubleshoot-azure-iis>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5725-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="e5725-107">설치</span><span class="sxs-lookup"><span data-stu-id="e5725-107">Set up</span></span>

* <span data-ttu-id="e5725-108">계정이 없는 경우 [체험 Azure 계정](https://azure.microsoft.com/free/dotnet/)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="e5725-109">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e5725-109">Create a web app</span></span>

<span data-ttu-id="e5725-110">Visual Studio 시작 페이지에서 **파일 > 새로 만들기 > 프로젝트...** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![파일 메뉴](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="e5725-112">**새 프로젝트** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="e5725-113">**새 ASP.NET Core 웹 애플리케이션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="e5725-114">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-114">Select **Next**.</span></span>

![새 프로젝트 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="e5725-116">**새 ASP.NET Core 웹 애플리케이션** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="e5725-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="e5725-117">**웹 애플리케이션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-117">Select **Web Application**.</span></span>
* <span data-ttu-id="e5725-118">인증에서 **변경** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-118">Select **Change** under Authentication.</span></span>

![새 ASP.NET Core 웹 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="e5725-120">**인증 변경** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="e5725-121">**개별 사용자 계정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-121">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="e5725-122">**확인** 을 선택하여 **새 ASP.NET Core 웹 애플리케이션** 으로 돌아간 다음 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-122">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **Create**.</span></span>

![새 ASP.NET Core 웹 인증 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="e5725-124">Visual Studio는 솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="e5725-125">앱 실행</span><span class="sxs-lookup"><span data-stu-id="e5725-125">Run the app</span></span>

* <span data-ttu-id="e5725-126">Ctrl+F5를 눌러 프로젝트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="e5725-127">**개인 정보** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-127">Test the **Privacy** link.</span></span>

![localhost의 Microsoft Edge에서 열린 웹 애플리케이션](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="e5725-129">사용자 등록</span><span class="sxs-lookup"><span data-stu-id="e5725-129">Register a user</span></span>

* <span data-ttu-id="e5725-130">**등록** 을 선택하고 새 사용자를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="e5725-131">가상의 전자 메일 주소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-131">You can use a fictitious email address.</span></span> <span data-ttu-id="e5725-132">제출하면 페이지에 다음과 같은 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="e5725-133">*"요청을 처리하는 동안 데이터베이스 작업이 실패했습니다. 애플리케이션 DB 컨텍스트에 대한 기존 마이그레이션 적용으로 이 문제를 해결할 수 있습니다.”*</span><span class="sxs-lookup"><span data-stu-id="e5725-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="e5725-134">**마이그레이션 적용** 을 선택한 다음 페이지가 업데이트되면 페이지를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![요청을 처리하는 동안 데이터베이스 작업이 실패했습니다.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="e5725-137">앱은 새 사용자를 등록하는 데 사용한 이메일 및 **로그아웃** 링크를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Microsoft Edge에서 열린 웹 애플리케이션.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="e5725-140">Azure에 앱 배포</span><span class="sxs-lookup"><span data-stu-id="e5725-140">Deploy the app to Azure</span></span>

<span data-ttu-id="e5725-141">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-141">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="e5725-143">**게시** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="e5725-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="e5725-144">**Azure** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-144">Select **Azure**.</span></span>
* <span data-ttu-id="e5725-145">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-145">Select **Next**.</span></span>

![게시 대화 상자](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="e5725-147">**게시** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="e5725-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="e5725-148">**Azure App Service(Linux)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-148">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="e5725-149">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-149">Select **Next**.</span></span>

![게시 대화 상자: Azure Service 선택](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="e5725-151">**게시** 대화 상자에서 **새 Azure App Service 만들기...** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![게시 대화 상자: Azure Service 인스턴스 선택](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="e5725-153">**App Service 만들기** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="e5725-154">**앱 이름**, **리소스 그룹** 및 **App Service 계획** 항목 필드가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-154">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="e5725-155">이러한 이름을 유지하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="e5725-156">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-156">Select **Create**.</span></span>

![App Service 만들기 대화 상자](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="e5725-158">만들기가 완료되면 대화 상자가 자동으로 닫히고 **게시** 대화 상자가 다시 포커스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="e5725-159">방금 만든 새 인스턴스가 자동으로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="e5725-160">**마침** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-160">Select **Finish**.</span></span>

![게시 대화 상자: App Service 인스턴스 선택](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="e5725-162">다음으로 **게시 프로필 요약** 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="e5725-163">이 애플리케이션에 SQL Server 데이터베이스가 필요하므로 이를 구성하라는 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="e5725-164">**구성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-164">Select **Configure**.</span></span>

![게시 프로필 요약 페이지: SQL Server 종속성 구성](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="e5725-166">**종속성 구성** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="e5725-167">**Azure SQL Database** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-167">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="e5725-168">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-168">Select **Next**.</span></span>

![SQL Server 종속성 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="e5725-170">**Azure SQL Database 구성** 대화 상자에서 **SQL 데이터베이스 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Azure SQL Database 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="e5725-172">**Azure SQL 데이터베이스 만들기** 가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="e5725-173">**데이터베이스 이름**, **리소스 그룹**, **데이터베이스 서버** 및 **App Service 계획** 항목 필드가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-173">The **Database name**, **Resource Group**, **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="e5725-174">채워진 값을 유지하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="e5725-175">선택한 **데이터베이스 서버** 에 대한 **데이터베이스 관리자 사용자 이름** 및 **데이터베이스 관리자 암호** 를 입력합니다. 사용하는 계정에 새 Azure SQL 데이터베이스를 만드는 데 필요한 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="e5725-176">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-176">Select **Create**.</span></span>

![새 Azure SQL Database 대화 상자](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="e5725-178">만들기가 완료되면 대화 상자가 자동으로 닫히고 **Azure SQL Database 구성** 대화 상자가 다시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="e5725-179">방금 만든 새 인스턴스가 자동으로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="e5725-180">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-180">Select **Next**.</span></span>

![Azure SQL Database 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="e5725-182">**Azure SQL Database 구성** 대화 상자의 다음 단계에서 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="e5725-183">**데이터베이스 연결 사용자 이름** 및 **데이터베이스 연결 암호** 필드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="e5725-184">이들은 애플리케이션이 런타임 시 데이터베이스에 연결하는 데 사용하는 세부 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="e5725-185">이전 단계에서 사용한 관리자 사용자 이름 및 암호와 동일한 세부 정보를 사용하지 않는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="e5725-186">**마침** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-186">Select **Finish**.</span></span>

![Azure SQL Database 구성 대화 상자, 연결 문자열 세부 정보](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="e5725-188">**게시 프로필 요약** 페이지에서 **설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-188">In the **Publish Profile summary** page select **Settings**:</span></span>

![게시 프로필 요약 페이지: 설정 편집](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="e5725-190">**게시** 대화 상자의 **설정** 페이지에서:</span><span class="sxs-lookup"><span data-stu-id="e5725-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="e5725-191">**데이터베이스** 를 확장하고 **런타임 시 이 연결 문자열 사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-191">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="e5725-192">**Entity Framework 마이그레이션** 을 확장하고 **게시에 이 마이그레이션 적용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="e5725-193">**저장** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-193">Select **Save**.</span></span> <span data-ttu-id="e5725-194">Visual Studio가 **게시** 대화 상자로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![게시 대화 상자: 설정 패널](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="e5725-196">**게시** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-196">Click **Publish**.</span></span> <span data-ttu-id="e5725-197">Visual Studio는 Azure에 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="e5725-198">배포가 완료되면 앱이 브라우저에서 열립니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-198">When the deployment completes, the app is opened in a browser.</span></span>

![게시 대화 상자: 설정 패널](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="e5725-200">앱 업데이트</span><span class="sxs-lookup"><span data-stu-id="e5725-200">Update the app</span></span>

* <span data-ttu-id="e5725-201">*Pages/Index.cshtml* Razor 페이지를 편집하고 내용을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-201">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="e5725-202">예를 들어 단락을 수정하여 “Hello ASP.NET Core!” 문구를 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="e5725-203">**게시 프로필 요약** 페이지에서 다시 **게시** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![게시 프로필 요약 페이지](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="e5725-205">앱이 게시된 후 변경 내용이 Azure에서 제공되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![작업이 완료되었는지 확인합니다.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="e5725-207">정리</span><span class="sxs-lookup"><span data-stu-id="e5725-207">Clean up</span></span>

<span data-ttu-id="e5725-208">앱 테스트를 완료하면 [Azure Portal](https://portal.azure.com/)로 이동하고 앱을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="e5725-209">**리소스 그룹** 을 선택한 다음 만든 리소스 그룹을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-209">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure Portal: 사이드바 메뉴에 있는 리소스 그룹](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="e5725-211">**리소스 그룹** 페이지에서 **삭제** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-211">In the **Resource groups** page, select **Delete**.</span></span>

![Azure Portal: 리소스 그룹 페이지](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="e5725-213">리소스 그룹의 이름을 입력하고 **삭제** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-213">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="e5725-214">이 자습서에서 만든 앱과 다른 모든 리소스는 이제 Azure에서 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5725-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="e5725-215">다음 단계</span><span class="sxs-lookup"><span data-stu-id="e5725-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="e5725-216">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e5725-216">Additional resources</span></span>

* <span data-ttu-id="e5725-217">Visual Studio Code는 [게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5725-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="e5725-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e5725-218">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="e5725-219">Azure 리소스 그룹</span><span class="sxs-lookup"><span data-stu-id="e5725-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="e5725-220">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="e5725-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>