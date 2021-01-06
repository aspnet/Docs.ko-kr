---
title: ASP.NET Core SignalR 시작하기
author: bradygaster
description: 이 자습서에서는 ASP.NET Core SignalR을 사용하는 채팅 앱을 만듭니다.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
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
uid: tutorials/signalr
ms.openlocfilehash: 1dad2264250bf43ec6c1df679b8754a82a5addfd
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "95417671"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a>자습서: ASP.NET Core SignalR 시작하기

::: moniker range=">= aspnetcore-3.0"

이 자습서에서는 SignalR을 이용해서 실시간 앱을 구현하기 위한 기본 사항을 알려줍니다. 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 웹 프로젝트를 만듭니다.
> * SignalR 클라이언트 라이브러리를 추가합니다.
> * SignalR 허브를 만듭니다.
> * SignalR을 사용하도록 프로젝트를 구성합니다.
> * 모든 클라이언트에서 연결된 모든 클라이언트로 메시지를 보내는 코드를 추가합니다.

이 모든 과정을 마치면 동작하는 채팅 앱이 만들어집니다.

![SignalR 샘플 앱](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>사전 요구 사항

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a>웹앱 프로젝트 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* 메뉴에서 **파일 > 새 프로젝트** 를 선택합니다.

* **새 프로젝트 만들기** 대화 상자에서 **ASP.NET Core 웹 애플리케이션** 을 선택한 후, **다음** 을 선택합니다.

* **새 프로젝트 구성** 대화 상자에서 *SignalRChat* 프로젝트 이름을 지정한 다음, **만들기** 를 선택합니다.

* **새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.1** 을 선택합니다. 

* Razor Pages를 사용하는 프로젝트를 생성하려면 **웹 애플리케이션** 을 선택한 다음, **만들기** 를 선택합니다.

  ![Visual Studio의 새 프로젝트 대화 상자](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* 새 프로젝트 폴더를 만들 폴더에 대한 [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.

* 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   cd SignalRChat
   code -r .
   ```

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* 메뉴에서 **파일 > 새 솔루션** 을 선택합니다.

* **.NET Core > 앱 > 웹 애플리케이션** (**웹 애플리케이션(Model-View-Controller)** 선택 안 함)을 선택한 후, **다음** 을 선택합니다.

* **대상 프레임워크** 가 **.NET Core 3.1** 로 설정되어 있는지 확인한 후 **다음** 을 선택합니다.

* 프로젝트 이름을 *SignalRChat* 으로 지정한 다음, **만들기** 를 선택합니다.

---

## <a name="add-the-no-locsignalr-client-library"></a>SignalR 클라이언트 라이브러리 추가

SignalR 서버 라이브러리는 ASP.NET Core 3.1 공유 프레임워크에 포함되어 있습니다. JavaScript 클라이언트 라이브러리는 프로젝트에 자동으로 포함되지 않습니다. 본 자습서에서는 라이브러리 관리자(LibMan)를 사용하여 *unpkg* 에서 클라이언트 라이브러리를 가져옵니다. unpkg는 Node.js의 패키지 관리자인 npm에서 찾은 모든 내용을 전달할 수 있는 CDN(콘텐츠 배달 네트워크)입니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **클라이언트 쪽 라이브러리** 를 선택합니다.

* **클라이언트 쪽 라이브러리 추가** 대화 상자에서 **공급자** 로 **unpkg** 를 선택합니다.

* **라이브러리** 인 경우 `@microsoft/signalr@latest`를 입력합니다.

* **특정 파일 선택** 을 선택하고 *dist/browser* 폴더를 확장한 다음 *signalr.js* 및 *signalr.min.js* 를 선택합니다.

* **대상 위치** 를 *wwwroot/js/signalr/* 로 설정하고 **설치** 를 선택합니다.

  ![클라이언트 쪽 라이브러리 추가 대화 상자 - 라이브러리 선택](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  그러면 LibMan이 *wwwroot/js/signalr* 폴더를 생성한 다음 여기에 선택한 파일을 복사합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* 통합 터미널에서 다음 명령을 실행하여 LibMan을 설치합니다.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* 다음 명령을 실행하고 LibMan을 사용하여 SignalR 클라이언트 라이브러리를 가져옵니다. 출력이 표시되기 전에 잠시 기다려야 할 수도 있습니다.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  매개 변수는 다음 옵션을 지정합니다.
  * unpkg 공급자를 사용합니다.
  * 파일을 *wwwroot/js/signalr* 대상으로 복사합니다.
  * 지정된 파일만 복사합니다.

  출력은 다음 예와 같습니다.

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* **터미널** 에서 다음 명령을 실행하여 LibMan을 설치합니다.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* 프로젝트 폴더( *SignalRChat.csproj* 파일을 포함하는 폴더)로 이동합니다.

* 다음 명령을 실행하고 LibMan을 사용하여 SignalR 클라이언트 라이브러리를 가져옵니다.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  매개 변수는 다음 옵션을 지정합니다.
  * unpkg 공급자를 사용합니다.
  * 파일을 *wwwroot/js/signalr* 대상으로 복사합니다.
  * 지정된 파일만 복사합니다.

  출력은 다음 예와 같습니다.

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a>SignalR 허브 만들기

*허브* 는 클라이언트-서버 통신을 처리하는 높은 수준의 파이프라인으로 제공되는 클래스입니다.

* SignalRChat 프로젝트 폴더에 *Hubs* 폴더를 만듭니다.

* *Hubs* 폴더에 다음 코드를 사용하여 *ChatHub.cs* 파일을 만듭니다.

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  `ChatHub` 클래스는 SignalR `Hub` 클래스에서 상속합니다. `Hub` 클래스는 연결, 그룹 및 메시징을 관리합니다.

  연결된 클라이언트에서 `SendMessage` 메서드를 호출하여 모든 클라이언트에 메시지를 보낼 수 있습니다. 메서드를 호출하는 JavaScript 클라이언트 코드는 자습서 뒷부분에 나와 있습니다. SignalR 코드는 최대한의 확장성을 제공할 수 있도록 비동기적입니다.

## <a name="configure-no-locsignalr"></a>SignalR 구성

SignalR에 SignalR 요청을 전달하도록 SignalR 서버를 구성해야 합니다.

* 다음 강조 표시된 코드를 *Startup.cs* 파일에 추가합니다.

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  이러한 변경 사항은 ASP.NET Core 종속성 주입 및 라우팅 시스템에 SignalR을 추가합니다.

## <a name="add-no-locsignalr-client-code"></a>SignalR 클라이언트 코드 추가

* *Pages\Index.cshtml* 의 콘텐츠를 다음 코드로 바꿉니다.

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  위의 코드는

  * 이름 및 메시지 텍스트에 대한 텍스트 상자 및 전송 단추를 만듭니다.
  * SignalR 허브에서 받은 메시지를 표시하기 위해 `id="messagesList"`를 사용하여 목록을 만듭니다.
  * SignalR 및 *chat.js* 애플리케이션 코드(다음 단계에서 만듦)에 대한 참조를 포함합니다.

* *wwwroot/js* 폴더에서 다음 코드를 사용하여 *chat.js* 파일을 만듭니다.

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  위의 코드는

  * 연결을 만들고 시작합니다.
  * 허브에 메시지를 전송하는 처리기를 전송 단추에 추가합니다.
  * 허브에서 메시지를 수신하고 목록에 추가하는 처리기를 연결 개체에 추가합니다.

## <a name="run-the-app"></a>앱 실행

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **CTRL+F5** 키를 눌러 디버깅 없이 앱을 실행합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 통합 터미널에서 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* 메뉴에서 **실행 > 디버깅하지 않고 시작** 을 선택합니다.

---

* 주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.

* 브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기 메시지** 단추를 선택합니다.

  이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.

  ![SignalR 샘플 앱](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * 앱이 작동하지 않는 경우 브라우저 개발자 도구(F12)를 열고 콘솔로 이동합니다. HTML 및 JavaScript 코드와 관련된 오류를 볼 수 있습니다. 예를 들어 지정되지 않은 다른 폴더에 *signalr.js* 를 넣었다고 가정합니다. 이 경우 해당 파일에 대한 참조는 작동하지 않으며 콘솔에 404 오류가 표시됩니다.
>   ![signalr.js 찾을 수 없음 오류](signalr/_static/3.x/f12-console.png)
> * Chrome에서 ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY 오류가 발생하는 경우, 다음 명령을 실행하여 개발 인증서를 업데이트합니다.
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 자습서에서는 SignalR을 이용해서 실시간 앱을 구현하기 위한 기본 사항을 알려줍니다. 다음과 같은 작업을 수행하는 방법을 살펴봅니다. 

> [!div class="checklist"]  
> * 웹 프로젝트를 만듭니다.   
> * SignalR 클라이언트 라이브러리를 추가합니다.   
> * SignalR 허브를 만듭니다. 
> * SignalR을 사용하도록 프로젝트를 구성합니다. 
> * 모든 클라이언트에서 연결된 모든 클라이언트로 메시지를 보내는 코드를 추가합니다.  
이제 작동하는 채팅 앱이 만들어졌습니다. ![SignalR 샘플 앱](signalr/_static/2.x/signalr-get-started-finished.png)   

## <a name="prerequisites"></a>사전 요구 사항    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>웹 프로젝트 만들기 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* 메뉴에서 **파일 > 새 프로젝트** 를 선택합니다. 

* **새 프로젝트** 대화 상자에서 **설치됨 &gt; Visual C# &gt; 웹 &gt; ASP.NET Core 웹 애플리케이션** 을 선택합니다. 프로젝트 이름을 *SignalRChat* 으로 지정합니다.   

  ![Visual Studio의 새 프로젝트 대화 상자](signalr/_static/2.x/signalr-new-project-dialog.png)    

* Razor Pages를 사용하는 프로젝트를 생성하려면 **웹 애플리케이션** 을 선택합니다.   

* **.NET Core** 의 대상 프레임워크를 선택하고, **ASP.NET Core 2.2** 를 선택하고, **확인** 을 클릭합니다.    

  ![Visual Studio의 새 프로젝트 대화 상자](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* 새 프로젝트 폴더를 만들 폴더에 대한 [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.  

* 다음 명령을 실행합니다.   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)   

* 메뉴에서 **파일 > 새 솔루션** 을 선택합니다.    

* **.NET Core > 앱 > ASP.NET Core 웹앱**(**ASP.NET Core 웹앱(MVC) 선택 안 함**)을 선택합니다.  

* **새로 만들기** 를 선택합니다.  

* 프로젝트 이름을 *SignalRChat* 으로 지정한 다음, **만들기** 를 선택합니다. 

--- 

## <a name="add-the-no-locsignalr-client-library"></a>SignalR 클라이언트 라이브러리 추가 

SignalR 서버 라이브러리는 `Microsoft.AspNetCore.App` 메타패키지에 포함되어 있습니다. JavaScript 클라이언트 라이브러리는 프로젝트에 자동으로 포함되지 않습니다. 본 자습서에서는 라이브러리 관리자(LibMan)를 사용하여 *unpkg* 에서 클라이언트 라이브러리를 가져옵니다. unpkg는 Node.js의 패키지 관리자인 npm에서 찾은 모든 내용을 전달할 수 있는 CDN(콘텐츠 배달 네트워크)입니다.   

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **클라이언트 쪽 라이브러리** 를 선택합니다.  

* **클라이언트 쪽 라이브러리 추가** 대화 상자에서 **공급자** 로 **unpkg** 를 선택합니다. 

* **라이브러리** 에 `@microsoft/signalr@3`을 입력하고 미리 보기가 아닌 최신 버전을 선택합니다.  

  ![클라이언트 쪽 라이브러리 추가 대화 상자 - 라이브러리 선택](signalr/_static/2.x/libman1.png)   

* **Choose specific files**(특정 파일 선택)를 선택하고 *dist/browser* 폴더를 확장한 후 *signalr.js* 및 *signalr.min.js* 를 선택합니다. 

* **대상 위치** 를 *wwwroot/lib/signalr/* 로 설정하고 **설치** 를 선택합니다.    

  ![클라이언트 쪽 라이브러리 추가 대화 상자 - 파일 및 대상 선택](signalr/_static/2.x/libman2.png) 

  그러면 LibMan이 *wwwroot/lib/signalr* 폴더를 생성한 다음 여기에 선택한 파일을 복사합니다.    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* 통합 터미널에서 다음 명령을 실행하여 LibMan을 설치합니다.  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* 다음 명령을 실행하고 LibMan을 사용하여 SignalR 클라이언트 라이브러리를 가져옵니다. 출력이 표시되기 전에 잠시 기다려야 할 수도 있습니다. 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  매개 변수는 다음 옵션을 지정합니다. 
  * unpkg 공급자를 사용합니다. 
  * 파일을 *wwwroot/lib/signalr* 대상으로 복사합니다.    
  * 지정된 파일만 복사합니다.  

  출력은 다음 예와 같습니다.  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)   

* **터미널** 에서 다음 명령을 실행하여 LibMan을 설치합니다. 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* 프로젝트 폴더( *SignalRChat.csproj* 파일을 포함하는 폴더)로 이동합니다.   

* 다음 명령을 실행하고 LibMan을 사용하여 SignalR 클라이언트 라이브러리를 가져옵니다.    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  매개 변수는 다음 옵션을 지정합니다. 
  * unpkg 공급자를 사용합니다. 
  * 파일을 *wwwroot/lib/signalr* 대상으로 복사합니다.    
  * 지정된 파일만 복사합니다.  

  출력은 다음 예와 같습니다.  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a>SignalR 허브 만들기   

*허브* 는 클라이언트-서버 통신을 처리하는 높은 수준의 파이프라인으로 제공되는 클래스입니다.   

* SignalRChat 프로젝트 폴더에 *Hubs* 폴더를 만듭니다.  

* *Hubs* 폴더에 다음 코드를 사용하여 *ChatHub.cs* 파일을 만듭니다. 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  `ChatHub` 클래스는 SignalR `Hub` 클래스에서 상속합니다. `Hub` 클래스는 연결, 그룹 및 메시징을 관리합니다.  

  연결된 클라이언트에서 `SendMessage` 메서드를 호출하여 모든 클라이언트에 메시지를 보낼 수 있습니다. 메서드를 호출하는 JavaScript 클라이언트 코드는 자습서 뒷부분에 나와 있습니다. SignalR 코드는 최대한의 확장성을 제공할 수 있도록 비동기적입니다.    

## <a name="configure-no-locsignalr"></a>SignalR 구성  

SignalR에 SignalR 요청을 전달하도록 SignalR 서버를 구성해야 합니다.    

* 다음 강조 표시된 코드를 *Startup.cs* 파일에 추가합니다.  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  이러한 변경 사항은 ASP.NET Core 종속성 주입 시스템 및 미들웨어 파이프라인에 SignalR을 추가합니다.  

## <a name="add-no-locsignalr-client-code"></a>SignalR 클라이언트 코드 추가    

* *Pages\Index.cshtml* 의 콘텐츠를 다음 코드로 바꿉니다.  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  위의 코드는   

  * 이름 및 메시지 텍스트에 대한 텍스트 상자 및 전송 단추를 만듭니다.  
  * SignalR 허브에서 받은 메시지를 표시하기 위해 `id="messagesList"`를 사용하여 목록을 만듭니다.   
  * SignalR 및 *chat.js* 애플리케이션 코드(다음 단계에서 만듦)에 대한 참조를 포함합니다.    

* *wwwroot/js* 폴더에서 다음 코드를 사용하여 *chat.js* 파일을 만듭니다.  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  위의 코드는   

  * 연결을 만들고 시작합니다.    
  * 허브에 메시지를 전송하는 처리기를 전송 단추에 추가합니다. 
  * 허브에서 메시지를 수신하고 목록에 추가하는 처리기를 연결 개체에 추가합니다.  

## <a name="run-the-app"></a>앱 실행  

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

* **CTRL+F5** 키를 눌러 디버깅 없이 앱을 실행합니다.   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* 통합 터미널에서 다음 명령을 실행합니다.    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* 메뉴에서 **실행 > 디버깅하지 않고 시작** 을 선택합니다.

---

* 주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.

* 브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기 메시지** 단추를 선택합니다.  

  이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.   

  ![SignalR 샘플 앱](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> 앱이 작동하지 않는 경우 브라우저 개발자 도구(F12)를 열고 콘솔로 이동합니다. HTML 및 JavaScript 코드와 관련된 오류를 볼 수 있습니다. 예를 들어 지정되지 않은 다른 폴더에 *signalr.js* 를 넣었다고 가정합니다. 이 경우 해당 파일에 대한 참조는 작동하지 않으며 콘솔에 404 오류가 표시됩니다.   
> ![signalr.js 찾을 수 없음 오류](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>추가 자료 
* [이 자습서의 YouTube 버전](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
