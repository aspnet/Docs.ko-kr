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
ms.openlocfilehash: 1161f7731898221e51a4c7f9f246269b83c6ae80
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551620"
---
::: moniker range=">= aspnetcore-3.0"

스 캐 폴더를 실행 합니다 Identity .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 >  > **새 스 캐 폴드 항목** 추가를 클릭 합니다.
* **스 캐 폴드 추가** 대화 상자의 왼쪽 창에서 추가를 선택 **Identity** > 합니다.
* **추가 Identity** 대화 상자에서 원하는 옵션을 선택 합니다.
  * 레이아웃 파일을 잘못 된 태그로 덮어쓰지 않도록 기존 레이아웃 페이지를 선택 합니다. 기존 *\_ 레이아웃* 을 선택한 경우에는이 파일을 덮어쓰지 **않습니다** . 예를 들면 다음과 같습니다.
    * `~/Pages/Shared/_Layout.cshtml`Razor Blazor Server 기존 페이지 인프라를 사용 하는 페이지 또는 프로젝트 Razor
    * `~/Views/Shared/_Layout.cshtml`Blazor Server기존 mvc 인프라를 사용 하는 mvc 프로젝트 또는 프로젝트의 경우
* 기존 데이터 컨텍스트를 사용 하려면 재정의할 파일을 하나 이상 선택 합니다. 데이터 컨텍스트를 추가 하려면 파일을 하나 이상 선택 해야 합니다.
  * 데이터 컨텍스트 클래스를 선택 합니다.
  * **추가** 를 선택합니다.
* 새 사용자 컨텍스트를 만들고 다음에 대 한 사용자 지정 사용자 클래스를 만들 수 있습니다 Identity .
  * 단추를 선택 **+** 하 여 새 **데이터 컨텍스트 클래스** 를 만듭니다. 기본값을 그대로 적용 하거나 클래스를 지정 합니다 (예: `MyApplication.Data.ApplicationDbContext` ).
  * **추가** 를 선택합니다.

참고: 새 사용자 컨텍스트를 만드는 경우 재정의할 파일을 선택할 필요가 없습니다.

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

프로젝트 폴더에서 Identity 원하는 옵션으로 스 캐 폴더를 실행 합니다. 예를 들어 기본 UI를 사용 하 여 id를 설정 하 고 최소 파일 수를 설정 하려면 다음 명령을 실행 합니다. DB 컨텍스트에 대 한 올바른 정규화 된 이름을 사용 합니다.

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다. PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 파일 목록을 큰따옴표로 묶습니다. 예를 들면 다음과 같습니다.

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Identity플래그 또는 플래그를 지정 하지 않고 스 캐 폴더를 실행 하면 `--files` `--useDefaultUI` 사용 가능한 모든 Identity UI 페이지가 프로젝트에 생성 됩니다.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

스 캐 폴더를 실행 합니다 Identity .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 >  > **새 스 캐 폴드 항목** 추가를 클릭 합니다.
* **스 캐 폴드 추가** 대화 상자의 왼쪽 창에서 추가를 선택 **Identity** > 합니다.
* **추가 Identity** 대화 상자에서 원하는 옵션을 선택 합니다.
  * 기존 레이아웃 페이지를 선택 하거나 잘못 된 태그를 사용 하 여 레이아웃 파일을 덮어씁니다. 기존 *\_ 레이아웃* 을 선택한 경우에는이 파일을 덮어쓰지 **않습니다** . 예를 들면 다음과 같습니다.
    * `~/Pages/Shared/_Layout.cshtml`페이지의 경우 Razor
    * `~/Views/Shared/_Layout.cshtml` MVC 프로젝트의 경우
* 기존 데이터 컨텍스트를 사용 하려면 재정의할 파일을 하나 이상 선택 합니다. 데이터 컨텍스트를 추가 하려면 파일을 하나 이상 선택 해야 합니다.
  * 데이터 컨텍스트 클래스를 선택 합니다.
  * **추가** 를 선택합니다.
* 새 사용자 컨텍스트를 만들고 다음에 대 한 사용자 지정 사용자 클래스를 만들 수 있습니다 Identity .
  * 단추를 선택 **+** 하 여 새 **데이터 컨텍스트 클래스** 를 만듭니다. 기본값을 그대로 적용 하거나 클래스를 지정 합니다 (예: `MyApplication.Data.ApplicationDbContext` ).
  * **추가** 를 선택합니다.

참고: 새 사용자 컨텍스트를 만드는 경우 재정의할 파일을 선택할 필요가 없습니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

이전에 ASP.NET Core 스 캐 폴더를 설치 하지 않은 경우 지금 설치 합니다.

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

프로젝트 파일 (*.csproj*)에 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 에 대 한 패키지 참조를 추가 합니다. 프로젝트 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

다음 명령을 실행 하 여 스 캐 폴더 옵션을 나열 합니다 Identity .

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

프로젝트 폴더에서 Identity 원하는 옵션으로 스 캐 폴더를 실행 합니다. 예를 들어 기본 UI를 사용 하 여 id를 설정 하 고 최소 파일 수를 설정 하려면 다음 명령을 실행 합니다. DB 컨텍스트에 대 한 올바른 정규화 된 이름을 사용 합니다.

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다. PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 파일 목록을 큰따옴표로 묶습니다. 예를 들면 다음과 같습니다.

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Identity플래그 또는 플래그를 지정 하지 않고 스 캐 폴더를 실행 하면 `--files` `--useDefaultUI` 사용 가능한 모든 Identity UI 페이지가 프로젝트에 생성 됩니다.

---

::: moniker-end
