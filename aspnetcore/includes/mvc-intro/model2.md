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
ms.openlocfilehash: 44cef0c1491cc609dd9b821910014ec88ecaad81
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552119"
---
::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

*Data* 폴더를 만듭니다.

다음 `MvcMovieContext` 클래스를 *Data* 폴더에 추가:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

이전 코드에서는 엔터티 집합에 대한 `DbSet` 속성을 만듭니다. Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>데이터베이스 연결 문자열 추가

*appsettings.json* 파일에 연결 문자열을 추가합니다.

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>NuGet 패키지 및 EF 도구 추가

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>데이터베이스 컨텍스트 등록

*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

데이터베이스 컨텍스트를 `Startup.ConfigureServices`의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

다음 `MvcMovieContext` 클래스를 *Models* 폴더에 추가합니다.  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

이전 코드에서는 엔터티 집합에 대한 `DbSet` 속성을 만듭니다. Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>데이터베이스 연결 문자열 추가

*appsettings.json* 파일에 연결 문자열을 추가합니다.

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>필요한 NuGet 패키지 추가

다음 .NET Core CLI 명령을 실행하여 SQLite 및 CodeGeneration.Design을 프로젝트에 추가합니다.

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

스캐폴딩에는 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 패키지가 필요합니다.

<a name="reg"></a>

### <a name="register-the-database-context"></a>데이터베이스 컨텍스트 등록

*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

데이터베이스 컨텍스트를 `Startup.ConfigureServices`의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

오류에 대한 검사 방법으로 프로젝트를 빌드합니다.
::: moniker-end