---
title: '4부: 데이터베이스 작업'
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 4부입니다.
ms.author: riande
ms.date: 09/26/2020
no-loc:
- Index
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
ms.openlocfilehash: 6627825239d815d2c8bcad98e22340f07f921ab9
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96420021"
---
# <a name="part-4-of-tutorial-series-on-no-locrazor-pages"></a>Razor Pages에 대한 자습서 시리즈의 4부

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-5.0"

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

`RazorPagesMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다. 데이터베이스 컨텍스트는 *Startup.cs* 의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString` 키를 읽습니다. 로컬 개발의 경우 구성은 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

생성된 연결 문자열은 다음과 유사합니다.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

앱이 테스트 또는 프로덕션 서버에 배포되는 경우 환경 변수를 사용하여 연결 문자열을 테스트 또는 프로덕션 데이터베이스 서버로 설정할 수 있습니다. 자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB는 프로그램 개발용으로 대상이 지정된 간단한 버전의 SQL Server Express 데이터베이스 엔진입니다. LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다. 기본적으로 LocalDB 데이터베이스는 `C:\Users\<user>\` 디렉터리에서 `*.mdf` 파일을 만듭니다.

<a name="ssox"></a>
1. **보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.

   ![보기 메뉴](sql/_static/5/ssox.png)

1. `Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기** 를 선택합니다.

   ![동영상 테이블의 열린 바로 가기 메뉴](sql/_static/5/design.png)

   ![디자이너에서 열린 동영상 테이블](sql/_static/dv.png)

   `ID` 옆의 키 아이콘을 확인합니다. 기본적으로 EF는 기본 키에 대해 `ID`라는 속성을 만듭니다.

1. `Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택합니다.

   ![테이블 데이터를 보여 주는 열린 Movie 테이블](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

[SQLite](https://www.sqlite.org/) 웹 사이트 상태:

> SQLite는 신뢰성 높고 통합 기능을 제공하는 자체 포함된 임베디드 공용 도메인 SQL 데이터베이스 엔진입니다. SQLite는 전 세계에서 가장 널리 사용되는 데이터베이스 엔진입니다.

여러 타사 도구를 다운로드하여 SQLite 데이터베이스를 관리하고 볼 수 있습니다. 다음은 [DB Browser for SQLite](https://sqlitebrowser.org/)의 이미지입니다. 다른 SQLite 도구를 사용할 경우 그에 관한 의견을 남겨 주세요.

![영화 데이터베이스를 보여 주는 DB Browser for SQLite](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> 이 자습서에서는 가능한 경우 Entity Framework Core 마이그레이션 기능을 사용합니다. 마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 수정합니다. 그러나 마이그레이션은 EF Core 공급자가 지원하는 유형의 변경만 수행할 수 있으며 SQLite 공급자의 기능은 제한적입니다. 예를 들어 열 추가는 지원되지만 열 제거 또는 변경은 지원되지 않습니다. 열을 제거 또는 변경하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다. 이러한 제한 때문에 이 자습서에서는 SQLite 스키마 변경에 대한 마이그레이션을 사용하지 않습니다. 대신 스키마가 변경되면 데이터베이스가 삭제되고 다시 만들어집니다.
>
>SQLite 제한 사항에 대한 해결 방법은 테이블의 내용이 변경되면 테이블을 다시 빌드하기 위해 수동으로 마이그레이션 코드를 작성하는 것입니다. 테이블 다시 빌드에는 다음이 포함됩니다.
>
>* 새 테이블 만들기.
>* 이전 테이블에서 새 테이블로 데이터 복사.
>* 이전 테이블 삭제.
>* 새 테이블 이름 바꾸기.
>
>자세한 내용은 다음 자료를 참조하세요.
> * [SQLite EF Core 데이터베이스 공급자 제한 사항](/ef/core/providers/sqlite/limitations)
> * [마이그레이션 코드 사용자 지정](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [데이터 시드](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 문](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>데이터베이스 시드

다음 코드를 사용하여 *Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

데이터베이스에 영화가 있는 경우 시드 이니셜라이저가 반환되고 영화가 추가되지 않습니다.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>시드 이니셜라이저 추가

*Program.cs* 의 내용을 다음 코드로 바꿉니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

위의 코드에서 다음을 수행하도록 `Main` 메서드가 수정되었습니다.

* 종속성 주입 컨테이너에서 데이터베이스 컨텍스트 인스턴스를 가져옵니다.
* `seedData.Initialize` 메서드를 호출하여 데이터베이스 컨텍스트 인스턴스에 전달합니다.
* 시드 메서드가 완료되면 컨텍스트를 삭제합니다. [using 문](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)은 컨텍스트가 삭제되도록 합니다.

`Update-Database`가 실행되지 않은 경우 다음 예외가 발생합니다.

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>앱을 테스트합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 데이터베이스의 모든 레코드를 삭제합니다. 브라우저에서 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용합니다.

1. `Startup` 클래스에서 메서드를 호출하여 앱을 강제로 초기화하면 시드 메서드가 실행됩니다. 초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다. 다음 중 한 가지 방법을 사용하여 IIS를 중지했다가 다시 시작합니다.

   1. 알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지** 를 선택합니다.

      ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![상황에 맞는 메뉴](sql/_static/stopIIS.png)

   1. 앱이 비디버그 모드에서 실행 중인 경우 <kbd>F5</kbd>를 눌러 디버그 모드에서 실행합니다.
   1. 앱이 디버그 모드인 경우 디버거를 중지하고 <kbd>F5</kbd>를 누릅니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

데이터베이스의 모든 레코드를 삭제하면 시드 메서드가 실행됩니다. 앱을 중지 및 시작하여 데이터베이스를 시드합니다.

---

앱에서 시드된 데이터를 보여 줍니다.

![영화 데이터를 표시하는 Movie 애플리케이션이 열린 브라우저](sql/_static/5/m55.png)

## <a name="additional-resources"></a>추가 리소스

> [!div class="step-by-step"]
> [이전: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)
> [다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

`RazorPagesMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다. 데이터베이스 컨텍스트는 *Startup.cs* 의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString` 키를 읽습니다. 로컬 개발의 경우 구성은 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

생성된 연결 문자열은 다음과 유사합니다.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

앱이 테스트 또는 프로덕션 서버에 배포되는 경우 환경 변수를 사용하여 연결 문자열을 테스트 또는 프로덕션 데이터베이스 서버로 설정할 수 있습니다. 자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB는 프로그램 개발용으로 대상이 지정된 간단한 버전의 SQL Server Express 데이터베이스 엔진입니다. LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다. 기본적으로 LocalDB 데이터베이스는 `C:\Users\<user>\` 디렉터리에서 `*.mdf` 파일을 만듭니다.

<a name="ssox"></a>
* **보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.

  ![보기 메뉴](sql/_static/ssox.png)

* `Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기** 를 선택합니다.

  ![동영상 테이블의 열린 바로 가기 메뉴](sql/_static/design.png)

  ![디자이너에서 열린 동영상 테이블](sql/_static/dv.png)

