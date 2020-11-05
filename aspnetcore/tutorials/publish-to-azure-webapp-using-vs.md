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
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060224"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Visual Studio를 사용하여 Azure에 ASP.NET Core 앱 게시

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


macOS에서 작업하는 경우 [Mac용 Visual Studio를 사용하여 Azure App Service에 웹 앱 게시](/visualstudio/mac/publish-app-svc?view=vsmac-2019)를 참조하세요.

App Service 배포 문제를 해결하려면 <xref:test/troubleshoot-azure-iis>을 참조하세요.

## <a name="set-up"></a>설치

* 계정이 없는 경우 [체험 Azure 계정](https://azure.microsoft.com/free/dotnet/)을 엽니다. 

## <a name="create-a-web-app"></a>웹앱 만들기

Visual Studio 시작 페이지에서 **파일 > 새로 만들기 > 프로젝트...** 를 선택합니다.

![파일 메뉴](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

**새 프로젝트** 대화 상자를 완료합니다.

* **새 ASP.NET Core 웹 애플리케이션** 을 선택합니다.
* **새로 만들기** 를 선택합니다.

![새 프로젝트 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj.png)

**새 ASP.NET Core 웹 애플리케이션** 대화 상자에서:

* **웹 애플리케이션** 을 선택합니다.
* 인증에서 **변경** 을 선택합니다.

![새 ASP.NET Core 웹 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

**인증 변경** 대화 상자가 나타납니다. 

* **개별 사용자 계정** 을 선택합니다.
* **확인** 을 선택하여 **새 ASP.NET Core 웹 애플리케이션** 으로 돌아간 다음 **만들기** 를 선택합니다.

![새 ASP.NET Core 웹 인증 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio는 솔루션을 만듭니다.

## <a name="run-the-app"></a>앱 실행

* Ctrl+F5를 눌러 프로젝트를 실행합니다.
* **개인 정보** 링크를 테스트합니다.

![localhost의 Microsoft Edge에서 열린 웹 애플리케이션](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>사용자 등록

* **등록** 을 선택하고 새 사용자를 등록합니다. 가상의 전자 메일 주소를 사용할 수 있습니다. 제출하면 페이지에 다음과 같은 오류가 표시됩니다.

    *"요청을 처리하는 동안 데이터베이스 작업이 실패했습니다. 애플리케이션 DB 컨텍스트에 대한 기존 마이그레이션 적용으로 이 문제를 해결할 수 있습니다.”*
* **마이그레이션 적용** 을 선택한 다음 페이지가 업데이트되면 페이지를 새로 고칩니다.

![요청을 처리하는 동안 데이터베이스 작업이 실패했습니다. 애플리케이션 DB 컨텍스트에 대한 기존 마이그레이션 적용으로 이 문제를 해결할 수 있습니다.](publish-to-azure-webapp-using-vs/_static/mig.png)

앱은 새 사용자를 등록하는 데 사용한 이메일 및 **로그아웃** 링크를 표시합니다.

![Microsoft Edge에서 열린 웹 애플리케이션. 레지스터 링크는 텍스트 Hello user1@example.com으로 교체됩니다.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Azure에 앱 배포

솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택합니다.

![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-webapp-using-vs/_static/pub.png)

**게시** 대화 상자에서:

* **Azure** 를 선택합니다.
* **새로 만들기** 를 선택합니다.

![게시 대화 상자](publish-to-azure-webapp-using-vs/_static/maas1.png)

**게시** 대화 상자에서:

* **Azure App Service(Linux)** 를 선택합니다.
* **새로 만들기** 를 선택합니다.

![게시 대화 상자: Azure Service 선택](publish-to-azure-webapp-using-vs/_static/maas2.png)

**게시** 대화 상자에서 **새 Azure App Service 만들기...** 를 선택합니다.

![게시 대화 상자: Azure Service 인스턴스 선택](publish-to-azure-webapp-using-vs/_static/maas3.png)

**App Service 만들기** 대화 상자가 나타납니다.

* **앱 이름** , **리소스 그룹** 및 **App Service 계획** 항목 필드가 채워집니다. 이러한 이름을 유지하거나 변경할 수 있습니다.
* **만들기** 를 선택합니다.

![App Service 만들기 대화 상자](publish-to-azure-webapp-using-vs/_static/newrg1.png)

만들기가 완료되면 대화 상자가 자동으로 닫히고 **게시** 대화 상자가 다시 포커스를 받습니다.

* 방금 만든 새 인스턴스가 자동으로 선택됩니다.
* **마침** 을 선택합니다.

![게시 대화 상자: App Service 인스턴스 선택](publish-to-azure-webapp-using-vs/_static/select_as.png)

다음으로 **게시 프로필 요약** 페이지가 표시됩니다. 이 애플리케이션에 SQL Server 데이터베이스가 필요하므로 이를 구성하라는 메시지가 표시됩니다. **구성** 을 선택합니다.

![게시 프로필 요약 페이지: SQL Server 종속성 구성](publish-to-azure-webapp-using-vs/_static/sql.png)

**종속성 구성** 대화 상자가 나타납니다.

* **Azure SQL Database** 를 선택합니다.
* **새로 만들기** 를 선택합니다.

![SQL Server 종속성 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/sql1.png)

**Azure SQL Database 구성** 대화 상자에서 **SQL 데이터베이스 만들기** 를 선택합니다.

![Azure SQL Database 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/sql2.png)

**Azure SQL 데이터베이스 만들기** 가 표시됩니다.

* **데이터베이스 이름** , **리소스 그룹** , **데이터베이스 서버** 및 **App Service 계획** 항목 필드가 채워집니다. 채워진 값을 유지하거나 변경할 수 있습니다.
* 선택한 **데이터베이스 서버** 에 대한 **데이터베이스 관리자 사용자 이름** 및 **데이터베이스 관리자 암호** 를 입력합니다. 사용하는 계정에 새 Azure SQL 데이터베이스를 만드는 데 필요한 권한이 있어야 합니다.
* **만들기** 를 선택합니다.

![새 Azure SQL Database 대화 상자](publish-to-azure-webapp-using-vs/_static/sql_create.png)

만들기가 완료되면 대화 상자가 자동으로 닫히고 **Azure SQL Database 구성** 대화 상자가 다시 표시됩니다.

* 방금 만든 새 인스턴스가 자동으로 선택됩니다.
* **새로 만들기** 를 선택합니다.

![Azure SQL Database 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/sql_select.png)

**Azure SQL Database 구성** 대화 상자의 다음 단계에서 다음 작업을 수행합니다.

* **데이터베이스 연결 사용자 이름** 및 **데이터베이스 연결 암호** 필드를 입력합니다. 이들은 애플리케이션이 런타임 시 데이터베이스에 연결하는 데 사용하는 세부 정보입니다. 이전 단계에서 사용한 관리자 사용자 이름 및 암호와 동일한 세부 정보를 사용하지 않는 것이 가장 좋습니다.
* **마침** 을 선택합니다.

![Azure SQL Database 구성 대화 상자, 연결 문자열 세부 정보](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

**게시 프로필 요약** 페이지에서 **설정** 을 선택합니다.

![게시 프로필 요약 페이지: 설정 편집](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

**게시** 대화 상자의 **설정** 페이지에서:

* **데이터베이스** 를 확장하고 **런타임 시 이 연결 문자열 사용** 을 선택합니다.
* **Entity Framework 마이그레이션** 을 확장하고 **게시에 이 마이그레이션 적용** 을 선택합니다.

* **저장** 을 선택합니다. Visual Studio가 **게시** 대화 상자로 돌아갑니다. 

![게시 대화 상자: 설정 패널](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

**게시** 를 클릭합니다. Visual Studio는 Azure에 앱을 게시합니다. 배포가 완료되면 앱이 브라우저에서 열립니다.

![게시 대화 상자: 설정 패널](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a>앱 업데이트

* *Pages/Index.cshtml* Razor 페이지를 편집하고 내용을 변경합니다. 예를 들어 단락을 수정하여 “Hello ASP.NET Core!” 문구를 표시할 수 있습니다.

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* **게시 프로필 요약** 페이지에서 다시 **게시** 를 선택합니다.

![게시 프로필 요약 페이지](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* 앱이 게시된 후 변경 내용이 Azure에서 제공되는지 확인합니다.

![작업이 완료되었는지 확인합니다.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>정리

앱 테스트를 완료하면 [Azure Portal](https://portal.azure.com/)로 이동하고 앱을 삭제합니다.

* **리소스 그룹** 을 선택한 다음 만든 리소스 그룹을 선택합니다.

![Azure Portal: 사이드바 메뉴에 있는 리소스 그룹](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* **리소스 그룹** 페이지에서 **삭제** 를 선택합니다.

![Azure Portal: 리소스 그룹 페이지](publish-to-azure-webapp-using-vs/_static/rgd.png)

* 리소스 그룹의 이름을 입력하고 **삭제** 를 선택합니다. 이 자습서에서 만든 앱과 다른 모든 리소스는 이제 Azure에서 삭제됩니다.

### <a name="next-steps"></a>다음 단계

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>추가 자료

* Visual Studio Code는 [게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)을 참조하세요.
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Azure 리소스 그룹](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>