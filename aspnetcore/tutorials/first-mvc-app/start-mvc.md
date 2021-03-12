---
title: ASP.NET Core MVC 시작
author: rick-anderson
description: ASP.NET Core MVC를 시작하는 방법을 알아봅니다.
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8e5f216a354b1ed7559b433d3a4867627bf60df3
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589779"
---
# <a name="get-started-with-aspnet-core-mvc"></a>ASP.NET Core MVC 시작

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

이 자습서는 컨트롤러와 뷰를 사용한 ASP.NET Core MVC 웹 개발을 설명하는 시리즈의 첫 번째 자습서입니다.

시리즈가 끝나면 영화 데이터를 관리하고 표시하는 앱이 생성됩니다. 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 웹앱 만들기
> * 모델 추가 및 스캐폴드
> * 데이터베이스 작업
> * 검색 및 유효성 검사 추가

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a>웹앱 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio를 시작하고 **새 프로젝트 만들기** 를 선택합니다.
* **새 프로젝트 만들기** 대화 상자에서 **ASP.NET Core 웹 애플리케이션** > **다음** 을 선택합니다.
* **새 프로젝트 구성** 대화 상자에서 **프로젝트 이름** 으로 `MvcMovie`를 입력합니다. 프로젝트 이름을 *MvcMovie* 로 지정해야 합니다. 코드를 복사할 때 대문자 표시가 각 `namespace` 일치 항목과 일치해야 합니다.
* **만들기** 를 선택합니다.
* **새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 다음을 선택합니다.
  * 드롭다운에서 **.NET Core** 와 **ASP.NET Core 5.0** 을 선택합니다.
  * **ASP.NET Core 웹앱(Model-View-Controller)** .
  * **Create**.

![새 ASP.NET Core 웹 애플리케이션 만들기 ](start-mvc/_static/mvcVS19v16.9.png)

프로젝트를 만드는 다른 방법은 [Visual Studio에서 새 프로젝트 만들기](/visualstudio/ide/create-new-project)를 참조하세요.

Visual Studio는 생성된 MVC 프로젝트에 기본 프로젝트 템플릿을 사용했습니다. 생성된 프로젝트는 다음과 같습니다.

* 작동하는 앱입니다.
* 기본 시작 프로젝트입니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다. 자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.

