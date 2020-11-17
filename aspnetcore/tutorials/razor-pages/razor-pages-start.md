---
title: '자습서: ASP.NET Core에서 Razor Pages 시작'
author: rick-anderson
description: ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명하는 자습서 시리즈 중 첫 번째입니다.
ms.author: riande
ms.date: 09/15/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: b4dcbe9536107cdc5b0342782abc4bad0b89a8dc
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360926"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a>자습서: ASP.NET Core에서 Razor Pages 시작

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"
ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명하는 자습서 시리즈 중 첫 번째입니다.

컨트롤러 및 보기에 익숙한 개발자를 대상으로 한 고급 소개는 [Razor Pages 소개](xref:razor-pages/index)를 참조하세요.

시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.  

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * Razor Pages 웹앱을 Create합니다.
> * 앱을 실행합니다.
> * 프로젝트 파일을 검사합니다.

이 자습서를 마치면 이후의 자습서에서 개선할 Razor Pages 웹앱을 사용할 수 있습니다.

![홈 또는 Index 페이지](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a>사전 요구 사항

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a>Razor Pages 웹앱 Create

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Visual Studio를 시작하고 **새 프로젝트 Create** 를 선택합니다. 자세한 내용은 [Visual Studio에서 새 프로젝트 Create](/visualstudio/ide/create-new-project)를 참조하세요.

   ![시작 창에서 새 프로젝트 Create](razor-pages-start/_static/5/start-window-create-new-project.png)

1. **새 프로젝트 Create** 대화 상자에서 **ASP.NET Core 웹 애플리케이션** 을 선택한 후, **다음** 을 선택합니다.

    ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/5/np.png)
    
1. **새 프로젝트 구성** 대화 상자에서 **프로젝트 이름** 으로 `RazorPagesMovie`를 입력합니다. 대문자 표시를 포함하여 프로젝트 이름을 *RazorPagesMovie* 로 지정해야 예제 코드를 복사해 붙여넣을 때 네임스페이스가 일치합니다.

1. **Create** 을 선택합니다.

    ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/config.png)

1. **새 ASP.NET Core 애플리케이션 Create** 대화 상자에서 다음을 선택합니다.
    1. 드롭다운에서 **.NET Core** 와 **ASP.NET Core 5.0** 을 선택합니다.
    1. **웹 애플리케이션**
    1. **Create**.

     ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/5/npx.png)

    다음 시작 프로젝트를 만듭니다.

    ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.

1. 프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.

1. 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * `dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.
   * `code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

