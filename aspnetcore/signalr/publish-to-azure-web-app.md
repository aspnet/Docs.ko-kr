---
title: :::no-loc(SignalR):::Azure App Service에 ASP.NET Core 앱 게시
author: bradygaster
description: 'Azure App Service에 ASP.NET Core 앱을 게시 하는 방법을 알아봅니다 :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/02/2020
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
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 8e6d36fe0b38486f94078b8f9cf12b852da7e0d9
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234514"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="854d5-103">:::no-loc(SignalR):::Azure App Service에 ASP.NET Core 앱 게시</span><span class="sxs-lookup"><span data-stu-id="854d5-103">Publish an ASP.NET Core :::no-loc(SignalR)::: app to Azure App Service</span></span>

<span data-ttu-id="854d5-104">[Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="854d5-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="854d5-105">[Azure App Service](/azure/app-service/app-service-web-overview) 은 ASP.NET Core를 포함 하 여 웹 앱을 호스팅하기 위한 [Microsoft 클라우드 컴퓨팅](https://azure.microsoft.com/) 플랫폼 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="854d5-106">이 문서에서는 Visual Studio에서 ASP.NET Core 앱을 게시 하는 방법을 설명 :::no-loc(SignalR)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-106">This article refers to publishing an ASP.NET Core :::no-loc(SignalR)::: app from Visual Studio.</span></span> <span data-ttu-id="854d5-107">자세한 내용은 [ :::no-loc(SignalR)::: Azure에 대 한 서비스](https://azure.microsoft.com/services/signalr-service)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="854d5-107">For more information, see [:::no-loc(SignalR)::: service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="854d5-108">앱 게시</span><span class="sxs-lookup"><span data-stu-id="854d5-108">Publish the app</span></span>

<span data-ttu-id="854d5-109">이 문서에서는 Visual Studio의 도구를 사용 하 여 게시 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="854d5-110">사용자 Visual Studio Code [Azure CLI](/cli/azure) 명령을 사용 하 여 Azure에 앱을 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="854d5-111">자세한 내용은 [명령줄 도구를 사용 하 여 Azure에 ASP.NET Core 앱 게시](/azure/app-service/app-service-web-get-started-dotnet)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="854d5-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="854d5-112">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-112">Right-click on the project in **Solution Explorer** and select **Publish** .</span></span>

1. <span data-ttu-id="854d5-113">**게시 대상 선택** 대화 상자에서 **App Service** 및 **새로 만들기** 가 선택 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="854d5-114">**게시** 단추 드롭다운에서 **프로필 만들기** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="854d5-115">**App Service 만들기** 대화 상자에서 다음 표에 설명 된 정보를 입력 하 고 **만들기** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create** .</span></span>

   | <span data-ttu-id="854d5-116">항목</span><span class="sxs-lookup"><span data-stu-id="854d5-116">Item</span></span>               | <span data-ttu-id="854d5-117">Description</span><span class="sxs-lookup"><span data-stu-id="854d5-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="854d5-118">**이름**</span><span class="sxs-lookup"><span data-stu-id="854d5-118">**Name**</span></span>           | <span data-ttu-id="854d5-119">앱의 고유한 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="854d5-120">**구독**</span><span class="sxs-lookup"><span data-stu-id="854d5-120">**Subscription**</span></span>   | <span data-ttu-id="854d5-121">앱에서 사용 하는 Azure 구독입니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="854d5-122">**리소스 그룹**</span><span class="sxs-lookup"><span data-stu-id="854d5-122">**Resource Group**</span></span> | <span data-ttu-id="854d5-123">앱이 속한 관련 리소스 그룹입니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="854d5-124">**호스팅 계획**</span><span class="sxs-lookup"><span data-stu-id="854d5-124">**Hosting Plan**</span></span>   | <span data-ttu-id="854d5-125">웹 앱에 대 한 가격 책정 계획입니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="854d5-126">**서비스 종속성** 섹션에서 **Azure :::no-loc(SignalR)::: 서비스** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-126">Select **Azure :::no-loc(SignalR)::: Service** in the **Service Dependencies** section.</span></span> <span data-ttu-id="854d5-127">단추를 선택 합니다 **+** .</span><span class="sxs-lookup"><span data-stu-id="854d5-127">Select the **+** button:</span></span>

   ![추가 드롭다운 목록에서 Azure::: no loc (SignalR)::: Service 선택 항목을 표시 하는 종속성 영역](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="854d5-129">**Azure :::no-loc(SignalR)::: 서비스** 대화 상자에서 **새 azure :::no-loc(SignalR)::: 서비스 인스턴스 만들기** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-129">In the **Azure :::no-loc(SignalR)::: Service** dialog, select **Create a new Azure :::no-loc(SignalR)::: Service instance** .</span></span>

1. <span data-ttu-id="854d5-130">**이름** , **리소스 그룹** 및 **위치** 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-130">Provide a **Name** , **Resource Group** , and **Location** .</span></span> <span data-ttu-id="854d5-131">**Azure :::no-loc(SignalR)::: 서비스** 대화 상자로 돌아가서 **추가** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-131">Return to the **Azure :::no-loc(SignalR)::: Service** dialog and select **Add** .</span></span>

<span data-ttu-id="854d5-132">Visual Studio에서는 다음 작업을 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-132">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="854d5-133">게시 설정을 포함 하는 게시 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-133">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="854d5-134">제공 된 세부 정보를 사용 하 여 *Azure 웹 앱* 을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-134">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="854d5-135">앱을 게시 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-135">Publishes the app.</span></span>
* <span data-ttu-id="854d5-136">웹 앱을 로드 하는 브라우저를 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-136">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="854d5-137">앱 URL의 형식은 `{APP SERVICE NAME}.azurewebsites.net` 입니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-137">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="854d5-138">예를 들어 라는 앱에는 `:::no-loc(SignalR):::ChatApp` 의 URL이 `https://signalrchatapp.azurewebsites.net` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-138">For example, an app named `:::no-loc(SignalR):::ChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="854d5-139">Preview .NET Core 릴리스를 대상으로 하는 앱을 배포할 때 HTTP *502.2-잘못 된 게이트웨이* 오류가 발생 하는 경우이를 해결 하려면 [Azure App Service에 ASP.NET Core preview 릴리스 배포](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="854d5-139">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="854d5-140">Azure App Service에서 앱 구성</span><span class="sxs-lookup"><span data-stu-id="854d5-140">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="854d5-141">*이 섹션은 Azure 서비스를 사용 하지 않는 앱에만 적용 됩니다 :::no-loc(SignalR)::: .*</span><span class="sxs-lookup"><span data-stu-id="854d5-141">*This section only applies to apps not using the Azure :::no-loc(SignalR)::: Service.*</span></span>
>
> <span data-ttu-id="854d5-142">앱에서 Azure App Service 서비스를 사용 하는 경우 :::no-loc(SignalR)::: 이 섹션에서 설명 하는 ARR (응용 프로그램 요청 라우팅) 선호도 및 웹 소켓을 구성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-142">If the app uses the Azure :::no-loc(SignalR)::: Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="854d5-143">클라이언트는 :::no-loc(SignalR)::: 앱에 직접 연결 하는 것이 아니라 Azure 서비스에 웹 소켓을 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-143">Clients connect their Web Sockets to the Azure :::no-loc(SignalR)::: Service, not directly to the app.</span></span>

<span data-ttu-id="854d5-144">Azure 서비스 없이 호스트 되는 앱의 :::no-loc(SignalR)::: 경우 다음을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-144">For apps hosted without the Azure :::no-loc(SignalR)::: Service, enable:</span></span>

* <span data-ttu-id="854d5-145">[ARR 선호도] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- :::no-loc(cookie)::: -(ARR- :::no-loc(cookie)::: ) -for-Azure-web-apps.html)-사용자의 요청을 다시 동일한 App Service 인스턴스로 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-145">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-:::no-loc(cookie):::-(ARR-:::no-loc(cookie):::)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="854d5-146">기본 설정은 **On** 입니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-146">The default setting is **On** .</span></span>
* <span data-ttu-id="854d5-147">웹 [소켓은](xref:fundamentals/websockets) 웹 소켓 전송이 작동할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-147">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="854d5-148">기본 설정은 **Off** 입니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-148">The default setting is **Off** .</span></span>

1. <span data-ttu-id="854d5-149">Azure Portal에서 **App Services** 웹 앱으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-149">In the Azure portal, navigate to the web app in **App Services** .</span></span>
1. <span data-ttu-id="854d5-150">**구성**  >  **일반 설정** 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-150">Open **Configuration** > **General settings** .</span></span>
1. <span data-ttu-id="854d5-151">**웹 소켓** 을 **켜기** 로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-151">Set **Web sockets** to **On** .</span></span>
1. <span data-ttu-id="854d5-152">**ARR 선호도** 가 **On** 으로 설정 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-152">Verify that **ARR affinity** is set to **On** .</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="854d5-153">App Service 계획 제한</span><span class="sxs-lookup"><span data-stu-id="854d5-153">App Service Plan limits</span></span>

<span data-ttu-id="854d5-154">웹 소켓 및 기타 전송은 선택한 App Service 계획을 기준으로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="854d5-154">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="854d5-155">자세한 내용은 azure [구독 및 서비스 제한, 할당량 및 제약 조건](/azure/azure-subscription-service-limits#app-service-limits) 문서의 *azure Cloud Services 제한* 및 *App Service 제한* 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="854d5-155">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="854d5-156">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="854d5-156">Additional resources</span></span>

* [<span data-ttu-id="854d5-157">Azure :::no-loc(SignalR)::: Service란?</span><span class="sxs-lookup"><span data-stu-id="854d5-157">What is Azure :::no-loc(SignalR)::: Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="854d5-158">명령줄 도구를 사용하여 Azure에 ASP.NET Core 앱 게시</span><span class="sxs-lookup"><span data-stu-id="854d5-158">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="854d5-159">Azure에서 ASP.NET Core Preview 앱 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="854d5-159">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
