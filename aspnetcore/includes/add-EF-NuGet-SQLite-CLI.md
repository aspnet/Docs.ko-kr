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
ms.openlocfilehash: 1a9e98a84093f89f3859454f482dfe59c8504d9b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552663"
---
다음 .NET Core CLI 명령을 실행합니다.

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.1.9
dotnet tool install --global dotnet-aspnet-codegenerator --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.9
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore --version 3.1.9
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.9
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.9
dotnet add package Microsoft.Extensions.Logging.Debug --version 3.1.9
```

앞의 명령은 다음을 추가합니다.

* [aspnet-codegenerator 스캐폴딩 도구](xref:fundamentals/tools/dotnet-aspnet-codegenerator)
* .NET Core CLI에 대한 Entity Framework Core 도구.
* EF Core 패키지를 종속성으로 설치하는 EF Core SQLite 공급자.
* 스캐폴딩에 필요한 패키지: `Microsoft.VisualStudio.Web.CodeGeneration.Design` 및 `Microsoft.EntityFrameworkCore.SqlServer`.

앱이 환경별로 데이터베이스 컨텍스트를 구성하도록 허용하는 여러 환경 구성에 대한 지침은 <xref:fundamentals/environments#environment-based-startup-class-and-methods>를 참조하세요.

[!INCLUDE[](~/includes/scaffoldTFM.md)]