1. **파일** > **새 솔루션** 을 선택합니다.

    ![macOS 새 솔루션](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션** > **다음** 을 선택합니다. 버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션** > **다음** 을 선택합니다.

    ![macOS 웹앱 템플릿 선택](razor-pages-start/_static/web_app_template_vsmac.png)

1. **새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.

    1. **인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.
    1. **대상 프레임워크** 를 선택하는 옵션이 제공되는 경우 최신 .NET 5.x 버전을 선택합니다.
    1. **다음** 을 선택합니다.

1. 프로젝트 이름을 *RazorPagesMovie* 로 지정하고, **Create** 를 선택합니다.

    ![macOS 프로젝트 이름 지정](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>앱 실행

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>프로젝트 파일 검토

이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.

### <a name="pages-folder"></a>페이지 폴더

Razor Pages 및 지원 파일이 들어 있습니다. 각 Razor 페이지는 파일 쌍입니다.

* Razor 구문을 사용하는 C# 코드가 있는 HTML 태그를 포함하는 *.cshtml* 파일.
* 페이지 이벤트를 처리하는 C# 코드가 있는 *.cshtml.cs* 파일.

지원 파일에는 밑줄로 시작하는 이름이 있습니다. 예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다. 이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다. 자세한 내용은 <xref:mvc/views/layout>를 참조하세요.

### <a name="wwwroot-folder"></a>wwwroot 폴더

HTML 파일, JavaScript 파일, CSS 파일과 같은 정적 자산을 포함합니다. 자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.

### appsettings.json

연결 문자열과 같은 구성 데이터를 포함합니다. 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.

### <a name="programcs"></a>Program.cs

앱의 진입점을 포함합니다. 자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.

### <a name="startupcs"></a>Startup.cs

앱 동작을 구성하는 코드를 포함합니다. 자세한 내용은 <xref:fundamentals/startup>를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="step-by-step"]
> [다음: 모델 추가](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명하는 자습서 시리즈 중 첫 번째입니다.

컨트롤러 및 보기에 익숙한 개발자를 대상으로 한 고급 소개는 [Razor Pages 소개](xref:razor-pages/index)를 참조하세요.

시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.  

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * Razor Pages 웹앱을 Create합니다.
> * 앱을 실행합니다.
> * 프로젝트 파일을 검사합니다.

이 자습서의 내용을 마치면 이후의 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.

![홈 또는 Index 페이지](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>사전 요구 사항

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a>Razor Pages 웹앱 Create

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트** 를 선택합니다.
* 새 ASP.NET Core 웹 애플리케이션을 Create하고 **다음** 을 선택합니다.
  ![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/np_2.1.png)
* 프로젝트 이름을 **RazorPagesMovie** 로 지정합니다. 코드를 복사하여 붙여 넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie* 로 지정해야 합니다.
  ![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/config.png)

* 드롭다운에서 **ASP.NET Core 3.1** 을 선택하고 **웹 애플리케이션** 을 선택한 다음 **Create** 를 선택합니다.

![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/3/npx.png)

  다음 시작 프로젝트를 만듭니다.

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.

* 프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.

* 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.
  * `code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.

* 상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 **빌드 및 디버그에 필요한 자산이 ‘RazorPagesMovie’에서 누락되었습니다. Add them?** 라는 대화 상자가 나타납니다. **Yes** 를 선택합니다.

  *launch.json* 및 *tasks.json* 파일을 포함한 *.vscode* 디렉터리가 프로젝트의 루트 디렉터리에 추가됩니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* **파일** > **새 솔루션** 을 선택합니다.

  ![macOS 새 솔루션](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션** > **다음** 을 선택합니다. 버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션** > **다음** 을 선택합니다.

  ![macOS 웹앱 템플릿 선택](razor-pages-start/_static/web_app_template_vsmac.png)

* **새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.

  * **인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.
  * **대상 프레임워크** 를 선택하는 옵션이 제공되는 경우 최신 3.x 버전을 선택합니다.

  **새로 만들기** 를 선택합니다.

* 프로젝트 이름을 **RazorPagesMovie** 로 지정한 다음 **Create** 를 선택합니다.

  ![macOS 프로젝트 이름 지정](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>앱 실행

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>프로젝트 파일 검토

이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.

### <a name="pages-folder"></a>페이지 폴더

Razor Pages 및 지원 파일이 들어 있습니다. 각 Razor 페이지는 파일 쌍입니다.

* Razor 구문을 사용하는 C# 코드가 있는 HTML 태그를 포함하는 *.cshtml* 파일.
* 페이지 이벤트를 처리하는 C# 코드가 있는 *.cshtml.cs* 파일.

지원 파일에는 밑줄로 시작하는 이름이 있습니다. 예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다. 이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다. 자세한 내용은 <xref:mvc/views/layout>를 참조하세요.

### <a name="wwwroot-folder"></a>wwwroot 폴더

HTML 파일, JavaScript 파일, CSS 파일과 같은 정적 파일을 포함합니다. 자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.

### <a name="appsettingsjson"></a>appSettings.json

연결 문자열과 같은 구성 데이터를 포함합니다. 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.

### <a name="programcs"></a>Program.cs

프로그램의 진입점을 포함합니다. 자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.

### <a name="startupcs"></a>Startup.cs

앱 동작을 구성하는 코드를 포함합니다. 자세한 내용은 <xref:fundamentals/startup>를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="step-by-step"]
> [다음: 모델 추가](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

이 시리즈의 첫 번째 자습서입니다. [시리즈](xref:tutorials/razor-pages/index)에서는 ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명합니다.

컨트롤러 및 보기에 익숙한 개발자를 대상으로 한 고급 소개는 [Razor Pages 소개](xref:razor-pages/index)를 참조하세요.

시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.  

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * Razor Pages 웹앱을 Create합니다.
> * 앱을 실행합니다.
> * 프로젝트 파일을 검사합니다.

이 자습서의 내용을 마치면 이후의 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.

![홈 또는 Index 페이지](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>사전 요구 사항

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a>Razor Pages 웹앱 Create

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트** 를 선택합니다.

* 새 ASP.NET Core 웹 애플리케이션을 Create하고 **다음** 을 선택합니다.

  ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/np_2.1.png)

* 프로젝트 이름을 **RazorPagesMovie** 로 지정합니다. 코드를 복사하여 붙여 넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie* 로 지정해야 합니다.

  ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/config.png)

* 드롭다운에서 **ASP.NET Core 2.2** 를 선택하고 **웹 애플리케이션** 을 선택한 다음 **Create** 를 선택합니다.

![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/np_2_2.2.png)

  다음 시작 프로젝트를 만듭니다.

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.

* 프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.

* 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.
  * `code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.

* 상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 **빌드 및 디버그에 필요한 자산이 ‘RazorPagesMovie’에서 누락되었습니다. Add them?** 라는 대화 상자가 나타납니다. **Yes** 를 선택합니다.

  *launch.json* 및 *tasks.json* 파일을 포함한 *.vscode* 디렉터리가 프로젝트의 루트 디렉터리에 추가됩니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* **파일** > **새 솔루션** 을 선택합니다.

![macOS 새 솔루션](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션** > **다음** 을 선택합니다. 버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션** > **다음** 을 선택합니다.

* **새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.

  * **인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.
  * **대상 프레임워크** 를 선택하는 옵션이 제공되는 경우 최신 2.x 버전을 선택합니다.

  **새로 만들기** 를 선택합니다.

* 프로젝트 이름을 **RazorPagesMovie** 로 지정한 다음 **Create** 를 선택합니다.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>앱 실행

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ctrl+F5를 눌러 디버거 없이 실행합니다.

  <kbd>Ctrl+F5</kbd>(디버그 이외 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다. 대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.


  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다. 주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다. `localhost`가 로컬 컴퓨터의 표준 호스트 이름이기 때문입니다. Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다. Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.

* 앱의 홈페이지에서 **승인** 을 선택하여 추적에 동의합니다.

  이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.

  ![홈 또는 Index 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  다음 이미지는 추적에 동의한 후 앱을 보여 줍니다.

  ![홈 또는 Index 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <kbd>Ctrl+F5</kbd>를 눌러 디버거 없이 실행합니다.

  <kbd>Ctrl+F5</kbd>(디버그 이외 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다. 대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.

  Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`로 이동합니다. 주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다. `localhost`가 로컬 컴퓨터의 표준 호스트 이름이기 때문입니다. Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.

* 앱의 홈페이지에서 **승인** 을 선택하여 추적에 동의합니다.

  이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.

  ![홈 또는 Index 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.

  ![홈 또는 Index 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* 디버거 없이 실행하려면 **Cmd-Opt-F5** 를 누릅니다.

  <kbd>Cmd+Opt+F5</kbd>(비디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다. 대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.

  Visual Studio가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`로 이동합니다.

* 앱의 홈페이지에서 **승인** 을 선택하여 추적에 동의합니다.

  이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.

  ![홈 또는 Index 페이지](razor-pages-start/_static/homeGDPR2.2_safari.png)

  다음 이미지는 추적에 동의한 후 앱을 보여 줍니다.

  ![홈 또는 Index 페이지](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>프로젝트 파일 검토

이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.

### <a name="pages-folder"></a>페이지 폴더

Razor Pages 및 지원 파일이 들어 있습니다. 각 Razor 페이지는 파일 쌍입니다.

* Razor 구문을 사용하는 C# 코드가 있는 HTML 태그를 포함하는 *.cshtml* 파일.
* 페이지 이벤트를 처리하는 C# 코드가 있는 *.cshtml.cs* 파일.

지원 파일에는 밑줄로 시작하는 이름이 있습니다. 예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다. 이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다. 자세한 내용은 <xref:mvc/views/layout>를 참조하세요.

Razor Pages는 `PageModel`에서 파생됩니다. 일반적으로 `PageModel` 파생 클래스의 이름은 `<PageName>Model`로 지정됩니다.

### <a name="wwwroot-folder"></a>wwwroot 폴더

HTML 파일, JavaScript 파일, CSS 파일과 같은 정적 파일을 포함합니다. 자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.

### <a name="appsettingsjson"></a>appSettings.json

연결 문자열과 같은 구성 데이터를 포함합니다. 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.

### <a name="programcs"></a>Program.cs

프로그램의 진입점을 포함합니다. 자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.

### <a name="startupcs"></a>Startup.cs

cookie에 대한 동의 필요 여부 등 앱 동작을 구성하는 코드를 포함합니다. 자세한 내용은 <xref:fundamentals/startup>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>다음 단계

> [!div class="step-by-step"]
> [다음: 모델 추가](xref:tutorials/razor-pages/model)

::: moniker-end
