---
title: 4부. 데이터베이스 및 ASP.NET Core 작업
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 4부입니다.
ms.author: riande
ms.date: 7/22/2019
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: d592cf7d8a96a7e4ec2e53418843a186488951be
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058157"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a>4부. 데이터베이스 및 ASP.NET Core 작업

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

`RazorPagesMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다. 데이터베이스 컨텍스트는 *Startup.cs* 의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString`을 읽습니다. 로컬 개발의 경우 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

데이터베이스의 이름 값(`Database={Database name}`)은 생성된 코드에 따라 달라집니다. 이름 값은 임의적입니다.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

앱이 테스트 또는 프로덕션 서버에 배포되는 경우 환경 변수를 사용하여 연결 문자열을 실제 데이터베이스 서버로 설정할 수 있습니다. 자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB는 프로그램 개발용으로 대상이 지정된 간단한 버전의 SQL Server Express 데이터베이스 엔진입니다. LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다. 기본적으로 LocalDB 데이터베이스는 `C:\Users\<user>\` 디렉터리에서 `*.mdf` 파일을 만듭니다.

<a name="ssox"></a>
* **보기** 메뉴에서 SSOX( **SQL Server 개체 탐색기** )를 엽니다.

  ![보기 메뉴](sql/_static/ssox.png)

* `Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기** 를 선택합니다.

  ![동영상 테이블의 열린 바로 가기 메뉴](sql/_static/design.png)

  ![디자이너에서 열린 동영상 테이블](sql/_static/dv.png)

`ID` 옆의 키 아이콘을 확인합니다. 기본적으로 EF는 기본 키에 대해 `ID`라는 속성을 만듭니다.

* `Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택합니다.

  ![테이블 데이터를 보여 주는 열린 Movie 테이블](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>데이터베이스 시드

다음 코드를 사용하여 *Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

DB에 영화가 존재할 경우 시드 이니셜라이저가 반환되고 영화가 추가되지 않습니다.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>시드 이니셜라이저 추가

*Program.cs* 에서 다음을 수행하는 `Main` 메서드를 수정합니다.

* 종속성 주입 컨테이너에서 DB 컨텍스트 인스턴스를 가져옵니다.
* 컨텍스트를 전달하는 시드 메서드를 호출합니다.
* 시드 메서드가 완료되면 컨텍스트를 삭제합니다.

다음 코드는 업데이트된 *Program.cs* 파일을 보여 줍니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

`Update-Database`가 실행되지 않은 경우 다음 예외가 발생합니다.

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>앱을 테스트합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* DB의 모든 레코드를 삭제합니다. 브라우저 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.
* 시드 메서드가 실행되도록 앱을 강제로 초기화합니다(`Startup` 클래스에서 메서드 호출). 초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다. 다음 중 한 가지 방법을 사용하여 이를 수행할 수 있습니다.

  * 알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지** 를 탭합니다.

    ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](sql/_static/stopIIS.png)

    * 비디버그 모드에서 VS를 실행했던 경우 F5 키를 눌러 디버그 모드에서 실행되도록 합니다.
    * 디버그 모드에서 VS를 실행했던 경우 디버거를 중지하고 F5 키를 누릅니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

DB의 모든 레코드를 삭제합니다(시드 메서드가 실행되도록). 앱을 중지 및 시작하여 데이터베이스를 시드합니다.

앱이 시드된 데이터를 보여줍니다.

---

다음 자습서는 데이터의 표현을 개선합니다.

## <a name="additional-resources"></a>추가 자료

> [!div class="step-by-step"]
> [이전: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)
> [다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

`RazorPagesMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다. 데이터베이스 컨텍스트는 *Startup.cs* 의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

`ConfigureServices`에서 사용되는 메서드에 대한 자세한 내용은 다음을 참조하세요.

