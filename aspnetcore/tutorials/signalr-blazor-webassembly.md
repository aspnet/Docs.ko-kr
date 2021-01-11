---
title: 호스트된 Blazor WebAssembly 앱을 활용하여 ASP.NET Core SignalR 사용
author: guardrex
description: Blazor WebAssembly와 함께 ASP.NET Core SignalR을 사용하는 채팅 앱을 만듭니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: b2f58fb29e451628aead4ad35c7272a1409cf3d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97797355"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a>호스트된 Blazor WebAssembly 앱을 활용하여 ASP.NET Core SignalR 사용

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

이 자습서에서는 Blazor WebAssembly와 함께 SignalR을 이용해서 실시간 앱을 구현하기 위한 기본 사항을 알려 줍니다. 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Blazor WebAssembly 호스트된 앱 프로젝트 만들기
> * SignalR 클라이언트 라이브러리 추가
> * SignalR 허브 추가
> * SignalR 서비스 및 SignalR 허브에 대한 엔드포인트 추가
> * 채팅을 위한 Razor 구성 요소 코드 추가

이 모든 과정을 마치면 채팅 앱을 실행할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>사전 요구 사항

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16.8 이상](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)과 **ASP.NET 및 웹 개발** 워크로드
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* [Mac용 Visual Studio 버전 8.8 이상](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16.6 이상](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)과 **ASP.NET 및 웹 개발** 워크로드
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* [Mac용 Visual Studio 버전 8.6 이상](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a>호스트된 Blazor WebAssembly 앱 프로젝트 만들기

선택한 도구의 지침을 따르세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Visual Studio 16.8 이상 및 .NET Core SDK 5.0.0 이상이 필요합니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Visual Studio 16.6 이상 및 .NET Core SDK 3.1.300 이상이 필요합니다.

::: moniker-end

1. 새 프로젝트를 만듭니다.

1. **Blazor 앱** 를 선택하고 **다음** 을 선택합니다.

1. **프로젝트 이름** 필드에 `BlazorSignalRApp`을 입력합니다. **위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다. **만들기** 를 선택합니다.

1. **Blazor WebAssembly 앱** 템플릿을 선택합니다.

1. **고급** 에서 **ASP.NET Core 호스팅** 확인란을 선택합니다.

1. **만들기** 를 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Visual Studio Code에서 앱의 프로젝트 폴더를 엽니다.

1. 앱을 빌드하고 디버그할 자산을 추가하기 위한 대화 상자가 나타나면 **예** 를 선택합니다. Visual Studio Code는 생성된 `launch.json` 및 `tasks.json` 파일이 포함된 `.vscode` 폴더를 자동으로 추가합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

1. 최신 버전의 [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치하고 다음 단계를 수행합니다.

1. **파일** > **새 솔루션** 을 선택하거나 **시작 창** 에서 **새** 프로젝트를 만듭니다.

1. 사이드바에서 **웹 및 콘솔** > **앱** 을 선택합니다.

1. **Blazor WebAssembly 앱** 템플릿을 선택합니다. **새로 만들기** 를 선택합니다.

1. **인증** 이 **인증 없음** 으로 설정되었는지 확인합니다. **ASP.NET Core에서 호스트** 확인란을 선택합니다. **새로 만들기** 를 선택합니다.

1. **프로젝트 이름** 필드에서 앱 이름을 `BlazorSignalRApp`로 지정합니다. **만들기** 를 선택합니다.

   개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다. 인증서를 신뢰하려면 사용자 및 키 집합 암호가 필요합니다.

1. 프로젝트 폴더로 이동하고 프로젝트의 솔루션 파일(`.sln`)을 열어 프로젝트를 엽니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

명령 셸에서 다음 명령을 실행합니다.

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a>SignalR 클라이언트 라이브러리 추가

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. **솔루션 탐색기** 에서 `BlazorSignalRApp.Client` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.

1. **NuGet 패키지 관리** 대화 상자에서 **패키지 원본** 이 `nuget.org`로 설정되어 있는지 확인합니다.

1. **찾아보기** 를 선택하고 검색 상자에 `Microsoft.AspNetCore.SignalR.Client`를 입력합니다.

1. 검색 결과에서 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 패키지를 선택하고 **설치** 를 선택합니다.

1. **변경 내용 미리 보기** 대화 상자가 표시되면 **확인** 을 선택합니다.

1. **라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

**통합 터미널**(도구 모음에서 **보기** > **터미널**)에서 다음 명령을 실행합니다.

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

1. **솔루션** 사이드바에서 `BlazorSignalRApp.Client` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.

1. **NuGet 패키지 관리** 대화 상자에서 원본 드롭다운이 `nuget.org`로 설정되어 있는지 확인합니다.

1. **찾아보기** 를 선택하고 검색 상자에 `Microsoft.AspNetCore.SignalR.Client`를 입력합니다.

1. 검색 결과에서 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 패키지 옆의 확인란을 선택하고 **패키지 추가** 를 선택합니다.

1. **라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

명령 셸에서 다음 명령을 실행합니다.

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>System.Text.Encodings.Web 패키지 추가

ASP.NET Core 3.1 앱에서 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0을 사용하는 경우 패키지 확인 문제로 인해 `BlazorSignalRApp.Server` 프로젝트에 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web)에 대한 패키지 참조가 필요합니다. 기본 문제는 .NET 5의 이후 패치 릴리스에서 해결될 예정입니다. 자세한 내용은 [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560)를 참조하세요.

ASP.NET Core 3.1 호스트 Blazor 솔루션의 `BlazorSignalRApp.Server` 프로젝트에 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web)을 추가하려면 선택한 도구의 지침을 따르세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. **솔루션 탐색기** 에서 `BlazorSignalRApp.Server` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.

