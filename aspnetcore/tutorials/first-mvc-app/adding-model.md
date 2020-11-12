---
title: 4부. ASP.NET Core MVC 앱에 모델 추가
author: rick-anderson
description: ASP.NET Core MVC에 대한 자습서 시리즈의 4부입니다.
ms.author: riande
ms.date: 01/13/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fa1d79bed56f17afe69697a7e24ec200e6a0ab22
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422724"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a>4부. ASP.NET Core MVC 앱에 모델 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Tom Dykstra](https://github.com/tdykstra)

이 섹션에서는 데이터베이스에서 영화를 관리하기 위한 클래스를 추가합니다. 이러한 클래스는 **M** VC 앱의 " **M** odel" 부분이 됩니다.

이러한 클래스를 EF Core([Entity Framework Core](/ef/core))와 함께 사용하여 데이터베이스 작업을 수행합니다. EF Core는 작성해야 할 데이터 액세스 코드를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.

직접 만들게 될 모델 클래스는 EF Core에 대한 어떠한 종속성도 없으므로 POCO( **P** lain **O** ld **C** LR **O** bject) 클래스로 알려져 있습니다. 이 클래스는 데이터베이스에 저장될 데이터의 속성만 정의합니다.

이 자습서에서는 먼저 모델 클래스를 작성하고 EF Core가 데이터베이스를 만듭니다.

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a>데이터 모델 클래스 추가

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Models* 폴더> **추가** > **클래스** 를 마우스 오른쪽 단추로 클릭합니다. 파일 이름을 *Movie.cs* 로 지정합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

*Movie.cs* 파일을 *Models* 폴더에 추가합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 클래스** > **빈 클래스** 를 클릭합니다. 파일 이름을 *Movie.cs* 로 지정합니다.

---

다음 코드로 *Movie.cs* 파일을 수정합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

`Movie` 클래스에는 데이터베이스에서 기본 키에 필요한 `Id` 필드가 포함되어 있습니다.

`ReleaseDate`의 <xref:System.ComponentModel.DataAnnotations.DataType> 특성은 날짜 형식(`Date`)을 지정합니다. 이 특성을 사용하면:

* 사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.
* 시간 정보 없이 날짜만 표시됩니다.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)는 이후 자습서에서 다룹니다.

## <a name="add-nuget-packages"></a>NuGet 패키지 추가

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.

