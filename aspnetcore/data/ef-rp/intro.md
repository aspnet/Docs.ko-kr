---
title: ASP.NET Core에서 Entity Framework Core를 사용한 Razor Pages - 자습서 1/8
author: rick-anderson
description: Entity Framework Core를 사용하여 Razor Pages 앱을 만드는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 86b57a9cad27673b72ad174a18741f5528f9f78a
ms.sourcegitcommit: c321518bfe367280ef262aecaada287f17fe1bc5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97011860"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>ASP.NET Core에서 Entity Framework Core를 사용한 Razor Pages - 자습서 1/8

작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[ASP.NET Core Razor Pages](xref:razor-pages/index) 앱에서 EF(Entity Framework) Core를 사용하는 방법을 보여 주는 자습서 시리즈 중 첫 번째 자습서입니다. 이 자습서에서는 가상 Contoso 대학의 웹 사이트를 빌드합니다. 이 사이트에는 학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다. 이 자습서에서는 Code First 방법을 사용합니다. Database First 방법을 사용하여 이 자습서를 수행하는 데 대한 정보는 [이 Github 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16897)를 참조하세요.

[완성된 앱을 다운로드하거나 확인하세요.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [지침을 다운로드하세요](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>사전 요구 사항

* Razor Pages를 처음 사용한다면 이 자습서를 시작하기 전에 Razor [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start) 자습서 시리즈를 진행하세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a>데이터베이스 엔진

Visual Studio 지침에서는 Windows에서만 실행되는 SQL Server Express 버전인 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 사용합니다.

Visual Studio Code 지침에서는 플랫폼 간 데이터베이스 엔진인 [SQLite](https://www.sqlite.org/)를 사용합니다.

SQLite를 사용하도록 선택하는 경우 [SQLite용 DB 브라우저](https://sqlitebrowser.org/)와 같이 SQLite 데이터베이스를 관리하고 볼 수 있는 타사 도구를 다운로드하여 설치합니다.

## <a name="troubleshooting"></a>문제 해결

해결할 수 없는 문제가 발생한 경우 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)와 코드를 비교합니다. 도움을 얻으려면 [ASP.NET Core 태그](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core 태그](https://stackoverflow.com/questions/tagged/entity-framework-core)를 사용하여 StackOverflow.com에 질문을 게시하는 것이 좋습니다.

## <a name="the-sample-app"></a>샘플 앱

이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다. 사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다. 자습서에서 만든 화면 중 몇 가지 예가 나와 있습니다.

![학생 인덱스 페이지](intro/_static/students-index30.png)

![학생 편집 페이지](intro/_static/student-edit30.png)

이 사이트의 UI 스타일은 기본 제공 프로젝트 템플릿을 기반으로 합니다. 이 자습서에서는 UI를 사용자 지정하는 방법이 아닌 EF Core를 ASP.NET Core와 함께 사용하는 방법을 중점적으로 설명합니다.

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a>웹앱 프로젝트 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Visual Studio를 시작하고 **새 프로젝트 만들기** 를 선택합니다.
1. **새 프로젝트 만들기** 대화 상자에서 **ASP.NET Core 웹 애플리케이션** > **다음** 을 선택합니다.
1. **새 프로젝트 구성** 대화 상자에서 **프로젝트 이름** 으로 `ContosoUniversity`를 입력합니다. 코드를 복사할 때 `namespace`가 일치하도록 대문자 표시를 포함하여 이 이름을 정확히 사용해야 합니다.
1. **만들기** 를 선택합니다.
1. **새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 다음을 선택합니다.
    1. 드롭다운에서 **.NET Core** 와 **ASP.NET Core 5.0** 을 선택합니다.
    1. **ASP.NET Core 웹앱**
    1. 
      ![새 ASP.NET Core 프로젝트 **만들기** 대화 상자](~/data/ef-mvc/intro/_static/new-aspnet5.png)
    
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 터미널에서 프로젝트 폴더를 만들어야 하는 폴더로 이동합니다.
* 다음 명령을 실행하여 Razor Pages 프로젝트를 만들고 `cd`를 사용하여 새 프로젝트 폴더로 이동합니다.

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a>사이트 스타일 설정

다음 코드를 복사하여 *Pages/Shared/_Layout.cshtml* 파일에 붙여넣습니다. 

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

레이아웃 파일은 사이트 머리글, 바닥글 및 메뉴를 설정합니다. 위의 코드로 다음이 변경됩니다.

* 모든 “ContosoUniversity”가 “Contoso University”로 변경됩니다. 세 번 나옵니다.
* **홈** 및 **프라이버시** 메뉴 항목이 삭제됩니다.
* **정보**,**학생**, **과정**, **강사** 및 **부서** 에 대해 항목이 추가됩니다.

*Pages/Index.cshtml* 에서 파일의 내용을 다음 코드로 바꿉니다.

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

위의 코드는 ASP.NET Core에 대한 텍스트를 이 앱에 대한 텍스트로 바꿉니다.

앱을 실행하여 홈페이지가 표시되는지 확인합니다.

## <a name="the-data-model"></a>데이터 모델

다음 섹션에서는 데이터 모델을 만듭니다.

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

학생은 개수와 관계없이 원하는 과정에 등록할 수 있으며 한 과정에는 여러 명이 등록될 수 있습니다.

## <a name="the-student-entity"></a>학생 엔터티

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

* 프로젝트 폴더에 *Models* 폴더를 만듭니다. 

* 다음 코드로 *Models/Student.cs* 를 만듭니다.

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

`ID` 속성은 이 클래스에 해당하는 데이터베이스 테이블의 기본 키 열이 됩니다. 기본적으로 EF 코어는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다. 따라서 `Student` 클래스 기본 키의 자동으로 인식되는 대체 이름은 `StudentID`입니다. 자세한 내용은 [EF Core - 키](/ef/core/modeling/keys?tabs=data-annotations)를 참조하세요.

`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다. 탐색 속성은 이 엔터티와 관련된 다른 엔터티를 포함합니다. 이 경우 `Student` 엔터티의 `Enrollments` 속성은 해당 Student에 관련된 모든 `Enrollment` 엔터티를 포함합니다. 예를 데이터베이스의 Student 행에 두 개의 관련 Enrollment 행이 있는 경우 `Enrollments` 탐색 속성은 그 두 Enrollment 엔터티를 포함합니다. 

데이터베이스에서 StudentID 열에 학생 ID 값이 포함된 경우 Enrollment 행은 Student 행과 관련됩니다. 예를 들어 Student 행에 ID=1이 있다고 가정합니다. 관련 Enrollment 행에는 StudentID=1이 포함됩니다. StudentID는 Enrollment 테이블의 ‘외래 키’입니다. 

관련 Enrollment 엔터티가 여러 개 있을 수 있으므로 `Enrollments` 속성은 `ICollection<Enrollment>`로 정의됩니다. `List<Enrollment>` 또는`HashSet<Enrollment>`와 같은 다른 컬렉션 형식을 사용할 수 있습니다. `ICollection<Enrollment>`가 사용되는 경우 EF Core는 기본적으로 `HashSet<Enrollment>` 컬렉션을 만듭니다.

## <a name="the-enrollment-entity"></a>등록 엔터티

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

다음 코드로 *Models/Enrollment.cs* 를 만듭니다.

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

`EnrollmentID` 속성은 기본 키입니다. 이 엔터티는 자체적으로 `ID` 대신 `classnameID` 패턴을 사용합니다. 프로덕션 데이터 모델의 경우 하나의 패턴을 선택하고 일관되게 사용합니다. 이 자습서에서는 두 항목이 작동하는 것을 보여 주기 위해 둘 다 사용합니다. `classname` 없이 `ID`를 사용하면 특정 종류의 데이터 모델 변경 내용을 더 쉽게 구현할 수 있습니다.

`Grade` 속성은 `enum`입니다. `Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 [nullable](/dotnet/csharp/programming-guide/nullable-types/)이라는 것을 나타냅니다. Null인 등급은 0등급과는 다릅니다. Null은 알 수 없거나 아직 할당되지 않은 등급을 의미합니다.

`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다. `Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티를 포함합니다.

`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다. `Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.

EF Core는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키로 해석합니다. 예를 들어 `Student` 엔터티의 기본 키는 `ID`이므로 `StudentID`는 `Student` 탐색 속성의 외래 키입니다. 외래 키 속성의 이름은 `<primary key property name>`으로 지정할 수 있습니다. 예를 들어 `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`라고 할 수 있습니다.

## <a name="the-course-entity"></a>강좌 엔터티

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

다음 코드로 *Models/Course.cs* 를 만듭니다.

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

`Enrollments` 속성은 탐색 속성입니다. `Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.

`DatabaseGenerated` 특성을 사용하면 데이터베이스에서 기본 키를 생성하도록 하지 않고 앱에서 기본 키를 지정할 수 있습니다.

프로젝트를 빌드하여 컴파일러 오류가 없는지 유효성을 검사합니다.

## <a name="scaffold-student-pages"></a>학생 페이지 스캐폴드

이 섹션에서는 ASP.NET Core 스캐폴딩 도구를 사용하여 다음을 생성합니다.

* EF Core `DbContext` 클래스. 컨텍스트는 특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다. 이 클래스는 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 클래스에서 파생됩니다.
* `Student` 엔터티에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 처리하는 Razor 페이지.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Pages/Students* 폴더를 만듭니다.
* **솔루션 탐색기** 에서 *Pages/Students* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드 항목 새로 만들기** 를 선택합니다.
* **새 스캐폴드 항목 추가** 대화 상자에서 다음을 수행합니다.
  * 왼쪽 탭에서 **설치 > 공통 > Razor 페이지** 를 선택합니다.
  * **RazorEntity Framework를 사용한 Razor 페이지(CRUD)** > **추가** 를 선택합니다.
* **Entity Framework(CRUD)를 사용한 Razor Pages 추가** 대화 상자에서 다음 작업을 수행합니다.
  * **모델 클래스** 드롭다운에서 **학생(ContosoUniversity.Models)** 을 선택합니다.
  * **데이터 컨텍스트 클래스** 행에서 **+** (더하기) 기호를 선택합니다.
    * 데이터 컨텍스트 이름을 `ContosoUniversityContext`가 아닌 `SchoolContext`로 끝나게 변경합니다. 업데이트된 컨텍스트 이름: `ContosoUniversity.Data.SchoolContext`
    * **추가** 를 선택하여 데이터 컨텍스트 클래스 추가를 마칩니다.
   * **추가** 를 선택하여 **Razor 페이지 추가** 대화 상자를 마칩니다.

다음 패키지가 자동으로 설치됩니다.

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 다음 .NET Core CLI 명령을 실행하여 필요한 NuGet 패키지를 설치합니다.

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   스캐폴딩에는 Microsoft.VisualStudio.Web.CodeGeneration.Design 패키지가 필요합니다. 앱은 SQL Server를 사용하지 않지만 스캐폴딩 도구에는 SQL Server 패키지가 필요합니다.

* *Pages/Students* 폴더를 만듭니다.

* 다음 명령을 실행하여 [aspnet-codegenerator 스캐폴딩 도구](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 설치합니다.

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* 다음 명령을 실행하여 학생 페이지를 스캐폴드합니다.

  **Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  **macOS 또는 Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

앞의 단계가 실패할 경우 프로젝트를 빌드하고 스캐폴드 단계를 다시 시도합니다.

스캐폴딩 프로세스:

* *Pages/Students* 폴더에서 다음 Razor 페이지를 만듭니다.
  * *Create.cshtml* 및 *Create.cshtml.cs*
  * *Delete.cshtml* 및 *Delete.cshtml.cs*
  * *Details.cshtml* 및 *Details.cshtml.cs*
  * *Edit.cshtml* 및 *Edit.cshtml.cs*
  * *Index.cshtml* 및 *Index.cshtml.cs*
* *Data/SchoolContext.cs* 를 만듭니다.
* *Startup.cs* 의 종속성 주입에 컨텍스트를 추가합니다.
* *appsettings.json* 에 데이터베이스 연결 문자열을 추가합니다.

## <a name="database-connection-string"></a>데이터베이스 연결 문자열

스캐폴딩 도구가 *appsettings.json* 파일에 연결 문자열을 생성합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

연결 문자열은 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다.

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다. 기본적으로 LocalDB는 `C:/Users/<user>` 디렉터리에 *.mdf* 파일을 만듭니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

SQLite 연결 문자열을 *CU.db* 로 줄입니다.

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>데이터베이스 컨텍스트 클래스 업데이트

특정 데이터 모델에 맞게 EF Core 기능을 조정하는 주 클래스는 데이터베이스 컨텍스트 클래스입니다. 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다. 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다. 이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.

다음 코드로 *Data/SchoolContext.cs* 를 업데이트합니다.

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

앞의 코드가 단수 `DbSet<Student> Student`에서 복수 `DbSet<Student> Students`로 변경됩니다. Razor 페이지 코드가 새 `DBSet` 이름과 일치하도록 `_context.Student.`에서
`_context.Students.`로 전역 변경을 수행합니다.

8개 발생 항목이 있습니다.

엔터티 집합은 여러 엔터티를 포함하기 때문에 다수의 개발자들이 복수인 `DBSet` 속성 이름을 선호합니다.

강조 표시된 코드:

* 각 엔터티 집합에 대한 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다. EF Core 용어에서:
  * 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.
  * 엔터티는 테이블의 행에 해당합니다.
* <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>. `OnModelCreating`:
  * `SchoolContext`가 초기화되었을 때 모델이 잠기고 컨텍스트를 초기화하는 데 사용되기 전에 호출됩니다.
  * 자습서의 뒷부분에서 `Student` 엔터티가 다른 엔터티에 대한 참조를 포함하기 때문에 필요합니다.
  <!-- Review, OnModelCreating needs review -->

프로젝트를 빌드하여 컴파일러 오류가 없는지 확인합니다.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다. 서비스(예: `SchoolContext`)는 앱 시작 중에 종속성 주입에 등록됩니다. 이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다. 데이터베이스 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.

스캐폴딩 도구는 종속성 주입 컨테이너에 컨텍스트 클래스를 자동으로 등록합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

다음 강조 표시된 줄을 스캐폴더가 추가했습니다.

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

스캐폴드가 추가한 코드가 `UseSqlite`를 호출하는지 확인합니다.

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

프로덕션 데이터베이스 사용에 대한 자세한 내용은 [개발에 SQLite, 프로덕션에 SQL Server 사용](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production)을 참조하세요.

---

[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다. 로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.

### <a name="add-the-database-exception-filter"></a>데이터베이스 예외 필터 추가

다음 코드와 같이 <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A>를 `ConfigureServices`에 추가합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

[Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 패키지를 추가합니다.

PMC에서 다음을 입력하여 NuGet 패키지를 추가합니다.

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet 패키지는 Entity Framework Core 오류 페이지에 대한 ASP.NET Core 미들웨어를 제공합니다. 이 미들웨어는 Entity Framework Core 마이그레이션의 오류를 검색 및 진단하는 데 도움이 됩니다.

`AddDatabaseDeveloperPageExceptionFilter`는 [개발 환경](xref:fundamentals/environments)에서 유용한 오류 정보를 제공합니다.

## <a name="create-the-database"></a>데이터베이스 만들기

*Program.cs* 를 업데이트하여 데이터베이스가 없는 경우 데이터베이스를 만듭니다.

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 메서드는 컨텍스트의 데이터베이스가 있는 경우 아무 작업도 수행하지 않습니다. 데이터베이스가 없는 경우 데이터베이스 및 스키마를 만듭니다. `EnsureCreated`를 사용하면 다음 워크플로가 데이터 모델 변경 내용을 처리할 수 있습니다.

* 데이터베이스를 삭제합니다. 모든 기존 데이터가 손실됩니다.
* 데이터 모델을 변경합니다. 예를 들어 `EmailAddress` 필드를 추가합니다.
* 앱을 실행합니다.
* `EnsureCreated`는 새 스키마를 사용하여 데이터베이스를 만듭니다.

이 워크플로는 데이터를 보존할 필요가 없는 경우 스키마가 빠르게 업데이트되는 개발 초기에 적합합니다. 데이터베이스에 입력된 데이터를 보존해야 하는 경우는 상황이 다릅니다. 이 경우에는 마이그레이션을 사용합니다.

자습서 시리즈의 뒷부분에서는 `EnsureCreated`에서 만든 데이터베이스를 삭제하고 마이그레이션을 대신 사용합니다. `EnsureCreated`에서 만든 데이터베이스는 마이그레이션을 사용하여 업데이트할 수 없습니다.

### <a name="test-the-app"></a>앱을 테스트합니다.

* 앱을 실행합니다.
* **학생** 링크 및 **새로 만들기** 를 차례로 선택합니다.
* 편집, 세부 정보 및 삭제 링크를 테스트합니다.

## <a name="seed-the-database"></a>데이터베이스 시드

`EnsureCreated` 메서드는 빈 데이터베이스를 만듭니다. 이 섹션에서는 테스트 데이터로 데이터베이스를 채우는 코드를 추가합니다.

다음 코드로 *Data/DbInitializer.cs* 를 만듭니다.
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  이 코드는 데이터베이스에 학생이 있는지 확인합니다. 학생이 없는 경우 테스트 데이터를 데이터베이스에 추가합니다. `List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 만들어 성능을 최적화합니다.

*Program.cs* 에서 `EnsureCreated` 호출을 `DbInitializer.Initialize` 호출로 바꿉니다.

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

앱이 실행 중이라면 중지하고 **패키지 관리자 콘솔**(PMC)에서 다음 명령을 실행합니다.

```powershell
Drop-Database -Confirm
```

데이터베이스를 삭제하려면 `Y`로 답합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 실행 중인 경우 앱을 중지하고 *CU.db* 파일을 삭제합니다.

---

* 앱을 다시 시작합니다.
* 학생 페이지를 선택하여 시드된 데이터를 확인합니다.

## <a name="view-the-database"></a>데이터베이스 보기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio의 **보기** 메뉴에서 **SSOX(SQL Server 개체 탐색기)** 를 엽니다.
* SSOX에서 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** 를 선택합니다. 데이터베이스 이름은 이전에 제공한 컨텍스트 이름과 대시 및 GUID를 사용하여 생성됩니다.
* **테이블** 노드를 확장합니다.
* **학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기** 를 클릭하여 만든 열 및 테이블에 삽입된 행을 볼 수 있습니다.
* **Student** 테이블을 마우스 오른쪽 단추로 클릭하고 **코드 보기** 를 클릭하여 `Student` 모델이 `Student` 테이블 스키마에 어떻게 매핑되는지 확인합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

SQLite 도구를 사용하여 데이터베이스 스키마 및 시드된 데이터를 표시합니다. 데이터베이스 파일은 *CU.db* 로 이름이 지정되고 프로젝트 폴더에 배치됩니다.

---

## <a name="asynchronous-code"></a>비동기 코드

비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.

웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다. 이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다. 동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다. 비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다. 따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.

비동기 코드는 런타임 시 약간의 오버헤드를 도입합니다. 트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.

다음 코드에서 [async](/dotnet/csharp/language-reference/keywords/async) 키워드, `Task` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* `async` 키워드는 컴파일러가 다음을 수행하도록 합니다.
  * 메서드 본문의 부분에 대한 콜백을 생성합니다.
  * 반환되는 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 개체를 만듭니다.
* `Task` 반환 형식은 진행 중인 작업을 나타냅니다.
* `await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다. 첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다. 두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.
* `ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.

EF Core를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.

* 쿼리 또는 명령을 데이터베이스에 보내는 명령문만 비동기적으로 실행됩니다. 여기에는 `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` 및 `SaveChangesAsync`가 포함됩니다. `var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.
* EF Core 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.
* 비동기 코드의 성능 이점을 이용하려면 라이브러리 패키지(예: 페이징)가 쿼리를 데이터베이스에 보내는 EF Core 메서드를 호출하는 경우 비동기를 사용하는지 확인합니다.

.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/standard/async) 및 [Async 및 Await를 사용한 비동기 프로그래밍](/dotnet/csharp/programming-guide/concepts/async/)을 참조하세요.

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a>성능 고려 사항

일반적으로 웹 페이지는 임의의 행 수를 로드하지 않아야 합니다. 쿼리는 페이징이나 제한 방법을 사용해야 합니다. 예를 들어 앞의 쿼리는 `Take`를 사용하여 반환되는 행을 제한할 수 있습니다.

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

부분적으로 열거를 통해 데이터베이스 예외가 발생할 경우, 뷰에서 대규모 테이블을 열거하면 일부분이 생성된 HTTP 200 응답을 반환할 수 있습니다.

<xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize>의 기본값은 1024입니다. 다음 코드는 `MaxModelBindingCollectionSize`를 설정합니다.

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

페이징에 대해서는 자습서 뒷부분에서 다룹니다.

## <a name="next-steps"></a>다음 단계

> [!div class="step-by-step"]
> [다음 자습서](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[ASP.NET Core Razor Pages](xref:razor-pages/index) 앱에서 EF(Entity Framework) Core를 사용하는 방법을 보여 주는 자습서 시리즈 중 첫 번째 자습서입니다. 이 자습서에서는 가상 Contoso 대학의 웹 사이트를 빌드합니다. 이 사이트에는 학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다. 이 자습서에서는 Code First 방법을 사용합니다. Database First 방법을 사용하여 이 자습서를 수행하는 데 대한 정보는 [이 Github 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16897)를 참조하세요.

[완성된 앱을 다운로드하거나 확인하세요.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [지침을 다운로드하세요](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>사전 요구 사항

* Razor Pages를 처음 사용한다면 이 자습서를 시작하기 전에 Razor [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start) 자습서 시리즈를 진행하세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>데이터베이스 엔진

Visual Studio 지침에서는 Windows에서만 실행되는 SQL Server Express 버전인 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 사용합니다.

Visual Studio Code 지침에서는 플랫폼 간 데이터베이스 엔진인 [SQLite](https://www.sqlite.org/)를 사용합니다.

SQLite를 사용하도록 선택하는 경우 [SQLite용 DB 브라우저](https://sqlitebrowser.org/)와 같이 SQLite 데이터베이스를 관리하고 볼 수 있는 타사 도구를 다운로드하여 설치합니다.

## <a name="troubleshooting"></a>문제 해결

해결할 수 없는 문제가 발생한 경우 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)와 코드를 비교합니다. 도움을 얻으려면 [ASP.NET Core 태그](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core 태그](https://stackoverflow.com/questions/tagged/entity-framework-core)를 사용하여 StackOverflow.com에 질문을 게시하는 것이 좋습니다.

## <a name="the-sample-app"></a>샘플 앱

이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다. 사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다. 자습서에서 만든 화면 중 몇 가지 예가 나와 있습니다.

![학생 인덱스 페이지](intro/_static/students-index30.png)

![학생 편집 페이지](intro/_static/student-edit30.png)

이 사이트의 UI 스타일은 기본 제공 프로젝트 템플릿을 기반으로 합니다. 이 자습서에서는 UI를 사용자 지정하는 방법이 아닌 EF Core를 사용하는 방법을 중점적으로 설명합니다.

페이지 맨 위에 있는 링크를 따라 완료된 프로젝트의 소스 코드를 가져옵니다. *cu30* 폴더에는 자습서의 ASP.NET Core 3.0 버전에 대한 코드가 있습니다. 자습서 1~7의 코드 상태를 반영하는 파일은 *cu30snapshots* 폴더에서 찾을 수 있습니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

완료된 프로젝트를 다운로드한 후 앱을 실행하려면 다음을 수행합니다.

* 프로젝트를 빌드합니다.
* PMC(패키지 관리자 콘솔)에서 다음 명령을 실행합니다.

  ```powershell
  Update-Database
  ```

* 프로젝트를 실행하여 데이터베이스를 시드합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

완료된 프로젝트를 다운로드한 후 앱을 실행하려면 다음을 수행합니다.

* *ContosoUniversity.csproj* 를 삭제하고 *ContosoUniversitySQLite.csproj* 의 이름을 *ContosoUniversity.csproj* 로 바꿉니다.
* *Program.cs* 에서 `StartupSQLite`를 사용하도록 `#define Startup`을 주석으로 처리합니다.
* *appSettings.json* 을 삭제하고 *appSettingsSQLite.json* 의 이름을 *appSettings.json* 으로 바꿉니다.
* *Migrations* 폴더를 삭제하고 *MigrationsSQL* 의 이름을 *Migrations* 로 바꿉니다.
* `#if SQLiteVersion`에 대한 전체 검색을 수행하고 `#if SQLiteVersion` 및 관련된 `#endif` 문을 제거합니다.
* 프로젝트를 빌드합니다.
* 프로젝트 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* SQLite 도구에서 다음 SQL 문을 실행합니다.

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* 프로젝트를 실행하여 데이터베이스를 시드합니다.

---

## <a name="create-the-web-app-project"></a>웹앱 프로젝트 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트** 를 선택합니다.
* **새 ASP.NET Core 웹 애플리케이션** 을 선택합니다.
* 프로젝트 이름을 *ContosoUniversity* 로 지정합니다. 코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 대문자 표시를 포함하여 정확한 이름을 사용해야 합니다.
* 드롭다운에서 **.NET Core** 및 **ASP.NET Core 3.0** 을 선택한 후 **웹 애플리케이션** 을 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 터미널에서 프로젝트 폴더를 만들어야 하는 폴더로 이동합니다.

* 다음 명령을 실행하여 Razor Pages 프로젝트를 만들고 `cd`를 사용하여 새 프로젝트 폴더로 이동합니다.

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>사이트 스타일 설정

*Pages/Shared/_Layout.cshtml* 을 업데이트하여 사이트 헤더, 바닥글 및 메뉴를 설정합니다.

* 모든 “ContosoUniversity”를 “Contoso University”로 변경합니다. 세 번 나옵니다.

* **홈** 및 **프라이버시** 메뉴 항목을 삭제하고 **정보**, **학생**, **과정**, **강사** 및 **부서** 항목을 추가합니다.

변경 내용은 강조 표시되어 있습니다.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

*Pages/Index.cshtml* 에서 다음 코드로 파일의 콘텐츠를 텍스트를 대체하여 ASP.NET Core에 대한 텍스트를 이 앱에 대한 텍스트로 바꿉니다.

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

앱을 실행하여 홈페이지가 표시되는지 확인합니다.

## <a name="the-data-model"></a>데이터 모델

다음 섹션에서는 데이터 모델을 만듭니다.

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

학생은 개수와 관계없이 원하는 과정에 등록할 수 있으며 한 과정에는 여러 명이 등록될 수 있습니다.

## <a name="the-student-entity"></a>학생 엔터티

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

* 프로젝트 폴더에 *Models* 폴더를 만듭니다.
* 다음 코드로 *Models/Student.cs* 를 만듭니다.

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

`ID` 속성은 이 클래스에 해당하는 데이터베이스 테이블의 기본 키 열이 됩니다. 기본적으로 EF 코어는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다. 따라서 `Student` 클래스 기본 키의 자동으로 인식되는 대체 이름은 `StudentID`입니다. 자세한 내용은 [EF Core - 키](/ef/core/modeling/keys?tabs=data-annotations)를 참조하세요.

`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다. 탐색 속성은 이 엔터티와 관련된 다른 엔터티를 포함합니다. 이 경우 `Student` 엔터티의 `Enrollments` 속성은 해당 Student에 관련된 모든 `Enrollment` 엔터티를 포함합니다. 예를 데이터베이스의 Student 행에 두 개의 관련 Enrollment 행이 있는 경우 `Enrollments` 탐색 속성은 그 두 Enrollment 엔터티를 포함합니다. 

데이터베이스에서 StudentID 열에 학생 ID 값이 포함된 경우 Enrollment 행은 Student 행과 관련됩니다. 예를 들어 Student 행에 ID=1이 있다고 가정합니다. 관련 Enrollment 행에는 StudentID=1이 포함됩니다. StudentID는 Enrollment 테이블의 ‘외래 키’입니다. 

관련 Enrollment 엔터티가 여러 개 있을 수 있으므로 `Enrollments` 속성은 `ICollection<Enrollment>`로 정의됩니다. `List<Enrollment>` 또는`HashSet<Enrollment>`와 같은 다른 컬렉션 형식을 사용할 수 있습니다. `ICollection<Enrollment>`가 사용되는 경우 EF Core는 기본적으로 `HashSet<Enrollment>` 컬렉션을 만듭니다.

## <a name="the-enrollment-entity"></a>등록 엔터티

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

다음 코드로 *Models/Enrollment.cs* 를 만듭니다.

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

`EnrollmentID` 속성은 기본 키입니다. 이 엔터티는 자체적으로 `ID` 대신 `classnameID` 패턴을 사용합니다. 프로덕션 데이터 모델의 경우 하나의 패턴을 선택하고 일관되게 사용합니다. 이 자습서에서는 두 항목이 작동하는 것을 보여 주기 위해 둘 다 사용합니다. `classname` 없이 `ID`를 사용하면 특정 종류의 데이터 모델 변경 내용을 더 쉽게 구현할 수 있습니다.

`Grade` 속성은 `enum`입니다. `Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 [nullable](/dotnet/csharp/programming-guide/nullable-types/)이라는 것을 나타냅니다. Null인 등급은 0등급과는 다릅니다. Null은 알 수 없거나 아직 할당되지 않은 등급을 의미합니다.

`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다. `Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티를 포함합니다.

`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다. `Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.

EF Core는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키로 해석합니다. 예를 들어 `Student` 엔터티의 기본 키는 `ID`이므로 `StudentID`는 `Student` 탐색 속성의 외래 키입니다. 외래 키 속성의 이름은 `<primary key property name>`으로 지정할 수 있습니다. 예를 들어 `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`라고 할 수 있습니다.

## <a name="the-course-entity"></a>강좌 엔터티

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

다음 코드로 *Models/Course.cs* 를 만듭니다.

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

`Enrollments` 속성은 탐색 속성입니다. `Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.

`DatabaseGenerated` 특성을 사용하면 데이터베이스에서 기본 키를 생성하도록 하지 않고 앱에서 기본 키를 지정할 수 있습니다.

프로젝트를 빌드하여 컴파일러 오류가 없는지 유효성을 검사합니다.

## <a name="scaffold-student-pages"></a>학생 페이지 스캐폴드

이 섹션에서는 ASP.NET Core 스캐폴딩 도구를 사용하여 다음을 생성합니다.

* EF Core ‘컨텍스트’ 클래스. 컨텍스트는 특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다. 이 클래스는 `Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생됩니다.
* `Student` 엔터티에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 처리하는 Razor 페이지.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Pages* 폴더에 *Students* 폴더를 만듭니다.
* **솔루션 탐색기** 에서 *Pages/Students* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드 항목 새로 만들기** 를 선택합니다.
* **스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가** 를 선택합니다.
* **Entity Framework(CRUD)를 사용한 Razor Pages 추가** 대화 상자에서 다음 작업을 수행합니다.
  * **모델 클래스** 드롭다운에서 **학생(ContosoUniversity.Models)** 을 선택합니다.
  * **데이터 컨텍스트 클래스** 행에서 **+** (더하기) 기호를 선택합니다.
  * 데이터 컨텍스트 이름을 *ContosoUniversity.Models.ContosoUniversityContext* 에서 *ContosoUniversity.Data.SchoolContext* 로 변경합니다.
  * **추가** 를 선택합니다.

다음 패키지가 자동으로 설치됩니다.

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 다음 .NET Core CLI 명령을 실행하여 필요한 NuGet 패키지를 설치합니다.
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  스캐폴딩에는 Microsoft.VisualStudio.Web.CodeGeneration.Design 패키지가 필요합니다. 앱은 SQL Server를 사용하지 않지만 스캐폴딩 도구에는 SQL Server 패키지가 필요합니다.

* *Pages/Students* 폴더를 만듭니다.

* 다음 명령을 실행하여 [aspnet-codegenerator 스캐폴딩 도구](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 설치합니다.

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* 다음 명령을 실행하여 학생 페이지를 스캐폴드합니다.

  **Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **macOS 또는 Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

이전 단계에서 문제가 발생하면 프로젝트를 빌드하고 스캐폴드 단계를 다시 시도합니다.

스캐폴딩 프로세스:

* *Pages/Students* 폴더에서 다음 Razor 페이지를 만듭니다.
  * *Create.cshtml* 및 *Create.cshtml.cs*
  * *Delete.cshtml* 및 *Delete.cshtml.cs*
  * *Details.cshtml* 및 *Details.cshtml.cs*
  * *Edit.cshtml* 및 *Edit.cshtml.cs*
  * *Index.cshtml* 및 *Index.cshtml.cs*
* *Data/SchoolContext.cs* 를 만듭니다.
* *Startup.cs* 의 종속성 주입에 컨텍스트를 추가합니다.
* *appsettings.json* 에 데이터베이스 연결 문자열을 추가합니다.

## <a name="database-connection-string"></a>데이터베이스 연결 문자열

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*appsettings.json* 파일은 연결 문자열 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다.

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다. 기본적으로 LocalDB는 `C:/Users/<user>` 디렉터리에 *.mdf* 파일을 만듭니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

*CU.db* 라는 SQLite 데이터베이스 파일을 가리키도록 연결 문자열을 변경합니다.

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>데이터베이스 컨텍스트 클래스 업데이트

특정 데이터 모델에 맞게 EF Core 기능을 조정하는 주 클래스는 데이터베이스 컨텍스트 클래스입니다. 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다. 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다. 이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.

다음 코드로 *Data/SchoolContext.cs* 를 업데이트합니다.

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

강조 표시된 코드는 각 엔터티 집합에 대해 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 생성합니다. EF Core 용어에서:

* 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.
* 엔터티는 테이블의 행에 해당합니다.

엔터티 집합은 여러 엔터티를 포함하므로 DBSet 속성은 복수형 이름이어야 합니다. 스캐폴딩 도구로 `Student` DBSet를 만들었으므로 이 단계에서는 이 DBSet를 복수형 `Students`로 변경합니다. 

Razor Pages 코드가 새 DBSet 이름과 일치하게 만들려면 전체 프로젝트에서 `_context.Student`를 `_context.Students`로 전체 변경합니다.  8개 발생 항목이 있습니다.

프로젝트를 빌드하여 컴파일러 오류가 없는지 확인합니다.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다. 서비스(예: EF Core 데이터베이스 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다. 이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다. 데이터베이스 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.

스캐폴딩 도구는 종속성 주입 컨테이너에 컨텍스트 클래스를 자동으로 등록합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* `ConfigureServices`에서 강조 표시된 줄은 스캐폴더에서 추가되었습니다.

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* `ConfigureServices`에서 스캐폴더에서 추가된 코드가 `UseSqlite`를 호출하는지 확인합니다.

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다. 로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.

## <a name="create-the-database"></a>데이터베이스 만들기

*Program.cs* 를 업데이트하여 데이터베이스가 없는 경우 데이터베이스를 만듭니다.

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 메서드는 컨텍스트의 데이터베이스가 있는 경우 아무 작업도 수행하지 않습니다. 데이터베이스가 없는 경우 데이터베이스 및 스키마를 만듭니다. `EnsureCreated`를 사용하면 다음 워크플로가 데이터 모델 변경 내용을 처리할 수 있습니다.

* 데이터베이스를 삭제합니다. 모든 기존 데이터가 손실됩니다.
* 데이터 모델을 변경합니다. 예를 들어 `EmailAddress` 필드를 추가합니다.
* 앱을 실행합니다.
* `EnsureCreated`는 새 스키마를 사용하여 데이터베이스를 만듭니다.

이 워크플로는 데이터를 보존할 필요가 없는 경우 스키마가 빠르게 업데이트되는 개발 초기에 적합합니다. 데이터베이스에 입력된 데이터를 보존해야 하는 경우는 상황이 다릅니다. 이 경우에는 마이그레이션을 사용합니다.

자습서 시리즈의 뒷부분에서는 `EnsureCreated`에서 만든 데이터베이스를 삭제하고 마이그레이션을 대신 사용합니다. `EnsureCreated`에서 만든 데이터베이스는 마이그레이션을 사용하여 업데이트할 수 없습니다.

### <a name="test-the-app"></a>앱을 테스트합니다.

* 앱을 실행합니다.
* **학생** 링크 및 **새로 만들기** 를 차례로 선택합니다.
* 편집, 세부 정보 및 삭제 링크를 테스트합니다.

## <a name="seed-the-database"></a>데이터베이스 시드

`EnsureCreated` 메서드는 빈 데이터베이스를 만듭니다. 이 섹션에서는 테스트 데이터로 데이터베이스를 채우는 코드를 추가합니다.

다음 코드로 *Data/DbInitializer.cs* 를 만듭니다.
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  이 코드는 데이터베이스에 학생이 있는지 확인합니다. 학생이 없는 경우 테스트 데이터를 데이터베이스에 추가합니다. `List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 만들어 성능을 최적화합니다.

* *Program.cs* 에서 `EnsureCreated` 호출을 `DbInitializer.Initialize` 호출로 바꿉니다.

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

앱이 실행 중이라면 중지하고 **패키지 관리자 콘솔**(PMC)에서 다음 명령을 실행합니다.

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 실행 중인 경우 앱을 중지하고 *CU.db* 파일을 삭제합니다.

---

* 앱을 다시 시작합니다.

* 학생 페이지를 선택하여 시드된 데이터를 확인합니다.

## <a name="view-the-database"></a>데이터베이스 보기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio의 **보기** 메뉴에서 **SSOX(SQL Server 개체 탐색기)** 를 엽니다.
* SSOX에서 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** 를 선택합니다. 데이터베이스 이름은 이전에 제공한 컨텍스트 이름과 대시 및 GUID를 사용하여 생성됩니다.
* **테이블** 노드를 확장합니다.
* **학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기** 를 클릭하여 만든 열 및 테이블에 삽입된 행을 볼 수 있습니다.
* **Student** 테이블을 마우스 오른쪽 단추로 클릭하고 **코드 보기** 를 클릭하여 `Student` 모델이 `Student` 테이블 스키마에 어떻게 매핑되는지 확인합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

SQLite 도구를 사용하여 데이터베이스 스키마 및 시드된 데이터를 표시합니다. 데이터베이스 파일은 *CU.db* 로 이름이 지정되고 프로젝트 폴더에 배치됩니다.

---

## <a name="asynchronous-code"></a>비동기 코드

비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.

웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다. 이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다. 동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다. 비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다. 따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.

비동기 코드는 런타임 시 약간의 오버헤드를 도입합니다. 트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.

다음 코드에서 [async](/dotnet/csharp/language-reference/keywords/async) 키워드, `Task<T>` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* `async` 키워드는 컴파일러가 다음을 수행하도록 합니다.
  * 메서드 본문의 부분에 대한 콜백을 생성합니다.
  * 반환되는 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 개체를 만듭니다.
* `Task<T>` 반환 형식은 진행 중인 작업을 나타냅니다.
* `await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다. 첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다. 두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.
* `ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.

EF Core를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.

* 쿼리 또는 명령을 데이터베이스에 보내는 명령문만 비동기적으로 실행됩니다. 여기에는 `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` 및 `SaveChangesAsync`가 포함됩니다. `var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.
* EF Core 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.
* 비동기 코드의 성능 이점을 이용하려면 라이브러리 패키지(예: 페이징)가 쿼리를 데이터베이스에 보내는 EF Core 메서드를 호출하는 경우 비동기를 사용하는지 확인합니다.

.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/standard/async) 및 [Async 및 Await를 사용한 비동기 프로그래밍](/dotnet/csharp/programming-guide/concepts/async/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="step-by-step"]
> [다음 자습서](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Contoso University 샘플 웹앱은 EF(Entity Framework) Core를 사용하여 ASP.NET Core Razor Pages 앱을 만드는 방법을 보여줍니다.

샘플 앱은 가상 Contoso University의 웹 사이트입니다. 학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다. 이 페이지는 Contoso University 샘플 앱을 빌드하는 방법을 설명하는 일련의 자습서 중 첫 번째 작업입니다.

[완성된 앱을 다운로드하거나 확인하세요.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [지침을 다운로드하세요](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>사전 요구 사항

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

[Razor Pages](xref:razor-pages/index) 숙련도. 신규 프로그래머는 이 시리즈를 시작하기 전에 [Razor 페이지 시작하기](xref:tutorials/razor-pages/razor-pages-start)를 먼저 완료해야 합니다.

## <a name="troubleshooting"></a>문제 해결

해결할 수 없는 문제가 발생한 경우 일반적으로 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)와 코드를 비교하여 해결책을 찾을 수 있습니다. 도움을 얻으려면 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)에 대한 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core)에 질문을 게시하는 것이 좋습니다.

## <a name="the-contoso-university-web-app"></a>Contoso University 웹앱

이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다.

사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다. 자습서에서 만든 화면 중 몇 가지 예가 나와 있습니다.

![학생 인덱스 페이지](intro/_static/students-index.png)

![학생 편집 페이지](intro/_static/student-edit.png)

이 사이트의 UI 스타일은 기본 제공 템플릿에서 생성된 것과 가깝습니다. 이 자습서에서는 UI가 아닌 Razor 페이지를 사용한 EF Core를 중점적으로 다룹니다.

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a>ContosoUniversity Razor Pages 웹앱 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트** 를 선택합니다.
* 새 ASP.NET Core 웹 애플리케이션을 만듭니다. 프로젝트 이름을 **ContosoUniversity** 로 지정합니다. 코드를 복사/붙여넣을 때 네임스페이스와 일치할 수 있도록 프로젝트 이름을 *ContosoUniversity* 로 지정하는 것이 중요합니다.
* 드롭다운에서 **ASP.NET Core 2.1** 을 선택한 다음, **웹 애플리케이션** 을 선택합니다.

이전 단계의 이미지는 [Razor 웹앱 만들기](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)를 참조하세요.
앱을 실행합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>사이트 스타일 설정

몇 가지 변경 내용으로 사이트 메뉴, 레이아웃 및 홈페이지를 설정합니다. *Pages/Shared/_Layout.cshtml* 을 다음 변경 내용으로 업데이트합니다.

* 모든 “ContosoUniversity”를 “Contoso University”로 변경합니다. 세 번 나옵니다.

* **학생**, **강좌**, **강사** 및 **부서** 에 대한 메뉴 항목을 추가하고 **연락처** 메뉴 항목을 삭제합니다.

변경 내용은 강조 표시되어 있습니다. (모든 표시가 표시되지는 *않습니다*.)

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

*Pages/Index.cshtml* 에서 다음 코드로 파일의 콘텐츠를 텍스트를 대체하여 ASP.NET 및 MVC에 대한 텍스트를 이 앱에 대한 텍스트로 바꿉니다.

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>데이터 모델 만들기

Contoso University 앱에 대한 엔터티 클래스를 만듭니다. 다음과 같은 세 가지 엔터티로 시작합니다.

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

`Student` 및 `Enrollment` 엔터티 사이에 일 대 다 관계가 있습니다. `Course` 및 `Enrollment` 엔터티 사이에 일 대 다 관계가 있습니다. 학생은 여러 강좌에 등록할 수 있습니다. 강좌는 등록된 학생이 여러 명일 수 있습니다.

다음 섹션에서 각각에 대한 이러한 엔터티에 대한 클래스를 만듭니다.

### <a name="the-student-entity"></a>학생 엔터티

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

*모델* 폴더를 만듭니다. *모델* 폴더에서 다음 코드로 *Student.cs* 라는 이름의 클래스 파일을 만듭니다.

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

`ID` 속성은 이 클래스에 해당하는 DB(데이터베이스) 테이블의 기본 키 열이 됩니다. 기본적으로 EF 코어는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다. `classnameID`에서 `classname`은 클래스의 이름입니다. 기본 키가 자동으로 인식되는 경우 대안은 앞의 예제에서 `StudentID`입니다.

`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다. 탐색 속성은 이 엔터티와 관련된 다른 엔터티에 연결됩니다. 이 경우 `Student entity`의 `Enrollments` 속성은 해당 `Student`에 관련된 모든 `Enrollment` 엔터티를 포함합니다. 예를 들어 DB의 학생 행에 두 개의 관련 등록 행이 있는 경우 `Enrollments` 탐색 속성은 그 두 `Enrollment` 엔터티를 포함합니다. 관련된 `Enrollment` 행은 `StudentID` 열에서 해당 학생의 기본 키 값을 포함하는 열입니다. 예를 들어 ID=1인 학생에 `Enrollment` 테이블의 두 개 행이 있다고 가정합니다. `Enrollment` 테이블에 `StudentID` = 1인 두 개의 행이 있습니다. `StudentID`는 `Student` 테이블에서 학생을 지정하는 `Enrollment` 테이블의 외래 키입니다.

탐색 속성이 여러 엔터티를 포함하는 경우 탐색 속성은 `ICollection<T>`와 같은 목록 유형이어야 합니다. `ICollection<T>`는 지정할 수 있으며, `List<T>` 또는 `HashSet<T>`와 같은 형식일 수 있습니다. `ICollection<T>`가 사용되는 경우 EF Core는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다. 여러 엔터티를 포함하는 탐색 속성은 다대다 및 일대다 관계에서 제공됩니다.

### <a name="the-enrollment-entity"></a>등록 엔터티

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

*모델* 폴더에서 다음 코드로 *Enrollment.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID` 속성은 기본 키입니다. 이 엔터티는 `Student` 엔터티 같은 `ID` 대신 `classnameID` 패턴을 사용합니다. 일반적으로 개발자는 하나의 패턴을 선택하여 데이터 모델 전체에 사용합니다. 자습서의 뒷부분에서는 클래스 이름 없이 ID를 사용하여 더 손쉽게 데이터 모델에서 상속을 구현하는 내용이 나옵니다.

`Grade` 속성은 `enum`입니다. `Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 nullable이라는 것을 나타냅니다. Null인 등급은 0 등급과는 다릅니다. Null은 알려지지 않거나 아직 등록되지 않은 등급을 의미합니다.

`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다. `Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티를 포함합니다. `Student` 엔터티는 여러 `Enrollment` 엔터티를 포함하는 `Student.Enrollments` 탐색 속성과 다릅니다.

`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다. `Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.

EF Core는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키로 해석합니다. 예를 들어 `Student` 탐색 속성의 경우 `Student` 엔터티의 기본 키가 `ID`이므로 `StudentID`입니다. 외래 키 속성의 이름은 `<primary key property name>`으로 지정할 수 있습니다. 예를 들어 `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`라고 할 수 있습니다.

### <a name="the-course-entity"></a>강좌 엔터티

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

*모델* 폴더에서 다음 코드로 *Course.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

`Enrollments` 속성은 탐색 속성입니다. `Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.

앱은 `DatabaseGenerated` 특성을 통해 DB가 생성하도록 하는 대신 기본 키를 지정할 수 있습니다.

## <a name="scaffold-the-student-model"></a>학생 모델 스캐폴드

이 섹션에서는 학생 모델을 스캐폴드합니다. 즉, 스캐폴드 도구는 학생 모델에서 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 위한 페이지를 생성합니다.

* 프로젝트를 빌드합니다.
* *Pages/Students* 폴더를 만듭니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기** 에서 *Pages/Students* 폴더 > **추가** > **스캐폴드 항목 새로 만들기** 를 마우스 오른쪽 단추로 클릭합니다.
* **스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가** 를 선택합니다.

**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.

* **모델 클래스** 드롭다운에서 **학생(ContosoUniversity.Models)** 을 선택합니다.
* **데이터 컨텍스트 클래스** 행에서 **+** (더하기)를 선택 하고 생성된 이름을 서명하고 **ContosoUniversity.Models.SchoolContext** 로 변경합니다.
* **데이터 컨텍스트 클래스** 드롭다운에서 **ContosoUniversity.Models.SchoolContext** 를 선택합니다.
* **추가** 를 선택합니다.

![CRUD 대화 상자](intro/_static/s1.png)

이전 단계에서 문제가 발생한 경우 [영화 모델 스캐폴드](xref:tutorials/razor-pages/model#scaffold-the-movie-model)를 참조하세요.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

다음 명령을 실행하여 학생 모델을 스캐폴드합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

스캐폴드 프로세스는 다음 파일을 생성하고 변경했습니다.

### <a name="files-created"></a>생성된 파일

* *Pages/Students* 만들기, 삭제, 세부 정보, 편집, 인덱스입니다.
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>파일 업데이트

* *Startup.cs* : 이 파일의 변경 내용은 다음 섹션에서 자세히 설명합니다.
* *appsettings.json* : 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열이 추가됩니다.

## <a name="examine-the-context-registered-with-dependency-injection"></a>종속성 주입을 사용하여 등록된 컨텍스트 확인

ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다. 서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다. 이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다. DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.

스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.

*Startup.cs* 의 `ConfigureServices` 메서드를 검사합니다. 강조 표시된 줄은 스캐폴더에서 추가되었습니다.

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다. 로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.

## <a name="update-main"></a>기본 업데이트

*Program.cs* 에서 다음을 수행하는 `Main` 메서드를 수정합니다.

* 종속성 주입 컨테이너에서 DB 컨텍스트 인스턴스를 가져옵니다.
* [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)를 호출합니다.
* `EnsureCreated` 메서드가 완료되면 컨텍스트를 삭제합니다.

다음 코드는 업데이트된 *Program.cs* 파일을 보여 줍니다.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated`는 컨텍스트에 대한 데이터베이스가 있는지 확인합니다. 존재하는 경우 아무런 동작이 발생하지 않습니다. 존재하지 않는 경우 데이터베이스 및 해당하는 모든 스키마가 생성됩니다. `EnsureCreated`는 데이터베이스를 만드는 데 마이그레이션을 사용하지 않습니다. `EnsureCreated`를 사용하여 만든 데이터베이스는 나중에 마이그레이션을 사용하여 업데이트될 수 없습니다.

앱 시작 시 다음 작업 흐름을 허용하는 `EnsureCreated`가 호출됩니다.

* DB를 삭제합니다.
* DB 스키마를 변경합니다(예: `EmailAddress` 필드 추가).
* 앱을 실행합니다.
* `EnsureCreated`가 `EmailAddress` 열이 있는 DB를 만듭니다.

`EnsureCreated`는 스키마가 빠르게 발전하는 경우 개발 초기에 편리합니다. 나중에 자습서에서 DB가 삭제되고 마이그레이션이 사용됩니다.

### <a name="test-the-app"></a>앱 테스트

앱을 실행하고 cookie 정책에 동의합니다. 이 앱은 개인 정보를 보관하지 않습니다. [EU GDPR(일반 데이터 보호 규정) 지원](xref:security/gdpr)에서 cookie 정책을 확인할 수 있습니다.

* **학생** 링크 및 **새로 만들기** 를 차례로 선택합니다.
* 편집, 세부 정보 및 삭제 링크를 테스트합니다.

## <a name="examine-the-schoolcontext-db-context"></a>SchoolContext DB 컨텍스트 검사

특정 데이터 모델에 맞게 EF Core 기능을 조정하는 주 클래스는 DB 컨텍스트 클래스입니다. 데이터 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다. 데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다. 이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.

*SchoolContext.cs* 를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

강조 표시된 코드는 각 엔터티 집합에 대해 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 생성합니다. EF Core 용어에서:

* 엔터티 집합은 일반적으로 DB 테이블에 해당합니다.
* 엔터티는 테이블의 행에 해당합니다.

`DbSet<Enrollment>` 및 `DbSet<Course>`를 생략할 수 있습니다. `Student` 엔터티는 `Enrollment` 엔터티를 참조하고 `Enrollment` 엔터티는 `Course` 엔터티를 참조하기 때문에 EF Core는 이를 암시적으로 포함합니다. 이 자습서의 경우 `DbSet<Enrollment>` 및 `DbSet<Course>`를 `SchoolContext`에 유지합니다.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

연결 문자열은 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다. LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다. LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다. 기본적으로 LocalDB는 *.mdf* DB 파일을 `C:/Users/<user>` 디렉터리에 만듭니다.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>코드를 추가하여 테스트 데이터로 DB 초기화

EF Core가 빈 DB를 만듭니다. 이 섹션에서는 테스트 데이터로 채울 `Initialize` 메서드가 작성됩니다.

*Data* 폴더에서 *DbInitializer.cs* 라는 새 클래스 파일을 만들고 다음 코드를 추가합니다.

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

참고: 앞의 코드는 `Data`가 아닌 `Models`을 네임스페이스(`namespace ContosoUniversity.Models`)에 사용합니다. `Models`는 스캐폴더에서 생성된 코드와 일치합니다. 자세한 내용은 [이 GitHub 스캐폴딩 문제](https://github.com/aspnet/Scaffolding/issues/822)를 참조하세요.

코드는 DB에 학생이 있는지를 확인합니다. DB에 학생이 없는 경우 DB는 테스트 데이터로 초기화됩니다. `List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 로드하여 성능을 최적화합니다.

`EnsureCreated` 메서드는 자동으로 DB 컨텍스트에 대한 DB를 만듭니다. DB가 있는 경우 `EnsureCreated`는 DB를 수정하지 않고 반환합니다.

*Program.cs* 에서 `Initialize`를 호출하도록 `Main` 메서드를 수정합니다.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

앱이 실행 중이라면 중지하고 **패키지 관리자 콘솔**(PMC)에서 다음 명령을 실행합니다.

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 실행 중인 경우 앱을 중지하고 *CU.db* 파일을 삭제합니다.

---

## <a name="view-the-db"></a>DB 보기

데이터베이스 이름은 이전에 제공한 컨텍스트 이름과 대시 및 GUID를 사용하여 생성됩니다. 따라서 데이터베이스 이름은 “SchoolContext-{GUID}”입니다. GUID는 사용자에 따라 다릅니다.
Visual Studio의 **보기** 메뉴에서 **SSOX(SQL Server 개체 탐색기)** 를 엽니다.
SSOX에서 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** 를 클릭합니다.

**테이블** 노드를 확장합니다.

**학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기** 를 클릭하여 만든 열 및 테이블에 삽입된 행을 볼 수 있습니다.

## <a name="asynchronous-code"></a>비동기 코드

비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.

웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다. 이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다. 동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다. 비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다. 따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.

비동기 코드는 런타임 시 약간의 오버헤드를 도입합니다. 트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.

다음 코드에서 [async](/dotnet/csharp/language-reference/keywords/async) 키워드, `Task<T>` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* `async` 키워드는 컴파일러가 다음을 수행하도록 합니다.
  * 메서드 본문의 부분에 대한 콜백을 생성합니다.
  * 반환되는 [작업](/dotnet/api/system.threading.tasks.task) 개체를 자동으로 만듭니다. 자세한 내용은 [작업 반환 형식](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)을 참조하세요.

* 암시적 반환 형식 `Task`는 진행 중인 작업을 나타냅니다.
* `await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다. 첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다. 두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.
* `ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.

EF Core를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.

* 쿼리 또는 명령을 DB에 보내는 명령문만 비동기적으로 실행됩니다. 여기에는 `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` 및 `SaveChangesAsync`가 포함됩니다. `var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.
* EF Core 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.
* 비동기 코드의 성능 이점을 활용하려면 쿼리를 DB에 보내는 EF Core 메서드를 호출하는 경우 라이브러리 패키지(예: 페이징)가 비동기를 사용하는지 확인합니다.

.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/standard/async) 및 [Async 및 Await를 사용한 비동기 프로그래밍](/dotnet/csharp/programming-guide/concepts/async/)을 참조하세요.

다음 자습서에서는 기본 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 검사합니다.



## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [다음](xref:data/ef-rp/crud)

::: moniker-end