1. **NuGet 패키지 관리** 대화 상자에서 **패키지 원본** 이 `nuget.org`로 설정되어 있는지 확인합니다.

1. **찾아보기** 를 선택하고 검색 상자에 `System.Text.Encodings.Web`를 입력합니다.

1. 검색 결과에서 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 패키지를 선택하고 **설치** 를 선택합니다.

1. **변경 내용 미리 보기** 대화 상자가 표시되면 **확인** 을 선택합니다.

1. **라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

**통합 터미널**(도구 모음에서 **보기** > **터미널**)에서 다음 명령을 실행합니다.

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

1. **솔루션** 사이드바에서 `BlazorSignalRApp.Server` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.

1. **NuGet 패키지 관리** 대화 상자에서 원본 드롭다운이 `nuget.org`로 설정되어 있는지 확인합니다.

1. **찾아보기** 를 선택하고 검색 상자에 `System.Text.Encodings.Web`를 입력합니다.

1. 검색 결과에서 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 패키지 옆의 확인란을 선택하고 **패키지 추가** 를 선택합니다.

1. **라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함** 을 선택합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

명령 셸에서 다음 명령을 실행합니다.

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a>SignalR 허브 추가

`BlazorSignalRApp.Server` 프로젝트에서 `Hubs`(복수형) 폴더를 만들고 다음 `ChatHub` 클래스(`Hubs/ChatHub.cs`)를 추가합니다.

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a>서비스 및 SignalR 허브에 대한 엔드포인트 추가

1. `BlazorSignalRApp.Server` 프로젝트에서 `Startup.cs` 파일을 엽니다.

1. `ChatHub` 클래스에 대한 네임스페이스를 파일의 맨 위에 추가합니다.

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. `Startup.ConfigureServices`에 SignalR 및 응답 압축 미들웨어 서비스를 추가합니다.

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. `Startup.Configure`의 경우

   * 처리 파이프라인 구성의 위쪽에서 응답 압축 미들웨어를 사용합니다.
   * 컨트롤러와 클라이언트 쪽 대체에 대한 엔드포인트 사이에 허브에 대한 엔드포인트를 추가합니다.

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. `Startup.ConfigureServices`에 SignalR 및 응답 압축 미들웨어 서비스를 추가합니다.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. `Startup.Configure`의 경우

   * 처리 파이프라인 구성의 위쪽에서 응답 압축 미들웨어를 사용합니다.
   * 컨트롤러와 클라이언트 쪽 대체에 대한 엔드포인트 사이에 허브에 대한 엔드포인트를 추가합니다.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a>채팅을 위한 Razor 구성 요소 코드 추가

1. `BlazorSignalRApp.Client` 프로젝트에서 `Pages/Index.razor` 파일을 엽니다.

::: moniker range=">= aspnetcore-5.0"

1. 태그를 다음 코드로 바꿉니다.

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. 태그를 다음 코드로 바꿉니다.

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>앱 실행

선택한 도구의 지침을 따르세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **솔루션 탐색기** 에서 `BlazorSignalRApp.Server` 프로젝트를 선택합니다. <kbd>F5</kbd> 키를 눌러 디버깅이 설정된 상태로 앱을 실행하거나 <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 디버깅 없이 실행합니다.

1. 주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.

1. 브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다. 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-5.0"

1. 서버 앱의 시작 프로필을 만들기 위한 VS Code가 제공되는 경우(`.vscode/launch.json`), `program` 항목은 다음과 유사하게 표시되어 앱의 어셈블리(`{APPLICATION NAME}.Server.dll`)를 가리킵니다.

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. 서버 앱의 시작 프로필을 만들기 위한 VS Code가 제공되는 경우(`.vscode/launch.json`), `program` 항목은 다음과 유사하게 표시되어 앱의 어셈블리(`{APPLICATION NAME}.Server.dll`)를 가리킵니다.

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <kbd>F5</kbd> 키를 눌러 디버깅이 설정된 상태로 앱을 실행하거나 <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 디버깅 없이 실행합니다.

1. 주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.

1. 브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다. 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

1. **솔루션** 사이드바에서 `BlazorSignalRApp.Server` 프로젝트를 선택합니다. <kbd>⌘</kbd>+<kbd>↩</kbd>를 눌러 디버깅이 설정된 상태로 앱을 실행하거나 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>를 눌러 디버깅 없이 실행합니다.

1. 주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.

1. 브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다. 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. 주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.

1. 브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, 메시지를 보내는 단추를 선택합니다. 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>다음 단계

본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.

> [!div class="checklist"]
> * Blazor WebAssembly 호스트된 앱 프로젝트 만들기
> * SignalR 클라이언트 라이브러리 추가
> * SignalR 허브 추가
> * SignalR 서비스 및 SignalR 허브에 대한 엔드포인트 추가
> * 채팅을 위한 Razor 구성 요소 코드 추가

Blazor 앱 빌드에 대한 자세한 내용은 Blazor 설명서를 참조하세요.

> [!div class="nextstepaction"]
> <xref:blazor/index>
> [Identity 서버, WebSocket 및 Server-Sent 이벤트를 사용하는 전달자 토큰 인증](xref:signalr/authn-and-authz#bearer-token-authentication)

## <a name="additional-resources"></a>추가 자료

* <xref:signalr/introduction>
* [SignalR 인증에 대한 원본 간 협상](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