* [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.
* 프로젝트가 포함될 디렉터리(`cd`)로 변경합니다.
* 다음 명령 실행:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * **빌드 및 디버그하려는 자산이 'MvcMovie'에 없습니다. 추가할까요?** 라는 대화 상자가 표시되면 **예** 를 선택합니다.

  * `dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.
  * `code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* **파일** > **새 솔루션** 을 선택합니다.

  ![macOS 새 솔루션](start-mvc/_static/new_project_vsmac.png)

* Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다. 버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.

  ![macOS 웹앱 템플릿 선택](start-mvc/_static/web_app_template_vsmac.png)

* **새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.

  * **인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.
  * **대상 프레임워크** 를 선택하는 옵션이 표시되면 최신 5.x 버전을 선택합니다.
  * **새로 만들기** 를 선택합니다.

* 프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.

  ![macOS 프로젝트 이름 지정](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>앱 실행

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ctrl+F5를 선택하여 디버거 없이 앱을 실행합니다.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작합니다.
  * 앱을 실행합니다.

  주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다. 로컬 컴퓨터의 표준 호스트 이름은 `localhost`입니다. Visual Studio가 웹 프로젝트를 만들면 임의의 포트가 웹 서버에 사용됩니다.

Ctrl+F5를 선택하여 디버깅 없이 앱을 시작하면 다음을 수행할 수 있습니다.

* 코드를 변경합니다.
* 파일을 저장합니다.
* 브라우저를 빠르게 새로 고치고 코드 변경 내용을 확인합니다.

**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.

![디버그 메뉴](start-mvc/_static/debug_menu50.png)

**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.

![IIS Express](start-mvc/_static/iis_express50.png)

다음 이미지는 앱을 보여줍니다.

![홈 또는 인덱스 페이지](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ctrl+F5를 선택하여 디버거 없이 실행합니다.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * [Kestrel](xref:fundamentals/servers/kestrel)을 시작합니다.
  * 브라우저를 시작합니다.
  * `https://localhost:5001`로 이동합니다.

  주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다. 로컬 컴퓨터의 표준 호스트 이름은 `localhost`입니다. Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.

Ctrl+F5를 선택하여 디버깅 없이 앱을 시작하면 다음을 수행할 수 있습니다.

* 코드를 변경합니다.
* 파일을 저장합니다.
* 브라우저를 빠르게 새로 고치고 코드 변경 내용을 확인합니다.

  ![홈 또는 인덱스 페이지](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* **실행** > **디버깅하지 않고 시작** 을 선택하여 앱을 시작합니다.

  Mac용 Visual Studio:

  * [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작합니다.
  * 브라우저를 시작합니다.
  * `http://localhost:port`로 이동합니다. 여기서 *port* 는 임의로 선택된 포트 번호입니다.

  [!INCLUDE[](~/includes/trustCertMac.md)]

  주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다. 로컬 컴퓨터의 표준 호스트 이름은 `localhost`입니다. Visual Studio가 웹 프로젝트를 만들면 임의의 포트가 웹 서버에 사용됩니다.

**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.

다음 이미지는 앱을 보여줍니다.

![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.

> [!div class="step-by-step"]
> [다음: 컨트롤러 추가](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

이 자습서는 컨트롤러와 뷰를 사용한 ASP.NET Core MVC 웹 개발을 설명하는 시리즈의 첫 번째 자습서입니다.

시리즈가 끝나면 영화 데이터를 관리하고 표시하는 앱이 생성됩니다. 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 웹앱 만들기
> * 모델 추가 및 스캐폴드
> * 데이터베이스 작업
> * 검색 및 유효성 검사 추가

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>웹앱 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio에서 **새 프로젝트 만들기** 를 선택합니다.

* **ASP.NET Core 웹 애플리케이션** > **다음** 을 선택합니다.

  ![새 ASP.NET Core 웹 애플리케이션 프로젝트 만들기](start-mvc/_static/np_2.1.png)

* 프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다. 코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 **MvcMovie** 로 지정하는 것이 중요합니다.

  ![새 프로젝트 구성](start-mvc/_static/config.png)

* **웹 애플리케이션(Model-View-Controller)** 을 선택합니다. 드롭다운 상자에서 **.NET Core** 와 **ASP.NET Core 3.1** 을 선택한 다음 **만들기** 를 선택합니다.

  ![새 프로젝트 대화 상자, 왼쪽 창의 .NET Core, ASP.NET Core 웹 ](start-mvc/_static/new_project30.png)

Visual Studio는 생성된 MVC 프로젝트에 기본 프로젝트 템플릿을 사용했습니다. 생성된 프로젝트는 다음과 같습니다.

* 작동하는 앱입니다.
* 기본 시작 프로젝트입니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다. 자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.

* [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.
* 디렉터리(`cd`)를 프로젝트가 포함될 폴더로 변경합니다.
* 다음 명령 실행:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * **Required assets to build and debug are missing from 'MvcMovie'. Add them?** 이라는 대화 상자가 표시되면 **예** 를 선택합니다.

  * `dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.
  * `code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* **파일** > **새 솔루션** 을 선택합니다.

  ![macOS 새 솔루션](start-mvc/_static/new_project_vsmac.png)

* Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다. 버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음** 을 선택합니다.

  ![macOS 웹앱 템플릿 선택](start-mvc/_static/web_app_template_vsmac.png)

* **새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.

  * **인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.
  * **대상 프레임워크** 를 선택하는 옵션이 표시되면 최신 3.x 버전을 선택합니다.
  * **새로 만들기** 를 선택합니다.

* 프로젝트 이름을 **MvcMovie** 로 지정하고 **만들기** 를 선택합니다.

  ![macOS 프로젝트 이름 지정](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>앱 실행

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ctrl+F5를 선택하여 디버깅 없이 앱을 실행합니다.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작합니다.
  * 앱을 실행합니다.

  주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다. 로컬 컴퓨터의 표준 호스트 이름은 `localhost`입니다. Visual Studio가 웹 프로젝트를 만들면 임의의 포트가 웹 서버에 사용됩니다.

Ctrl+F5를 선택하여 디버깅 없이 앱을 시작하면 다음을 수행할 수 있습니다.

* 코드를 변경합니다.
* 파일을 저장합니다.
* 브라우저를 빠르게 새로 고치고 코드 변경 내용을 확인합니다.

**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.

![디버그 메뉴](start-mvc/_static/debug_menu.png)

**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.

![IIS Express](start-mvc/_static/iis_express.png)

다음 이미지는 앱을 보여줍니다.

![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ctrl+F5를 선택하여 디버깅 없이 앱을 실행합니다.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * [Kestrel](xref:fundamentals/servers/kestrel)을 시작합니다.
  * 브라우저를 시작합니다.
  * `https://localhost:5001`로 이동합니다.

  주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다. 로컬 컴퓨터의 표준 호스트 이름은 `localhost`입니다. Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.

Ctrl+F5를 선택하여 디버깅 없이 앱을 시작하면 다음을 수행할 수 있습니다.

* 코드를 변경합니다.
* 파일을 저장합니다.
* 브라우저를 빠르게 새로 고치고 코드 변경 내용을 확인합니다.

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* **실행** > **디버깅하지 않고 시작** 을 선택하여 앱을 시작합니다.

  Mac용 Visual Studio: [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하고, `http://localhost:port`로 이동합니다. 여기서 *port* 는 임의로 선택된 포트 번호입니다.

[!INCLUDE[](~/includes/trustCertMac.md)]

주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다. 로컬 컴퓨터의 표준 호스트 이름은 `localhost`입니다. Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다. 앱을 실행할 경우 다른 포트 번호가 표시됩니다.

**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.

다음 이미지는 앱을 보여줍니다.

![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.

> [!div class="step-by-step"]
> [다음](adding-controller.md)

::: moniker-end