`ID` 옆의 키 아이콘을 확인합니다. 기본적으로 EF는 기본 키에 대해 `ID`라는 속성을 만듭니다.

* `Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택합니다.

  ![테이블 데이터를 보여 주는 열린 Movie 테이블](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

[SQLite](https://www.sqlite.org/) 웹 사이트 상태:

> SQLite는 신뢰성 높고 통합 기능을 제공하는 자체 포함된 임베디드 공용 도메인 SQL 데이터베이스 엔진입니다. SQLite는 전 세계에서 가장 널리 사용되는 데이터베이스 엔진입니다.

여러 타사 도구를 다운로드하여 SQLite 데이터베이스를 관리하고 볼 수 있습니다. 다음은 [DB Browser for SQLite](https://sqlitebrowser.org/)의 이미지입니다. 다른 SQLite 도구를 사용할 경우 그에 관한 의견을 남겨 주세요.

![영화 데이터베이스를 보여 주는 DB Browser for SQLite](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> 이 자습서에서는 가능한 경우 Entity Framework Core 마이그레이션 기능을 사용합니다. 마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 수정합니다. 그러나 마이그레이션은 EF Core 공급자가 지원하는 유형의 변경만 수행할 수 있으며 SQLite 공급자의 기능은 제한적입니다. 예를 들어 열 추가는 지원되지만 열 제거 또는 변경은 지원되지 않습니다. 열을 제거 또는 변경하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다. 이러한 제한 때문에 이 자습서에서는 SQLite 스키마 변경에 대한 마이그레이션을 사용하지 않습니다. 대신 스키마가 변경되면 데이터베이스가 삭제되고 다시 만들어집니다.
>
>SQLite 제한 사항에 대한 해결 방법은 테이블의 내용이 변경되면 테이블을 다시 빌드하기 위해 수동으로 마이그레이션 코드를 작성하는 것입니다. 테이블 다시 빌드에는 다음이 포함됩니다.
>
>* 새 테이블 만들기.
>* 이전 테이블에서 새 테이블로 데이터 복사.
>* 이전 테이블 삭제.
>* 새 테이블 이름 바꾸기.
>
>자세한 내용은 다음 자료를 참조하세요.
> * [SQLite EF Core 데이터베이스 공급자 제한 사항](/ef/core/providers/sqlite/limitations)
> * [마이그레이션 코드 사용자 지정](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [데이터 시드](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 문](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>데이터베이스 시드

다음 코드를 사용하여 *Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

데이터베이스에 영화가 있는 경우 시드 이니셜라이저가 반환되고 영화가 추가되지 않습니다.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>시드 이니셜라이저 추가

*Program.cs* 의 내용을 다음 코드로 바꿉니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

위의 코드에서 다음을 수행하도록 `Main` 메서드가 수정되었습니다.

* 종속성 주입 컨테이너에서 데이터베이스 컨텍스트 인스턴스를 가져옵니다.
* `seedData.Initialize` 메서드를 호출하여 데이터베이스 컨텍스트 인스턴스에 전달합니다.
* 시드 메서드가 완료되면 컨텍스트를 삭제합니다. [using 문](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)은 컨텍스트가 삭제되도록 합니다.

`Update-Database`가 실행되지 않은 경우 다음 예외가 발생합니다.

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>앱을 테스트합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 데이터베이스의 모든 레코드를 삭제합니다. 브라우저에서 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용합니다.
* `Startup` 클래스에서 메서드를 호출하여 앱을 강제로 초기화하면 시드 메서드가 실행됩니다. 초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다. 다음 중 한 가지 방법을 사용하여 IIS를 중지했다가 다시 시작합니다.

  * 알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지** 를 탭합니다.

    ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](sql/_static/stopIIS.png)

    * 앱이 비디버그 모드에서 실행 중인 경우 <kbd>F5</kbd>를 눌러 디버그 모드에서 실행합니다.
    * 앱이 디버그 모드인 경우 디버거를 중지하고 <kbd>F5</kbd>를 누릅니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

데이터베이스의 모든 레코드를 삭제하면 시드 메서드가 실행됩니다. 앱을 중지 및 시작하여 데이터베이스를 시드합니다.

---

앱에서 시드된 데이터를 보여 줍니다.

![동영상 데이터를 표시하는 크롬에서 열린 동영상 애플리케이션](sql/_static/m55https.png)

## <a name="additional-resources"></a>추가 리소스

> [!div class="step-by-step"]
> [이전: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)
> [다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

`RazorPagesMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다. 데이터베이스 컨텍스트는 *Startup.cs* 의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

