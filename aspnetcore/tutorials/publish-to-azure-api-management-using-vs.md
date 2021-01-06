---
title: Visual Studio를 사용하여 Azure API Management에 ASP.NET Core 웹 API 게시
author: codemillmatt
description: Visual Studio를 사용하여 Azure API Management에 ASP.NET Core 웹 API를 게시하는 방법을 알아봅니다.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 395b5981a3018486235c38f032893f985ab71383
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96332229"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="62778-103">Visual Studio를 사용하여 Azure API Management에 ASP.NET Core 웹 API 게시</span><span class="sxs-lookup"><span data-stu-id="62778-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="62778-104">작성자: [Matt Soucoup](https://twitter.com/codemillmatt)</span><span class="sxs-lookup"><span data-stu-id="62778-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="62778-105">설치</span><span class="sxs-lookup"><span data-stu-id="62778-105">Set up</span></span>

- <span data-ttu-id="62778-106">계정이 없는 경우 [체험 Azure 계정](https://azure.microsoft.com/free/dotnet/)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="62778-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="62778-107">아직 만들지 않은 경우 [새 Azure API Management 인스턴스를 만듭니다](/azure/api-management/get-started-create-service-instance).</span><span class="sxs-lookup"><span data-stu-id="62778-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="62778-108">[웹 API 앱 프로젝트를 만듭니다](xref:tutorials/first-web-api#create-a-web-project).</span><span class="sxs-lookup"><span data-stu-id="62778-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="62778-109">앱 구성</span><span class="sxs-lookup"><span data-stu-id="62778-109">Configure the app</span></span>

<span data-ttu-id="62778-110">ASP.NET Core 웹 API에 Swagger 정의를 추가하면 Azure API Management에서 앱의 API 정의를 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62778-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="62778-111">다음 단계를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="62778-112">Swagger 추가</span><span class="sxs-lookup"><span data-stu-id="62778-112">Add Swagger</span></span>

1. <span data-ttu-id="62778-113">ASP.NET Core 웹 API 프로젝트에 [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![NuGet 구성 대화 상자의 스크린샷](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="62778-115">다음 줄을 `Startup.ConfigureServices` 메서드에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="62778-116">다음 줄을 `Startup.Configure` 메서드에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="62778-117">API 라우팅 변경</span><span class="sxs-lookup"><span data-stu-id="62778-117">Change the API routing</span></span>

<span data-ttu-id="62778-118">다음으로, `WeatherForecastController`의 `Get` 작업에 액세스하는 데 필요한 URL 구조를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="62778-119">다음 단계를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-119">Complete the following steps:</span></span>

1. <span data-ttu-id="62778-120">*WeatherForecastController.cs* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="62778-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="62778-121">`[Route("[controller]")]` 클래스 수준 특성을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="62778-122">클래스 정의는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="62778-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="62778-123">`Get()` 작업에 `[Route("/")]` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="62778-124">함수 정의는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="62778-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="62778-125">Azure App Service에 웹 API 게시</span><span class="sxs-lookup"><span data-stu-id="62778-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="62778-126">다음 단계를 완료하여 Azure API Management에 ASP.NET Core 웹 API를 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="62778-127">Azure App Service에 API 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="62778-128">Azure API Management 서비스 인스턴스에 빈 API를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="62778-129">Azure API Management 서비스 인스턴스에 ASP.NET Core 웹 API 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="62778-130">Azure App Service에 API 앱 게시</span><span class="sxs-lookup"><span data-stu-id="62778-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="62778-131">다음 단계를 완료하여 Azure API Management에 ASP.NET Core 웹 API를 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="62778-132">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="62778-134">**게시** 대화 상자에서 **Azure** 를 선택하고 **다음** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![게시 대화 상자](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="62778-136">**Azure App Service(Windows)** 를 선택하고 **다음** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![게시 대화 상자: App Service 선택](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="62778-138">**새 Azure App Service 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-138">Select **Create a new Azure App Service**.</span></span>

    ![게시 대화 상자: Azure Service 인스턴스 선택](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="62778-140">**App Service 만들기** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="62778-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="62778-141">**앱 이름**, **리소스 그룹** 및 **App Service 계획** 항목 필드가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="62778-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="62778-142">이러한 이름을 유지하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62778-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="62778-143">**만들기** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-143">Select the **Create** button.</span></span>

    ![App Service 만들기 대화 상자](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="62778-145">만들기가 완료되면 대화 상자가 자동으로 닫히고 **게시** 대화 상자가 다시 포커스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="62778-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="62778-146">만든 인스턴스가 자동으로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="62778-146">The instance that was created is automatically selected.</span></span>

![게시 대화 상자: App Service 인스턴스 선택](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="62778-148">이 시점에서 Azure API Management 서비스에 API를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="62778-149">다음 작업을 완료하는 동안 Visual Studio를 열어 두세요.</span><span class="sxs-lookup"><span data-stu-id="62778-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="62778-150">Azure API Management에 API 추가</span><span class="sxs-lookup"><span data-stu-id="62778-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="62778-151">이전에 Azure Portal에서 만든 API Management 서비스 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="62778-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="62778-152">**API** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-152">Select the **APIs** blade:</span></span>

  ![API Management 서비스 인스턴스에서 선택한 API 블레이드](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="62778-154">**Echo API** 옆의 3개 점을 선택하고 팝업 메뉴에서 **삭제** 를 선택하여 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-154">Select the 3 dots next to **Echo API** and then select **Delete** from the pop-up menu to remove it.</span></span>

  ![API Management 서비스 인스턴스에서 Echo API 삭제](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. <span data-ttu-id="62778-156">**새 API 추가** 패널에서 **빈 API** 타일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-156">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

  ![빈 API 타일이 강조 표시된 화면](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="62778-158">**빈 API 만들기** 대화 상자가 나타나면 다음 값을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-158">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="62778-159">**표시 이름**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="62778-159">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="62778-160">**이름**: *weatherforecasts*</span><span class="sxs-lookup"><span data-stu-id="62778-160">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="62778-161">**API URL 접미사**: *v1*</span><span class="sxs-lookup"><span data-stu-id="62778-161">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="62778-162">**웹 서비스 URL** 필드는 비워 둡니다.</span><span class="sxs-lookup"><span data-stu-id="62778-162">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="62778-163">**만들기** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-163">Select the **Create** button.</span></span>

    ![빈 API 만들기 대화 상자 완료 스크린샷](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="62778-165">빈 API가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="62778-165">The blank API is created.</span></span>

![API Management 포털의 빈 API 스크린샷](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="62778-167">Azure API Management에 ASP.NET Core 웹 API 게시</span><span class="sxs-lookup"><span data-stu-id="62778-167">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="62778-168">Visual Studio로 다시 전환합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-168">Switch back to Visual Studio.</span></span> <span data-ttu-id="62778-169">**게시** 대화 상자는 이전에 중단한 위치에서 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62778-169">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="62778-170">새로 게시된 Azure App Service를 선택하여 강조 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-170">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="62778-171">**다음** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-171">Select the **Next** button.</span></span>

    ![게시 대화 상자에서 App Service를 선택한 스크린샷](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="62778-173">이전에 만든 Azure API Management 서비스가 이제 대화 상자에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="62778-173">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="62778-174">해당 서비스와 *API* 폴더를 확장하여 만든 빈 API를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-174">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="62778-175">빈 API의 이름을 선택하고 **마침** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-175">Select the blank API's name and select the **Finish** button.</span></span>

    ![게시 대화 상자에서 새로 만든 Azure API Management 빈 API를 선택한 스크린샷](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="62778-177">대화 상자가 닫히고 게시에 대한 정보가 포함된 요약 화면이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="62778-177">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="62778-178">**게시** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-178">Select the **Publish** button.</span></span>

    ![게시 프로필이 표시된 Visual Studio 스크린샷](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="62778-180">웹 API는 Azure App Service와 Azure API Management에 모두 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="62778-180">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="62778-181">새 브라우저 창이 나타나고 Azure App Service에서 실행되는 API가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="62778-181">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="62778-182">해당 창을 닫을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62778-182">You can close that window.</span></span>

1. <span data-ttu-id="62778-183">Azure Portal에서 Azure API Management 인스턴스로 다시 전환합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-183">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="62778-184">브라우저 창을 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="62778-184">Refresh the browser window.</span></span>
1. <span data-ttu-id="62778-185">이전 단계에서 만든 빈 API를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-185">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="62778-186">이제 채워져 있으며, 해당 내용을 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62778-186">It's now populated and you can explore around.</span></span>

    ![Azure API Management의 채워진 API 스크린샷](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="62778-188">게시된 API 이름 구성</span><span class="sxs-lookup"><span data-stu-id="62778-188">Configure the published API name</span></span>

<span data-ttu-id="62778-189">API의 이름이 명명한 것과 다르다는 것을 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62778-189">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="62778-190">게시된 API의 이름은 *WeatherAPI* 이지만, 만들 때 지정한 이름은 *WeatherForecasts* 입니다.</span><span class="sxs-lookup"><span data-stu-id="62778-190">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="62778-191">이름을 수정하려면 다음 단계를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-191">Complete the following steps to fix the name:</span></span>

![채워진 API에서 다른 이름이 강조된 스크린샷](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="62778-193">`Startup.ConfigureServices` 메서드에서 다음 줄을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-193">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="62778-194">`Startup.ConfigureServices` 메서드에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-194">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("WeatherForecasts", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "Weather Forecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="62778-195">새로 만든 게시 프로필을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="62778-195">Open the newly created publish profile.</span></span> <span data-ttu-id="62778-196">**솔루션 탐색기** 에서 *Properties/PublishProfiles* 폴더를 찾으면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="62778-196">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![게시 프로필 파일 위치를 강조하여 보여 주는 스크린샷](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="62778-198">`<OpenAPIDocumentName>` 요소의 값을 `v1`에서 `WeatherForecasts`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-198">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![게시 프로필에 필요한 변경 사항 스크린샷](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="62778-200">Azure Portal에서 ASP.NET Core 웹 API를 다시 게시하고 Azure API Management 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="62778-200">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="62778-201">브라우저에서 페이지를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="62778-201">Refresh the page in your browser.</span></span> <span data-ttu-id="62778-202">이제 표시되는 API 이름이 올바릅니다.</span><span class="sxs-lookup"><span data-stu-id="62778-202">You'll see the name of the API is now correct.</span></span>

    ![포털에서 완료된 API의 스크린샷](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="62778-204">웹 API가 작동하는지 확인</span><span class="sxs-lookup"><span data-stu-id="62778-204">Verify the web API is working</span></span>

<span data-ttu-id="62778-205">Azure Portal에서 다음 단계에 따라 Azure API Management에 배포된 ASP.NET Core 웹 API를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62778-205">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="62778-206">**테스트** 탭을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="62778-206">Open the **Test** tab.</span></span>
1. <span data-ttu-id="62778-207">**/** 또는 **Get** 작업을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-207">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="62778-208">**보내기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-208">Select **Send**.</span></span>

    ![테스트 전의 포털 스크린샷](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="62778-210">성공적인 응답은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="62778-210">A successful response will look like the following:</span></span>

![API Management의 성공적인 응답 스크린샷](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="62778-212">정리</span><span class="sxs-lookup"><span data-stu-id="62778-212">Clean up</span></span>

<span data-ttu-id="62778-213">앱 테스트를 완료하면 [Azure Portal](https://portal.azure.com/)로 이동하고 앱을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-213">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="62778-214">**리소스 그룹** 을 선택한 다음 만든 리소스 그룹을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-214">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Azure Portal: 사이드바 메뉴에 있는 리소스 그룹](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="62778-216">**리소스 그룹** 페이지에서 **삭제** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-216">In the **Resource groups** page, select **Delete**.</span></span>

    ![Azure Portal: 리소스 그룹 페이지](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="62778-218">리소스 그룹의 이름을 입력하고 **삭제** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="62778-218">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="62778-219">이 자습서에서 만든 앱과 다른 모든 리소스는 이제 Azure에서 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="62778-219">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="62778-220">다음 단계</span><span class="sxs-lookup"><span data-stu-id="62778-220">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="62778-221">추가 자료</span><span class="sxs-lookup"><span data-stu-id="62778-221">Additional resources</span></span>

- [<span data-ttu-id="62778-222">Azure API Management</span><span class="sxs-lookup"><span data-stu-id="62778-222">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="62778-223">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="62778-223">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
