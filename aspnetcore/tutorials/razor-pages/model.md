---
title: 2부. ASP.NET Core에서 Razor Pages 앱에 모델 추가
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 2부입니다.
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 84198760cf8302d379c7630b65641e65b66d72a2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050929"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>2부. ASP.NET Core에서 Razor Pages 앱에 모델 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

이 섹션에서는 동영상을 관리하기 위한 클래스를 추가합니다. 이 앱의 모델 클래스는 [EF Core(Entity Framework Core)](/ef/core)를 사용하여 데이터베이스 작업을 수행합니다. EF Core는 데이터 액세스를 간소화하는 O/RM(개체-관계형 매퍼)입니다.

모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다. 이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>데이터 모델 추가

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.

*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다. **추가** > **클래스** 를 선택합니다. 클래스의 이름을 **동영상** 으로 지정합니다.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Models* 폴더를 추가합니다.
* *Models* 폴더에 *Movie.cs* 클래스를 추가합니다.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* Solution Pad에서 **RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더...** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.
* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일...** 을 선택합니다.
* **새 파일** 대화 상자에서:

  * 왼쪽 창에서 **일반** 을 선택합니다.
  * 가운데 창에서 **빈 클래스** 를 선택합니다.
  * 클래스 이름을 **Movie** 로 지정하고 **새로 만들기** 를 선택합니다.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.

## <a name="scaffold-the-movie-model"></a>영화 모델 스캐폴드

이 섹션에서는 영화 모델을 스캐폴드 합니다. 즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Pages/Movies* 폴더를 만듭니다.

* *Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.
* 폴더 이름을 *Movies* 로 지정합니다.

*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목** 을 선택합니다.

![이전 지침의 이미지입니다.](model/_static/sca.png)

**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가** 를 선택합니다.

![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.

* **모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택합니다.
* **데이터 컨텍스트 클래스** 행에서 **+** 기호를 선택하고 생성된 이름을 RazorPagesMovie. **Models**.RazorPagesMovieContext에서 RazorPagesMovie. **Data**.RazorPagesMovieContext로 변경합니다. [이 변경은](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) 필수는 아닙니다. 올바른 네임스페이스를 사용하여 데이터베이스 컨텍스트 클래스를 만듭니다.
* **추가** 를 선택합니다.

![이전 지침의 이미지입니다.](model/_static/3/arp.png)

*appsettings.json* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.
* 스캐폴딩 도구를 설치합니다.

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Windows의 경우** : 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **macOS 및 Linux의 경우** : 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

*Pages/Movies* 폴더를 만듭니다.

* *Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.
* 폴더 이름을 *Movies* 로 지정합니다.

*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 스캐폴딩...** 을 선택합니다.

![이전 지침의 이미지입니다.](model/_static/scaMac.png)

**새 스캐폴딩** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **다음** 을 선택합니다.

![이전 지침의 이미지입니다.](model/_static/add_scaffoldMac.png)

**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.

* **모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택하거나 입력합니다.
* **데이터 컨텍스트 클래스** 행에 새 클래스의 이름 RazorPagesMovie. **Data**.RazorPagesMovieContext를 입력합니다. [이 변경은](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) 필수는 아닙니다. 올바른 네임스페이스를 사용하여 데이터베이스 컨텍스트 클래스를 만듭니다.
* **추가** 를 선택합니다.

![이전 지침의 이미지입니다.](model/_static/arpMac.png)

*appsettings.json* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.

### <a name="add-ef-tools"></a>EF 도구 추가

다음 .NET Core CLI 명령을 실행합니다.

```dotnetcli
dotnet tool install --global dotnet-ef
```

위의 명령은 .NET Core CLI에 대한 Entity Framework Core 도구를 추가합니다.

---

### <a name="files-created"></a>생성된 파일

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.

* *Pages/Movies* : 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>업데이트됨

* *Startup.cs*

생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.

* *Pages/Movies* : 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>업데이트됨

* *Startup.cs*

생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

스캐폴드 프로세스는 다음 파일을 만듭니다.

* *Pages/Movies* : 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.

생성된 파일은 다음 섹션에서 설명합니다.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>초기 마이그레이션

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.

* 초기 마이그레이션을 추가합니다.
* 초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.

**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다.

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

PMC에서 다음 명령을 입력합니다.

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

이전 명령은 다음 경고를 생성합니다. "엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다. This will cause values to be silently truncated if they do not fit in the default precision and scale. 'HasColumnType()'를 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다."

이 경고는 무시할 수 있으며 자습서의 뒷부분에서 수정될 예정입니다.

migrations 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다. 이 스키마는 `DbContext`에 지정된 모델을 기반으로 합니다. `InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다. 모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.

`update` 명령은 적용되지 않은 마이그레이션에서 `Up` 메서드를 실행합니다. 이 경우 `update`는 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행하여 데이터베이스를 만듭니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>종속성 주입을 사용하여 등록된 컨텍스트 확인

ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다. 서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다. 이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다. DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.

스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.

`Startup.ConfigureServices` 메서드를 검사합니다. 강조 표시된 줄은 스캐폴더에서 추가된 것입니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제 등)을 조정합니다. 데이터 컨텍스트(`RazorPagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다. 데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다. Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다. 엔터티는 테이블의 행에 해당합니다.

연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다. 로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`Up` 메서드를 검사합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

`Up` 메서드를 검사합니다.

---

<a name="test"></a>

### <a name="test-the-app"></a>앱을 테스트합니다.

* 앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).

오류가 표시될 경우:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.