`ConfigureServices`에서 사용되는 메서드에 대한 자세한 내용은 다음을 참조하세요.

* `CookiePolicyOptions`에 대한 [EU GDPR(일반 데이터 보호 규정) 지원](xref:security/gdpr)
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString` 키를 읽습니다. 로컬 개발의 경우 구성은 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

생성된 연결 문자열은 다음과 유사합니다.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

앱이 테스트 또는 프로덕션 서버에 배포되는 경우 환경 변수를 사용하여 연결 문자열을 테스트 또는 프로덕션 데이터베이스 서버로 설정할 수 있습니다. 자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB는 프로그램 개발용으로 대상이 지정된 간단한 버전의 SQL Server Express 데이터베이스 엔진입니다. LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다. 기본적으로 LocalDB 데이터베이스는 `C:/Users/<user/>` 디렉터리에서 `*.mdf` 파일을 만듭니다.

<a name="ssox"></a>
* **보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.

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

데이터베이스에 영화가 있는 경우 시드 이니셜라이저가 반환되고 영화가 추가되지 않습니다.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>시드 이니셜라이저 추가

*Program.cs* 의 내용을 다음 코드로 바꿉니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

위의 코드에서 다음을 수행하도록 `Main` 메서드가 수정되었습니다.

* 종속성 주입 컨테이너에서 데이터베이스 컨텍스트 인스턴스를 가져옵니다.
* `seedData.Initialize` 메서드를 호출하여 데이터베이스 컨텍스트 인스턴스에 전달합니다.
* 시드 메서드가 완료되면 컨텍스트를 삭제합니다. [using 문](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)은 컨텍스트가 삭제되도록 합니다.

프로덕션 앱은 `Database.Migrate`를 호출하지 않습니다. `Update-Database`가 실행되지 않는 경우 다음 예외를 방지하기 위해 위의 코드에 추가됩니다.

SqlException: 로그인에서 요청한 “RazorPagesMovieContext-21” 데이터베이스를 열 수 없습니다. 로그인에 실패했습니다.
'user name' 사용자에 대한 로그인에 실패했습니다.

### <a name="test-the-app"></a>앱을 테스트합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 데이터베이스의 모든 레코드를 삭제합니다. 브라우저 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.
* `Startup` 클래스에서 메서드를 호출하여 앱을 강제로 초기화하면 시드 메서드가 실행됩니다. 초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다. 다음 중 한 가지 방법을 사용하여 이를 수행할 수 있습니다.

  * 알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지** 를 탭합니다.

    ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](sql/_static/stopIIS.png)

    * 앱이 비디버그 모드에서 실행 중인 경우 <kbd>F5</kbd>를 눌러 디버그 모드에서 실행합니다.
    * 앱이 디버그 모드인 경우 디버거를 중지하고 <kbd>F5</kbd>를 누릅니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

데이터베이스의 모든 레코드를 삭제하면 시드 메서드가 실행됩니다. 앱을 중지 및 시작하여 데이터베이스를 시드합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

데이터베이스의 모든 레코드를 삭제하면 시드 메서드가 실행됩니다. 앱을 중지 및 시작하여 데이터베이스를 시드합니다.

---

앱에서 시드된 데이터를 보여 줍니다.

![동영상 데이터를 표시하는 크롬에서 열린 동영상 애플리케이션](sql/_static/m55https.png)

다음 자습서는 데이터의 표현을 정리합니다.

## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [이전: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)
> [다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)

::: moniker-end