* `CookiePolicyOptions`에 대한 [EU GDPR(일반 데이터 보호 규정) 지원](xref:security/gdpr)
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString`을 읽습니다. 로컬 개발의 경우 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

데이터베이스의 이름 값(`Database={Database name}`)은 생성된 코드에 따라 달라집니다. 이름 값은 임의적입니다.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

앱이 테스트 또는 프로덕션 서버에 배포되는 경우 환경 변수를 사용하여 연결 문자열을 실제 데이터베이스 서버로 설정할 수 있습니다. 자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB는 프로그램 개발용으로 대상이 지정된 간단한 버전의 SQL Server Express 데이터베이스 엔진입니다. LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다. 기본적으로 LocalDB 데이터베이스는 `C:/Users/<user/>` 디렉터리에서 `*.mdf` 파일을 만듭니다.

<a name="ssox"></a>
* **보기** 메뉴에서 SSOX( **SQL Server 개체 탐색기** )를 엽니다.

  ![보기 메뉴](sql/_static/ssox.png)

* `Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기** 를 선택합니다.

  ![Movie 테이블에서 열린 상황에 맞는 메뉴](sql/_static/design.png)

  ![디자이너에 열린 Movie 테이블](sql/_static/dv.png)

`ID` 옆의 키 아이콘을 확인합니다. 기본적으로 EF는 기본 키에 대해 `ID`라는 속성을 만듭니다.

* `Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택합니다.

  ![테이블 데이터를 보여 주는 열린 Movie 테이블](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>데이터베이스 시드

다음 코드를 사용하여 *Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

DB에 영화가 존재할 경우 시드 이니셜라이저가 반환되고 영화가 추가되지 않습니다.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>시드 이니셜라이저 추가

*Program.cs* 에서 다음을 수행하는 `Main` 메서드를 수정합니다.

* 종속성 주입 컨테이너에서 DB 컨텍스트 인스턴스를 가져옵니다.
* 컨텍스트를 전달하는 시드 메서드를 호출합니다.
* 시드 메서드가 완료되면 컨텍스트를 삭제합니다.

다음 코드는 업데이트된 *Program.cs* 파일을 보여 줍니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

프로덕션 앱은 `Database.Migrate`를 호출하지 않습니다. `Update-Database`가 실행되지 않는 경우 다음 예외를 방지하기 위해 위의 코드에 추가됩니다.

SqlException: 로그인에서 요청한 “RazorPagesMovieContext-21” 데이터베이스를 열 수 없습니다. 로그인에 실패했습니다.
'user name' 사용자에 대한 로그인에 실패했습니다.

### <a name="test-the-app"></a>앱을 테스트합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* DB의 모든 레코드를 삭제합니다. 브라우저 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.
* 시드 메서드가 실행되도록 앱을 강제로 초기화합니다(`Startup` 클래스에서 메서드 호출). 초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다. 다음 중 한 가지 방법을 사용하여 이를 수행할 수 있습니다.

  * 알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지** 를 탭합니다.

    ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](sql/_static/stopIIS.png)

    * 비디버그 모드에서 VS를 실행했던 경우 F5 키를 눌러 디버그 모드에서 실행되도록 합니다.
    * 디버그 모드에서 VS를 실행했던 경우 디버거를 중지하고 F5 키를 누릅니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

DB의 모든 레코드를 삭제합니다(시드 메서드가 실행되도록). 앱을 중지 및 시작하여 데이터베이스를 시드합니다.

앱이 시드된 데이터를 보여줍니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

DB의 모든 레코드를 삭제합니다(시드 메서드가 실행되도록). 앱을 중지 및 시작하여 데이터베이스를 시드합니다.

앱이 시드된 데이터를 보여줍니다.

---

앱에서 시드된 데이터를 보여 줍니다.

![동영상 데이터를 표시하는 크롬에서 열린 동영상 애플리케이션](sql/_static/m55.png)

다음 자습서는 데이터의 표현을 정리합니다.

## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [이전: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)
> [다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)

::: moniker-end