* **Create** 링크를 테스트합니다.

  ![페이지 만들기](model/_static/conan.png)

  > [!NOTE]
  > `Price` 필드에 소수점을 입력하지 못할 수도 있습니다. 소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다. 세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.

* **Edit** , **Details** 및 **Delete** 링크를 테스트합니다.

다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.

## <a name="additional-resources"></a>추가 자료

> [!div class="step-by-step"]
> [이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

이 섹션에서는 플랫폼 간 [SQLite 데이터베이스](https://www.sqlite.org/index.html)에서 동영상을 관리하기 위한 클래스를 추가합니다. ASP.NET Core 템플릿으로 만든 앱이 SQLite 데이터베이스를 사용합니다. 앱의 모델 클래스를 [EF Core(Entity Framework Core)](/ef/core)([SQLite EF Core 데이터베이스 공급자](/ef/core/providers/sqlite))와 함께 사용하여 데이터베이스를 작업합니다. EF Core는 데이터 액세스를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.

모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다. 이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>데이터 모델 추가

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.

*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다. **추가** > **클래스** 를 선택합니다. 클래스의 이름을 **동영상** 으로 지정합니다.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Models* 폴더를 추가합니다.
* *Models* 폴더에 *Movie.cs* 클래스를 추가합니다.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* 솔루션 탐색기에서 **RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.
* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** 을 선택합니다.
* **새 파일** 대화 상자에서:

  * 왼쪽 창에서 **일반** 을 선택합니다.
  * 가운데 창에서 **빈 클래스** 를 선택합니다.
  * 클래스 이름을 **Movie** 로 지정하고 **새로 만들기** 를 선택합니다.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.

## <a name="scaffold-the-movie-model"></a>영화 모델 스캐폴드

이 섹션에서는 영화 모델을 스캐폴드 합니다. 즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Pages/Movies* 폴더를 만듭니다.

* *Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.
* 폴더 이름을 *Movies* 로 지정합니다.

*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목** 을 선택합니다.

![이전 지침의 이미지입니다.](model/_static/sca.png)

**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가** 를 선택합니다.

![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* **모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택합니다.
* **데이터 컨텍스트 클래스** 행에서 **+** 기호를 선택하고 생성된 이름인 **RazorPagesMovie.Models.RazorPagesMovieContext** 를 수용합니다.
* **추가** 를 선택합니다.

![이전 지침의 이미지입니다.](model/_static/arp.png)

*appsettings.json* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.

* **Windows의 경우** : 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **macOS 및 Linux의 경우** : 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

*Pages/Movies* 폴더를 만듭니다.

* *Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.
* 폴더 이름을 *Movies* 로 지정합니다.

*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목** 을 선택합니다.

![이전 지침의 이미지입니다.](model/_static/scaMac.png)

**새 스캐폴딩 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가** 를 선택합니다.

![이전 지침의 이미지입니다.](model/_static/add_scaffoldMac.png)

**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.

* **모델 클래스** 드롭다운에서 **동영상** 을 선택하거나 입력합니다.
* **데이터 컨텍스트 클래스** 행에 **RazorPagesMovieContext** 를 입력하거나 선택합니다. 그러면 올바른 네임스페이스의 새 db 컨텍스트 클래스가 만들어집니다. 이 경우 클래스는 **RazorPagesMovie.Models.RazorPagesMovieContext** 입니다.
* **추가** 를 선택합니다.

![이전 지침의 이미지입니다.](model/_static/arpMac.png)

*appsettings.json* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.

---

스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.

### <a name="files-created"></a>생성된 파일

* *Pages/Movies* : 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.
* *Data/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>파일 업데이트됨

* *Startup.cs*

생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.

<a name="pmc"></a>

## <a name="initial-migration"></a>초기 마이그레이션

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.

* 초기 마이그레이션을 추가합니다.
* 초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.

**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다.

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

PMC에서 다음 명령을 입력합니다.

```powershell
Add-Migration Initial
Update-Database
```

`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다. 스키마는 `DbContext`에 지정된 모델을 기반으로 합니다( *RazorPagesMovieContext.cs* 파일). `InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다. 모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 사용합니다. 자세한 내용은 <xref:data/ef-mvc/migrations>를 참조하세요.

`Update-Database` 명령은 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다. `Up` 메서드는 데이터베이스를 만듭니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> 이전 명령은 다음 경고를 생성합니다. " *엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다. This will cause values to be silently truncated if they do not fit in the default precision and scale. 'HasColumnType()'을 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다.* " 이 경고는 무시할 수 있으며 이후 자습서에서 수정될 예정입니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>종속성 주입을 사용하여 등록된 컨텍스트 확인

ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다. 서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다. 이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다. DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.

스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.

`Startup.ConfigureServices` 메서드를 검사합니다. 강조 표시된 줄은 스캐폴더에서 추가된 것입니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제 등)을 조정합니다. 데이터 컨텍스트(`RazorPagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다. 데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다. Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다. 엔터티는 테이블의 행에 해당합니다.

연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다. 로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`Up` 메서드를 검사합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

`Up` 메서드를 검사합니다.

---

<a name="test"></a>

### <a name="test-the-app"></a>앱을 테스트합니다.

* 앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).

오류가 표시될 경우:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.

* **Create** 링크를 테스트합니다.

  ![페이지 만들기](model/_static/conan.png)

  > [!NOTE]
  > `Price` 필드에 소수점을 입력하지 못할 수도 있습니다. 소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다. 세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.

* **Edit** , **Details** 및 **Delete** 링크를 테스트합니다.

다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.

## <a name="additional-resources"></a>추가 자료

> [!div class="step-by-step"]
> [이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end
