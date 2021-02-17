---
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
ms.openlocfilehash: ed6de823b8b860c7d27e932e9ece40d8b43b0893
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552410"
---
스 캐 폴더를 실행 합니다 Identity .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 >   >  **새 스 캐 폴드 항목** 추가를 클릭 합니다.
* **새 스 캐 폴드 항목 추가** 대화 상자의 왼쪽 창에서 추가를 선택 **Identity**  >  합니다.
* **추가 Identity** 대화 상자에서 원하는 옵션을 선택 합니다.
  * 기존 레이아웃 페이지를 선택 하거나 잘못 된 태그를 사용 하 여 레이아웃 파일을 덮어씁니다.
    * `~/Pages/Shared/_Layout.cshtml`페이지의 경우 Razor
    * `~/Views/Shared/_Layout.cshtml` MVC 프로젝트의 경우
    * Blazor Server 템플릿에서 만든 앱 Blazor Server ()은 `blazorserver` Razor 기본적으로 페이지 또는 MVC에 대해 구성 되지 않습니다. 레이아웃 페이지 항목을 비워 둡니다.
  * 단추를 선택 **+** 하 여 새 **데이터 컨텍스트 클래스** 를 만듭니다. 기본값을 그대로 적용 하거나 클래스를 지정 합니다 (예: `MyApplication.Data.ApplicationDbContext` ).
* **추가** 를 선택합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

이전에 ASP.NET Core 스 캐 폴더를 설치 하지 않은 경우 지금 설치 합니다.

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

프로젝트 파일 (*.csproj*)에 필요한 NuGet 패키지 참조를 추가 합니다. 프로젝트 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

다음 명령을 실행 하 여 스 캐 폴더 옵션을 나열 합니다 Identity .

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

프로젝트 폴더에서 Identity 원하는 옵션으로 스 캐 폴더를 실행 합니다. 예를 들어 기본 UI와 최소 파일 수를 사용 하 여 id를 설정 하려면 다음 명령을 실행 합니다.

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
