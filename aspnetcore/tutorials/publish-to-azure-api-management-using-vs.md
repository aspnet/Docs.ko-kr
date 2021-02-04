---
title: Visual Studio를 사용하여 Azure API Management에 ASP.NET Core 웹 API 게시
author: codemillmatt
description: Visual Studio를 사용하여 Azure API Management에 ASP.NET Core 웹 API를 게시하는 방법을 알아봅니다.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: ddff54bbd146c98cf83a865910401df26e7ac4ec
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217585"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a>Visual Studio를 사용하여 Azure API Management에 ASP.NET Core 웹 API 게시

작성자: [Matt Soucoup](https://twitter.com/codemillmatt)

## <a name="set-up"></a>설치

- 계정이 없는 경우 [체험 Azure 계정](https://azure.microsoft.com/free/dotnet/)을 엽니다.
- 아직 만들지 않은 경우 [새 Azure API Management 인스턴스를 만듭니다](/azure/api-management/get-started-create-service-instance).
- [웹 API 앱 프로젝트를 만듭니다](xref:tutorials/first-web-api#create-a-web-project).

## <a name="configure-the-app"></a>앱 구성

ASP.NET Core 웹 API에 Swagger 정의를 추가하면 Azure API Management에서 앱의 API 정의를 읽을 수 있습니다. 다음 단계를 완료합니다.

### <a name="add-swagger"></a>Swagger 추가

1. ASP.NET Core 웹 API 프로젝트에 [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet 패키지를 추가합니다.

    ![NuGet 구성 대화 상자의 스크린샷](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. 다음 줄을 `Startup.ConfigureServices` 메서드에 추가합니다.
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. 다음 줄을 `Startup.Configure` 메서드에 추가합니다.

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a>API 라우팅 변경

다음으로, `WeatherForecastController`의 `Get` 작업에 액세스하는 데 필요한 URL 구조를 변경합니다. 다음 단계를 완료합니다.

1. *WeatherForecastController.cs* 파일을 엽니다.
1. `[Route("[controller]")]` 클래스 수준 특성을 삭제합니다. 클래스 정의는 다음과 같습니다.

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. `Get()` 작업에 `[Route("/")]` 특성을 추가합니다. 함수 정의는 다음과 같습니다.

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a>Azure App Service에 웹 API 게시

다음 단계를 완료하여 Azure API Management에 ASP.NET Core 웹 API를 게시합니다.

1. Azure App Service에 API 앱을 게시합니다.
1. Azure API Management 서비스 인스턴스에 빈 API를 추가합니다.
1. Azure API Management 서비스 인스턴스에 ASP.NET Core 웹 API 앱을 게시합니다.

### <a name="publish-the-api-app-to-azure-app-service"></a>Azure App Service에 API 앱 게시

다음 단계를 완료하여 Azure API Management에 ASP.NET Core 웹 API를 게시합니다.

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.

    ![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. **게시** 대화 상자에서 **Azure** 를 선택하고 **다음** 단추를 선택합니다.

    ![게시 대화 상자](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. **Azure App Service(Windows)** 를 선택하고 **다음** 단추를 선택합니다.

    ![게시 대화 상자: App Service 선택](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. **새 Azure App Service 만들기** 를 선택합니다.

    ![게시 대화 상자: Azure Service 인스턴스 선택](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    **App Service 만들기** 대화 상자가 나타납니다. **앱 이름**, **리소스 그룹** 및 **App Service 계획** 항목 필드가 채워집니다. 이러한 이름을 유지하거나 변경할 수 있습니다.
1. **만들기** 단추를 선택합니다.

    ![App Service 만들기 대화 상자](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

만들기가 완료되면 대화 상자가 자동으로 닫히고 **게시** 대화 상자가 다시 포커스를 받습니다. 만든 인스턴스가 자동으로 선택됩니다.

![게시 대화 상자: App Service 인스턴스 선택](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

이 시점에서 Azure API Management 서비스에 API를 추가해야 합니다. 다음 작업을 완료하는 동안 Visual Studio를 열어 두세요.

### <a name="add-an-api-to-azure-api-management"></a>Azure API Management에 API 추가

1. 이전에 Azure Portal에서 만든 API Management 서비스 인스턴스를 엽니다. **API** 블레이드를 선택합니다.

  ![API Management 서비스 인스턴스에서 선택한 API 블레이드](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. **Echo API** 옆의 3개 점을 선택하고 팝업 메뉴에서 **삭제** 를 선택하여 제거합니다.

  ![API Management 서비스 인스턴스에서 Echo API 삭제](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. **새 API 추가** 패널에서 **빈 API** 타일을 선택합니다.

  ![빈 API 타일이 강조 표시된 화면](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. **빈 API 만들기** 대화 상자가 나타나면 다음 값을 입력합니다.    

    - **표시 이름**: *WeatherForecasts*
    - **이름**: *weatherforecasts*
    - **API URL 접미사**: *v1*
    - **웹 서비스 URL** 필드는 비워 둡니다.

1. **만들기** 단추를 선택합니다.

    ![빈 API 만들기 대화 상자 완료 스크린샷](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

빈 API가 만들어집니다.

![API Management 포털의 빈 API 스크린샷](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a>Azure API Management에 ASP.NET Core 웹 API 게시

1. Visual Studio로 다시 전환합니다. **게시** 대화 상자는 이전에 중단한 위치에서 계속 열려 있습니다.
1. 새로 게시된 Azure App Service를 선택하여 강조 표시합니다.
1. **다음** 단추를 선택합니다.

    ![게시 대화 상자에서 App Service를 선택한 스크린샷](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. 이전에 만든 Azure API Management 서비스가 이제 대화 상자에 표시됩니다. 해당 서비스와 *API* 폴더를 확장하여 만든 빈 API를 표시합니다.
1. 빈 API의 이름을 선택하고 **마침** 단추를 선택합니다.

    ![게시 대화 상자에서 새로 만든 Azure API Management 빈 API를 선택한 스크린샷](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. 대화 상자가 닫히고 게시에 대한 정보가 포함된 요약 화면이 나타납니다. **게시** 단추를 선택합니다.

    ![게시 프로필이 표시된 Visual Studio 스크린샷](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    웹 API는 Azure App Service와 Azure API Management에 모두 게시됩니다. 새 브라우저 창이 나타나고 Azure App Service에서 실행되는 API가 표시됩니다. 해당 창을 닫을 수 있습니다.

1. Azure Portal에서 Azure API Management 인스턴스로 다시 전환합니다.
1. 브라우저 창을 새로 고칩니다.
1. 이전 단계에서 만든 빈 API를 선택합니다. 이제 채워져 있으며, 해당 내용을 살펴볼 수 있습니다.

    ![Azure API Management의 채워진 API 스크린샷](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a>게시된 API 이름 구성

API의 이름이 명명한 것과 다르다는 것을 알 수 있습니다. 게시된 API의 이름은 *WeatherAPI* 이지만, 만들 때 지정한 이름은 *WeatherForecasts* 입니다. 이름을 수정하려면 다음 단계를 완료합니다.

![채워진 API에서 다른 이름이 강조된 스크린샷](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. `Startup.ConfigureServices` 메서드에서 다음 줄을 삭제합니다.
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. `Startup.ConfigureServices` 메서드에 다음 코드를 추가합니다.
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("v1", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "WeatherForecasts",
            Version = "v1"
        });
    });
    ```

1. 새로 만든 게시 프로필을 엽니다. **솔루션 탐색기** 에서 *Properties/PublishProfiles* 폴더를 찾으면 됩니다.

    ![게시 프로필 파일 위치를 강조하여 보여 주는 스크린샷](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. `<OpenAPIDocumentName>` 요소의 값을 `v1`에서 `WeatherForecasts`로 변경합니다.

    ![게시 프로필에 필요한 변경 사항 스크린샷](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. Azure Portal에서 ASP.NET Core 웹 API를 다시 게시하고 Azure API Management 인스턴스를 엽니다.
1. 브라우저에서 페이지를 새로 고칩니다. 이제 표시되는 API 이름이 올바릅니다.

    ![포털에서 완료된 API의 스크린샷](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a>웹 API가 작동하는지 확인

Azure Portal에서 다음 단계에 따라 Azure API Management에 배포된 ASP.NET Core 웹 API를 테스트할 수 있습니다.

1. **테스트** 탭을 엽니다.
1. **/** 또는 **Get** 작업을 선택합니다.
1. **보내기** 를 선택합니다.

    ![테스트 전의 포털 스크린샷](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

성공적인 응답은 다음과 같습니다.

![API Management의 성공적인 응답 스크린샷](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a>정리

앱 테스트를 완료하면 [Azure Portal](https://portal.azure.com/)로 이동하고 앱을 삭제합니다.

1. **리소스 그룹** 을 선택한 다음 만든 리소스 그룹을 선택합니다.

    ![Azure Portal: 사이드바 메뉴에 있는 리소스 그룹](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. **리소스 그룹** 페이지에서 **삭제** 를 선택합니다.

    ![Azure Portal: 리소스 그룹 페이지](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. 리소스 그룹의 이름을 입력하고 **삭제** 를 선택합니다. 이 자습서에서 만든 앱과 다른 모든 리소스는 이제 Azure에서 삭제됩니다.

## <a name="next-steps"></a>다음 단계

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>추가 자료

- [Azure API Management](/azure/api-management/api-management-key-concepts)
- [Azure App Service](/azure/app-service/app-service-web-overview)
