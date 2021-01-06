---
title: 5부. ASP.NET Core MVC 앱에서 데이터베이스 작업
author: rick-anderson
description: 5부. ASP.NET Core MVC 앱에 모델 추가
ms.author: riande
ms.date: 11/10/2020
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
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: c082457258346637499b8ea8000dd8792931112f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94422654"
---
# <a name="part-5-work-with-a-database-in-an-aspnet-core-mvc-app"></a>5부. ASP.NET Core MVC 앱에서 데이터베이스 작업

::: moniker range=">= aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

`MvcMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다. 데이터베이스 컨텍스트는 *Startup.cs* 파일의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString`을 읽습니다. 로컬 개발의 경우 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString`을 읽습니다. 로컬 개발의 경우 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

앱을 테스트 또는 프로덕션 서버에 배포할 때 환경 변수를 사용하여 연결 문자열을 프로덕션 SQL Server로 설정할 수 있습니다. 자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB는 프로그램 개발을 위한 SQL Server Express 데이터베이스 엔진의 간단 버전입니다. LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다. 기본적으로 LocalDB 데이터베이스는 *C:/Users/{user}* 디렉터리에 *.mdf* 파일을 만듭니다.

* **보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.

  ![보기 메뉴](working-with-sql/_static/ssox.png)

* 마우스 오른쪽 단추로 `Movie` 테이블 **> 디자이너 보기** 를 클릭합니다.

  ![Movie 테이블에서 열린 상황에 맞는 메뉴](working-with-sql/_static/design.png)

  ![디자이너에 열린 Movie 테이블](working-with-sql/_static/dv.png)

`ID` 옆의 키 아이콘을 확인합니다. 기본적으로 EF는 `ID`라는 속성을 기본 키로 만듭니다.

* 마우스 오른쪽 단추로 `Movie` 테이블 **> 데이터 보기** 를 클릭합니다.

  ![Movie 테이블에서 열린 상황에 맞는 메뉴](working-with-sql/_static/ssox2.png)

  ![테이블 데이터를 보여 주는 열린 Movie 테이블](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>데이터베이스 시드

*Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다. 생성된 코드를 다음으로 바꿉니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

DB에 영화가 존재할 경우 시드 이니셜라이저가 반환되고 영화가 추가되지 않습니다.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>시드 이니셜라이저 추가

*Program.cs* 의 내용을 다음 코드로 바꿉니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

앱을 테스트합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* DB의 모든 레코드를 삭제합니다. 브라우저에서 삭제 링크를 사용하거나 SSOX에서 이를 수행할 수 있습니다.
* 시드 메서드가 실행되도록 앱을 강제로 초기화합니다(`Startup` 클래스에서 메서드 호출). 초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다. 다음 중 한 가지 방법을 사용하여 이를 수행할 수 있습니다.

  * 알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지** 를 누릅니다.

    ![IIS Express 시스템 트레이 아이콘](working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](working-with-sql/_static/stopIIS.png)

    * 비 디버그 모드에서 VS를 실행했다면 F5 키를 눌러 디버그 모드에서 실행합니다.
    * 디버그 모드에서 VS를 실행했다면 디버거를 중지하고 F5 키를 누릅니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

DB의 모든 레코드를 삭제합니다(시드 메서드가 실행되도록). 앱을 중지 및 시작하여 데이터베이스를 시드합니다.

---

앱이 시드된 데이터를 보여줍니다.

![영화 데이터를 표시하는 Microsoft Edge에서 열린 MVC 영화 애플리케이션](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> [이전](adding-model.md)
> [다음](controller-methods-views.md)
::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

`MvcMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다. 데이터베이스 컨텍스트는 *Startup.cs* 파일의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString`을 읽습니다. 로컬 개발의 경우 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString`을 읽습니다. 로컬 개발의 경우 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

테스트 또는 프로덕션 서버에 앱을 배포할 때 환경 변수 또는 다른 방법을 사용하여 실제 SQL Server에 연결 문자열을 설정할 수 있습니다. 자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB는 프로그램 개발을 위한 SQL Server Express 데이터베이스 엔진의 간단 버전입니다. LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다. 기본적으로 LocalDB 데이터베이스는 *C:/Users/{user}* 디렉터리에 *.mdf* 파일을 만듭니다.

* **보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.

  ![보기 메뉴](working-with-sql/_static/ssox.png)

* 마우스 오른쪽 단추로 `Movie` 테이블 **> 디자이너 보기** 를 클릭합니다.

  ![Movie 테이블에서 열린 상황에 맞는 메뉴](working-with-sql/_static/design.png)

  ![디자이너에 열린 Movie 테이블](working-with-sql/_static/dv.png)

`ID` 옆의 키 아이콘을 확인합니다. 기본적으로 EF는 `ID`라는 속성을 기본 키로 만듭니다.

* 마우스 오른쪽 단추로 `Movie` 테이블 **> 데이터 보기** 를 클릭합니다.

  ![Movie 테이블에서 열린 상황에 맞는 메뉴](working-with-sql/_static/ssox2.png)

  ![테이블 데이터를 보여 주는 열린 Movie 테이블](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>데이터베이스 시드

*Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다. 생성된 코드를 다음으로 바꿉니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

DB에 영화가 존재할 경우 시드 이니셜라이저가 반환되고 영화가 추가되지 않습니다.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>시드 이니셜라이저 추가

*Program.cs* 의 내용을 다음 코드로 바꿉니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

앱을 테스트합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* DB의 모든 레코드를 삭제합니다. 브라우저에서 삭제 링크를 사용하거나 SSOX에서 이를 수행할 수 있습니다.
* 시드 메서드가 실행되도록 앱을 강제로 초기화합니다(`Startup` 클래스에서 메서드 호출). 초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다. 다음 중 한 가지 방법을 사용하여 이를 수행할 수 있습니다.

  * 알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지** 를 누릅니다.

    ![IIS Express 시스템 트레이 아이콘](working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](working-with-sql/_static/stopIIS.png)

    * 비 디버그 모드에서 VS를 실행했다면 F5 키를 눌러 디버그 모드에서 실행합니다.
    * 디버그 모드에서 VS를 실행했다면 디버거를 중지하고 F5 키를 누릅니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

DB의 모든 레코드를 삭제합니다(시드 메서드가 실행되도록). 앱을 중지 및 시작하여 데이터베이스를 시드합니다.

---

앱이 시드된 데이터를 보여줍니다.

![영화 데이터를 표시하는 Microsoft Edge에서 열린 MVC 영화 애플리케이션](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> [이전](adding-model.md)
> [다음](controller-methods-views.md)

::: moniker-end
