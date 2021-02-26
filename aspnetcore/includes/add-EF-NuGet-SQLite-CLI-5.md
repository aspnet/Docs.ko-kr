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
ms.openlocfilehash: 27cb64dc8734fcb6d165795515096c9d9dd2a9cc
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551451"
---
<span data-ttu-id="24130-101">다음 .NET CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="24130-101">Run the following .NET CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

<span data-ttu-id="24130-102">앞의 명령은 다음을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="24130-102">The preceding commands add:</span></span>

* <span data-ttu-id="24130-103">[aspnet-codegenerator 스캐폴딩 도구](xref:fundamentals/tools/dotnet-aspnet-codegenerator)</span><span class="sxs-lookup"><span data-stu-id="24130-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="24130-104">.NET CLI를 위한 EF Core 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="24130-104">The EF Core Tools for the .NET CLI.</span></span>
* <span data-ttu-id="24130-105">EF Core 패키지를 종속성으로 설치하는 EF Core SQLite 공급자.</span><span class="sxs-lookup"><span data-stu-id="24130-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="24130-106">스캐폴딩에 필요한 패키지: `Microsoft.VisualStudio.Web.CodeGeneration.Design` 및 `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="24130-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="24130-107">앱이 환경별로 데이터베이스 컨텍스트를 구성하도록 허용하는 여러 환경 구성에 대한 지침은 <xref:fundamentals/environments#environment-based-startup-class-and-methods>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="24130-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