![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

PMC에서 다음 명령을 실행합니다.

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

앞의 명령은 EF Core SQL Server 공급자를 추가합니다. 이 공급자 패키지는 EF Core 패키지를 종속성으로 설치합니다. 추가적인 패키지는 자습서 뒷부분의 스캐폴딩 단계에서 자동으로 설치됩니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

**프로젝트** 메뉴에서 **NuGet 패키지 관리** 를 선택합니다.

오른쪽 위의 **검색** 필드에 `Microsoft.EntityFrameworkCore.SQLite`를 입력하고 **반환** 키를 눌러 검색합니다. 일치하는 NuGet 패키지를 선택하고 **패키지 추가** 단추를 누릅니다.

![Entity Framework Core NuGet 패키지 추가](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

`MvcMovie` 프로젝트가 선택된 **프로젝트 선택** 대화 상자가 표시됩니다. **확인** 단추를 누릅니다.

**라이선스 승인** 대화 상자가 나타납니다. 필요에 따라 라이선스를 검토한 다음 **수락** 단추를 클릭합니다.

위의 단계를 반복하여 다음 NuGet 패키지를 설치합니다.

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>데이터베이스 컨텍스트 클래스 만들기

`Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제)을 조정하려면 데이터베이스 컨텍스트 클래스가 필요합니다. 데이터베이스 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생되며 데이터 모델에 포함할 엔터티를 지정합니다.

*Data* 폴더를 만듭니다.

다음 코드로 *Data/MvcMovieContext.cs* 파일을 추가합니다. 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다. Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다. 엔터티는 테이블의 행에 해당합니다.

<a name="reg"></a>

## <a name="register-the-database-context"></a>데이터베이스 컨텍스트 등록

ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 만들어집니다. 서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록되어야 합니다. 이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다. DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다. 이 섹션에서는 DI 컨테이너에 데이터베이스 컨텍스트를 등록합니다.

*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

`Startup.ConfigureServices`에 다음 강조 표시된 코드를 추가합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다. 로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>데이터베이스 연결 문자열 추가

*appsettings.json* 파일에 연결 문자열을 추가합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.

## <a name="scaffold-movie-pages"></a>영화 페이지 스캐폴드

스캐폴딩 도구를 사용하여 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 페이지를 생성합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**솔루션 탐색기** 에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.

![위 단계의 보기](adding-model/_static/add_controller21.png)

**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용** 을 선택합니다.

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

**컨트롤러 추가** 대화 상자를 입력합니다.

* **모델 클래스:** *Movie(MvcMovie.Models)*
* **데이터 컨텍스트 클래스:** *MvcMovieContext(MvcMovie.Data)*

![데이터 컨텍스트 추가](adding-model/_static/dc5.png)

* **보기:** 확인된 각 옵션의 기본값 선택 유지
* **컨트롤러 이름:** 기본값 *MoviesController* 유지
* **추가** 를 선택합니다.

Visual Studio가 다음을 만듭니다

* 영화 컨트롤러( *Controllers/MoviesController.cs* )
* Create, Delete, Details, Edit, 및 Index 페이지에 대한 Razor 뷰 파일( *Views/Movies/\*.cshtml* )

이러한 파일의 자동 생성을 *스캐폴딩* 이라고 합니다.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* 프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.

* Linux에서는 스캐폴드 도구 경로를 내보냅니다.

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* 프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.

* 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

데이터베이스가 없으므로 아직 스캐폴드된 페이지를 사용할 수 없습니다. 앱을 실행하고 **Movie App** 링크를 클릭하면 *Cannot open database* 또는 *no such table: Movie 오류 메시지가 표시됩니다.* Movie’ 오류 메시지가 표시됩니다.

<a name="migration"></a>

## <a name="initial-migration"></a>초기 마이그레이션

EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용하여 데이터베이스를 만듭니다. 마이그레이션은 데이터 모델과 일치하도록 데이터베이스를 만들고 수정할 수 있는 도구 모음입니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.

PMC에서 다음 명령을 입력합니다.

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다. `InitialCreate` 인수는 마이그레이션 이름입니다. 모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다. 이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다. 데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.

* `Update-Database`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다. 이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.

  데이터베이스 수정 명령은 다음 경고를 생성합니다. 

  > No type was specified for the decimal column ‘Price’ on entity type ‘Movie’. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using ‘HasColumnType()’.

  이 경고는 무시할 수 있으며 자습서의 뒷부분에서 수정될 예정입니다.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

다음 .NET Core CLI 명령을 실행합니다.

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다. `InitialCreate` 인수는 마이그레이션 이름입니다. 모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다. 이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다. 데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일의 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.

* `ef database update`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다. 이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.

---

### <a name="the-initialcreate-class"></a>InitialCreate 클래스

*Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 확인합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

`Up` 메서드는 Movie 테이블을 만들고 `Id`를 기본 키로 구성합니다. `Down` 메서드는 `Up` 마이그레이션에 의해 변경된 스키마를 되돌립니다.

<a name="test"></a>

## <a name="test-the-app"></a>앱을 테스트합니다.

* 앱을 실행하고 **Movie App** 링크를 클릭합니다.

  다음 중 하나와 비슷한 예외가 발생할 경우:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  [마이그레이션 단계](#migration)를 누락했을 수 있습니다.

* **Create** 페이지를 테스트합니다. 데이터를 입력하고 제출합니다.

  > [!NOTE]
  > `Price` 필드에 소수점을 입력하지 못할 수도 있습니다. 소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다. 세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.

* **Edit** , **Details** 및 **Delete** 링크를 테스트합니다.

## <a name="dependency-injection-in-the-controller"></a>컨트롤러에서 종속성 주입

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Controllers/MoviesController.cs* 파일을 열고 생성자를 확인합니다.

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다. 컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다. 컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용

SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다. 다음 코드에서는 시작에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여줍니다. `IWebHostEnvironment`는 `ConfigureServices`가 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 주입됩니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>강력한 형식의 모델 및 @model 키워드

이 자습서의 앞부분에서는 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 보기에 전달하는 방법을 살펴봤습니다. `ViewData` 사전은 정보를 보기에 전달하기 위한 편리한 런타임 바인딩 방법을 제공하는 동적 개체입니다.

또한 MVC는 보기에 강력한 형식의 모델 개체를 전달하는 기능도 제공합니다. 강력한 형식의 방법을 사용하면 컴파일 시에 코드 검사를 수행할 수 있습니다. 스캐폴딩 메커니즘은 `MoviesController` 클래스 및 보기에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용합니다.

*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 확인합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

일반적으로 `id` 매개 변수는 경로 데이터 변수로 전달됩니다. 예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.

* 컨트롤러를 `movies` 컨트롤러로 설정(첫 번째 URL 세그먼트)
* 작업을 `details`로 설정(두 번째 URL 세그먼트)
* ID를 1로 설정(마지막 URL 세그먼트)

다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수도 있습니다.

`https://localhost:5001/movies/details?id=1`

ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.

[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

영화를 찾으면 `Movie` 모델의 인스턴스를 `Details` 보기에 전달합니다.

```csharp
return View(movie);
```

*Views/Movies/Details.cshtml* 파일의 내용을 확인합니다.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

보기 파일 맨 위에 있는 `@model` 문은 보기에 필요한 개체 형식을 지정합니다. 영화 컨트롤러가 만들어질 때 다음 `@model` 문이 포함됩니다.

```cshtml
@model MvcMovie.Models.Movie
```

`@model` 지시문을 사용하면 컨트롤러가 보기에 전달한 영화에 액세스할 수 있습니다. `Model` 개체는 강력한 형식입니다. 예를 들어 *Details.cshtml* 보기의 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다. 또한 `Create` 및 `Edit` 메서드와 보기도 `Movie` 모델 개체를 전달합니다.

*Index.cshtml* 보기 및 영화 컨트롤러의 `Index` 메서드를 확인합니다. 코드가 `View` 메서드를 호출할 때 `List` 개체를 생성하는 방법에 유의하세요. 이 코드는 `Index` 작업 메서드에서 보기로 `Movies` 목록을 전달합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

영화 컨트롤러가 만들어질 때 스캐폴딩은 다음 `@model` 문을 *Index.cshtml* 파일의 맨 위에 포함시킵니다.

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 보기에 전달한 영화 목록에 액세스할 수 있습니다. 예를 들어 *Index.cshtml* 보기에서 코드는 강력한 형식의 `Model` 개체에 대해 `foreach` 문을 사용하여 영화를 반복합니다.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie` 형식입니다. 즉, 여러 가지 이점 중에서도 코드의 컴파일 시 검사를 수행할 수 있다는 뜻입니다.

## <a name="additional-resources"></a>추가 자료

* [태그 도우미](xref:mvc/views/tag-helpers/intro)
* [세계화 및 지역화](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [이전 보기 추가](adding-view.md)
> [다음 SQL 사용](working-with-sql.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a>데이터 모델 클래스 추가

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Models* 폴더> **추가** > **클래스** 를 마우스 오른쪽 단추로 클릭합니다. 파일 이름을 *Movie.cs* 로 지정합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

*Movie.cs* 파일을 *Models* 폴더에 추가합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 클래스** > **빈 클래스** 를 클릭합니다. 파일 이름을 *Movie.cs* 로 지정합니다.

---

다음 코드로 *Movie.cs* 파일을 수정합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

`Movie` 클래스에는 데이터베이스에서 기본 키에 필요한 `Id` 필드가 포함되어 있습니다.

`ReleaseDate`의 <xref:System.ComponentModel.DataAnnotations.DataType> 특성은 날짜 형식(`Date`)을 지정합니다. 이 특성을 사용하면:

* 사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.
* 시간 정보 없이 날짜만 표시됩니다.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)는 이후 자습서에서 다룹니다.

## <a name="add-nuget-packages"></a>NuGet 패키지 추가

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.

![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

PMC에서 다음 명령을 실행합니다.

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

앞의 명령은 EF Core SQL Server 공급자를 추가합니다. 이 공급자 패키지는 EF Core 패키지를 종속성으로 설치합니다. 추가적인 패키지는 자습서 뒷부분의 스캐폴딩 단계에서 자동으로 설치됩니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

**프로젝트** 메뉴에서 **NuGet 패키지 관리** 를 선택합니다.

오른쪽 위의 **검색** 필드에 `Microsoft.EntityFrameworkCore.SQLite`를 입력하고 **반환** 키를 눌러 검색합니다. 일치하는 NuGet 패키지를 선택하고 **패키지 추가** 단추를 누릅니다.

![Entity Framework Core NuGet 패키지 추가](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

`MvcMovie` 프로젝트가 선택된 **프로젝트 선택** 대화 상자가 표시됩니다. **확인** 단추를 누릅니다.

**라이선스 승인** 대화 상자가 나타납니다. 필요에 따라 라이선스를 검토한 다음 **수락** 단추를 클릭합니다.

위의 단계를 반복하여 다음 NuGet 패키지를 설치합니다.

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>데이터베이스 컨텍스트 클래스 만들기

`Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제)을 조정하려면 데이터베이스 컨텍스트 클래스가 필요합니다. 데이터베이스 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생되며 데이터 모델에 포함할 엔터티를 지정합니다.

*Data* 폴더를 만듭니다.

다음 코드로 *Data/MvcMovieContext.cs* 파일을 추가합니다. 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다. Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다. 엔터티는 테이블의 행에 해당합니다.

<a name="reg"></a>

## <a name="register-the-database-context"></a>데이터베이스 컨텍스트 등록

ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 만들어집니다. 서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록되어야 합니다. 이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다. DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다. 이 섹션에서는 DI 컨테이너에 데이터베이스 컨텍스트를 등록합니다.

*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

`Startup.ConfigureServices`에 다음 강조 표시된 코드를 추가합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다. 로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>데이터베이스 연결 문자열 추가

*appsettings.json* 파일에 연결 문자열을 추가합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.

## <a name="scaffold-movie-pages"></a>영화 페이지 스캐폴드

스캐폴딩 도구를 사용하여 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 페이지를 생성합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**솔루션 탐색기** 에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.

![위 단계의 보기](adding-model/_static/add_controller21.png)

**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용** 을 선택합니다.

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

**컨트롤러 추가** 대화 상자를 입력합니다.

* **모델 클래스:** *Movie(MvcMovie.Models)*
* **데이터 컨텍스트 클래스:** *MvcMovieContext(MvcMovie.Data)*

![데이터 컨텍스트 추가](adding-model/_static/dc3.png)

* **보기:** 확인된 각 옵션의 기본값 선택 유지
* **컨트롤러 이름:** 기본값 *MoviesController* 유지
* **추가** 를 선택합니다.

Visual Studio가 다음을 만듭니다

* 영화 컨트롤러( *Controllers/MoviesController.cs* )
* Create, Delete, Details, Edit, 및 Index 페이지에 대한 Razor 뷰 파일( *Views/Movies/\*.cshtml* )

이러한 파일의 자동 생성을 *스캐폴딩* 이라고 합니다.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* 프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.

* Linux에서는 스캐폴드 도구 경로를 내보냅니다.

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* 프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.

* 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

데이터베이스가 없으므로 아직 스캐폴드된 페이지를 사용할 수 없습니다. 앱을 실행하고 **Movie App** 링크를 클릭하면 *Cannot open database* 또는 *no such table: Movie 오류 메시지가 표시됩니다.* Movie’ 오류 메시지가 표시됩니다.

<a name="migration"></a>

## <a name="initial-migration"></a>초기 마이그레이션

EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용하여 데이터베이스를 만듭니다. 마이그레이션은 데이터 모델과 일치하도록 데이터베이스를 만들고 수정할 수 있는 도구 모음입니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.

PMC에서 다음 명령을 입력합니다.

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다. `InitialCreate` 인수는 마이그레이션 이름입니다. 모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다. 이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다. 데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.

* `Update-Database`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다. 이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.

  데이터베이스 수정 명령은 다음 경고를 생성합니다. 

  > No type was specified for the decimal column ‘Price’ on entity type ‘Movie’. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using ‘HasColumnType()’.

  이 경고는 무시할 수 있으며 자습서의 뒷부분에서 수정될 예정입니다.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

다음 .NET Core CLI 명령을 실행합니다.

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다. `InitialCreate` 인수는 마이그레이션 이름입니다. 모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다. 이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다. 데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일의 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.

* `ef database update`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다. 이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.

---

### <a name="the-initialcreate-class"></a>InitialCreate 클래스

*Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 확인합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

`Up` 메서드는 Movie 테이블을 만들고 `Id`를 기본 키로 구성합니다. `Down` 메서드는 `Up` 마이그레이션에 의해 변경된 스키마를 되돌립니다.

<a name="test"></a>

## <a name="test-the-app"></a>앱을 테스트합니다.

* 앱을 실행하고 **Movie App** 링크를 클릭합니다.

  다음 중 하나와 비슷한 예외가 발생할 경우:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  [마이그레이션 단계](#migration)를 누락했을 수 있습니다.

* **Create** 페이지를 테스트합니다. 데이터를 입력하고 제출합니다.

  > [!NOTE]
  > `Price` 필드에 소수점을 입력하지 못할 수도 있습니다. 소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다. 세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.

* **Edit** , **Details** 및 **Delete** 링크를 테스트합니다.

## <a name="dependency-injection-in-the-controller"></a>컨트롤러에서 종속성 주입

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Controllers/MoviesController.cs* 파일을 열고 생성자를 확인합니다.

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다. 컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다. 컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용

SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다. 다음 코드에서는 시작에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여줍니다. `IWebHostEnvironment`는 `ConfigureServices`가 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 주입됩니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>강력한 형식의 모델 및 @model 키워드

이 자습서의 앞부분에서는 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 보기에 전달하는 방법을 살펴봤습니다. `ViewData` 사전은 정보를 보기에 전달하기 위한 편리한 런타임 바인딩 방법을 제공하는 동적 개체입니다.

또한 MVC는 보기에 강력한 형식의 모델 개체를 전달하는 기능도 제공합니다. 강력한 형식의 방법을 사용하면 컴파일 시에 코드 검사를 수행할 수 있습니다. 스캐폴딩 메커니즘은 `MoviesController` 클래스 및 보기에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용합니다.

*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 확인합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

일반적으로 `id` 매개 변수는 경로 데이터 변수로 전달됩니다. 예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.

* 컨트롤러를 `movies` 컨트롤러로 설정(첫 번째 URL 세그먼트)
* 작업을 `details`로 설정(두 번째 URL 세그먼트)
* ID를 1로 설정(마지막 URL 세그먼트)

다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수도 있습니다.

`https://localhost:5001/movies/details?id=1`

ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.

[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

영화를 찾으면 `Movie` 모델의 인스턴스를 `Details` 보기에 전달합니다.

```csharp
return View(movie);
```

*Views/Movies/Details.cshtml* 파일의 내용을 확인합니다.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

보기 파일 맨 위에 있는 `@model` 문은 보기에 필요한 개체 형식을 지정합니다. 영화 컨트롤러가 만들어질 때 다음 `@model` 문이 포함됩니다.

```cshtml
@model MvcMovie.Models.Movie
```

`@model` 지시문을 사용하면 컨트롤러가 보기에 전달한 영화에 액세스할 수 있습니다. `Model` 개체는 강력한 형식입니다. 예를 들어 *Details.cshtml* 보기의 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다. 또한 `Create` 및 `Edit` 메서드와 보기도 `Movie` 모델 개체를 전달합니다.

*Index.cshtml* 보기 및 영화 컨트롤러의 `Index` 메서드를 확인합니다. 코드가 `View` 메서드를 호출할 때 `List` 개체를 생성하는 방법에 유의하세요. 이 코드는 `Index` 작업 메서드에서 보기로 `Movies` 목록을 전달합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

영화 컨트롤러가 만들어질 때 스캐폴딩은 다음 `@model` 문을 *Index.cshtml* 파일의 맨 위에 포함시킵니다.

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 보기에 전달한 영화 목록에 액세스할 수 있습니다. 예를 들어 *Index.cshtml* 보기에서 코드는 강력한 형식의 `Model` 개체에 대해 `foreach` 문을 사용하여 영화를 반복합니다.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie` 형식입니다. 즉, 여러 가지 이점 중에서도 코드의 컴파일 시 검사를 수행할 수 있다는 뜻입니다.

## <a name="additional-resources"></a>추가 자료

* [태그 도우미](xref:mvc/views/tag-helpers/intro)
* [세계화 및 지역화](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [이전 보기 추가](adding-view.md)
> [다음 SQL 사용](working-with-sql.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>데이터 모델 클래스 추가

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Models* 폴더> **추가** > **클래스** 를 마우스 오른쪽 단추로 클릭합니다. 클래스의 이름을 **Movie** 로 지정합니다.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* *Models* 폴더에 *Movie.cs* 클래스를 추가합니다.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>영화 모델 스캐폴드

이 섹션에서는 영화 모델을 스캐폴드 합니다. 즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**솔루션 탐색기** 에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.

![위 단계의 보기](adding-model/_static/add_controller21.png)

**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용** 을 선택합니다.

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

**컨트롤러 추가** 대화 상자를 입력합니다.

* **모델 클래스:** *Movie(MvcMovie.Models)*
* **데이터 컨텍스트 클래스:** **+** 아이콘을 선택하고 기본값 **MvcMovie.Models.MvcMovieContext** 를 추가합니다.

![데이터 컨텍스트 추가](adding-model/_static/dc.png)

* **보기:** 확인된 각 옵션의 기본값 선택 유지
* **컨트롤러 이름:** 기본값 *MoviesController* 유지
* **추가** 를 선택합니다.

![컨트롤러 추가 대화 상자](adding-model/_static/add_controller2.png)

Visual Studio가 다음을 만듭니다

* Entity Framework Core [데이터베이스 컨텍스트 클래스](xref:data/ef-mvc/intro#create-the-database-context)( *Data/MvcMovieContext.cs* )
* 영화 컨트롤러( *Controllers/MoviesController.cs* )
* Create, Delete, Details, Edit, 및 Index 페이지에 대한 Razor 뷰 파일( *Views/Movies/\*.cshtml* )

[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)(생성, 읽기, 수정 및 삭제) 작업 메서드와 보기 및 데이터베이스 컨텍스트의 자동 생성을 *스캐폴딩* 이라고 합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.
* 스캐폴딩 도구를 설치합니다.

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Linux에서는 스캐폴드 도구 경로를 내보냅니다.

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* 다음 명령을 실행합니다.

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* 프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.
* 스캐폴딩 도구를 설치합니다.

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* 다음 명령을 실행합니다.

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

응용 프로그램을 실행하고 **Mvc Movie** 링크를 클릭하면 다음과 유사한 오류가 표시됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

데이터베이스를 만들어야 하는데 이를 수행하기 위해 EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용합니다. 마이그레이션을 통해 데이터 모델과 일치하는 데이터베이스를 만들고 데이터 모델 변경 시 데이터베이스 스키마를 수정할 수 있습니다.

<a name="pmc"></a>

## <a name="initial-migration"></a>초기 마이그레이션

이 섹션에서는 다음 작업을 완료합니다.

* 초기 마이그레이션을 추가합니다.
* 초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.

   ![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. PMC에서 다음 명령을 입력합니다.

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   `Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.

   데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다. `Initial` 인수는 마이그레이션 이름입니다. 모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 사용합니다. 자세한 내용은 <xref:data/ef-mvc/migrations>를 참조하세요.

   `Update-Database` 명령은 데이터베이스를 만드는 *Migrations/{time-stamp}_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

`ef migrations add InitialCreate` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.

데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일의 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다. `InitialCreate` 인수는 마이그레이션 이름입니다. 모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>종속성 주입을 사용하여 등록된 컨텍스트 확인

ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 만들어집니다. 서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록됩니다. 이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다. DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

스캐폴딩 도구는 자동으로 DB 컨텍스트를 만들고 DI 컨테이너에 등록합니다.

다음 `Startup.ConfigureServices` 메서드를 확인합니다. 강조 표시된 줄은 스캐폴더에서 추가된 것입니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

`MvcMovieContext`는 `Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제 등)을 조정합니다. 데이터 컨텍스트(`MvcMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다. 데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다. Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다. 엔터티는 테이블의 행에 해당합니다.

연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다. 로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

DB 컨텍스트를 만들고 DI 컨테이너에 등록했습니다.

---

<a name="test"></a>

### <a name="test-the-app"></a>앱을 테스트합니다.

* 앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).

다음과 비슷한 데이터베이스 예외가 발생할 경우:

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.

* **Create** 링크를 테스트합니다. 데이터를 입력하고 제출합니다.

  > [!NOTE]
  > `Price` 필드에 소수점을 입력하지 못할 수도 있습니다. 소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다. 세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.

* **Edit** , **Details** 및 **Delete** 링크를 테스트합니다.

`Startup` 클래스를 확인합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

앞에서 강조 표시된 코드는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가된 영화 데이터베이스 컨텍스트를 표시합니다.

* `services.AddDbContext<MvcMovieContext>(options =>`는 사용할 데이터베이스 및 연결 문자열을 지정합니다.
* `=>`는 [람다 연산자](/dotnet/articles/csharp/language-reference/operators/lambda-operator)입니다.

*Controllers/MoviesController.cs* 파일을 열고 생성자를 확인합니다.

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다. 컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>강력한 형식의 모델 및 @model 키워드

이 자습서의 앞부분에서는 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 보기에 전달하는 방법을 살펴봤습니다. `ViewData` 사전은 정보를 보기에 전달하기 위한 편리한 런타임 바인딩 방법을 제공하는 동적 개체입니다.

또한 MVC는 보기에 강력한 형식의 모델 개체를 전달하는 기능도 제공합니다. 이렇게 강력하게 형식화된 방법을 사용하면 코드를 검사하는 컴파일 시간을 개선할 수 있습니다. 스캐폴딩 메커니즘은 메서드 및 뷰를 만든 경우 `MoviesController` 클래스 및 뷰에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용했습니다.

*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 확인합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

일반적으로 `id` 매개 변수는 경로 데이터 변수로 전달됩니다. 예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.

* 컨트롤러를 `movies` 컨트롤러로 설정(첫 번째 URL 세그먼트)
* 작업을 `details`로 설정(두 번째 URL 세그먼트)
* ID를 1로 설정(마지막 URL 세그먼트)

다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수도 있습니다.

`https://localhost:5001/movies/details?id=1`

ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.

[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

영화를 찾으면 `Movie` 모델의 인스턴스를 `Details` 보기에 전달합니다.

```csharp
return View(movie);
   ```

*Views/Movies/Details.cshtml* 파일의 내용을 확인합니다.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

보기 파일 맨 위에 있는 `@model` 문을 포함하여 보기에서 필요로 하는 개체 형식을 지정할 수 있습니다. 영화 컨트롤러를 만들 때 *Details.cshtml* 파일의 맨 위에 다음 `@model` 문이 자동으로 포함됩니다.

```cshtml
@model MvcMovie.Models.Movie
```

이 `@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 뷰에 전달된 영화에 액세스할 수 있습니다. 예를 들어 *Details.cshtml* 뷰에서 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다. 또한 `Create` 및 `Edit` 메서드와 보기도 `Movie` 모델 개체를 전달합니다.

*Index.cshtml* 보기 및 영화 컨트롤러의 `Index` 메서드를 확인합니다. 코드가 `View` 메서드를 호출할 때 `List` 개체를 생성하는 방법에 유의하세요. 이 코드는 `Index` 작업 메서드의 `Movies` 목록을 뷰에 전달합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

영화 컨트롤러를 만들 때 스캐폴딩에서는 *Index.cshtml* 파일의 맨 위에 다음 `@model` 문을 자동으로 포함시킵니다.

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 보기에 전달한 영화 목록에 액세스할 수 있습니다. 예를 들어 *Index.cshtml* 보기에서 코드는 강력한 형식의 `Model` 개체에 대해 `foreach` 문을 사용하여 영화를 반복합니다.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie` 형식입니다. 즉, 여러 가지 이점 중에서 코드를 검사하는 컴파일 시간을 가져올 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* [태그 도우미](xref:mvc/views/tag-helpers/intro)
* [세계화 및 지역화](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [이전 보기 추가](adding-view.md)
> [다음 데이터베이스 사용](working-with-sql.md)

::: moniker-end
