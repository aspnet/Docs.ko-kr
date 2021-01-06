<span data-ttu-id="90338-101">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="90338-101">Run the following .NET Core CLI commands:</span></span>

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

<span data-ttu-id="90338-102">앞의 명령은 다음을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="90338-102">The preceding commands add:</span></span>

* <span data-ttu-id="90338-103">[aspnet-codegenerator 스캐폴딩 도구](xref:fundamentals/tools/dotnet-aspnet-codegenerator)</span><span class="sxs-lookup"><span data-stu-id="90338-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="90338-104">.NET Core CLI에 대한 Entity Framework Core 도구.</span><span class="sxs-lookup"><span data-stu-id="90338-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="90338-105">EF Core 패키지를 종속성으로 설치하는 EF Core SQLite 공급자.</span><span class="sxs-lookup"><span data-stu-id="90338-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="90338-106">스캐폴딩에 필요한 패키지: `Microsoft.VisualStudio.Web.CodeGeneration.Design` 및 `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="90338-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="90338-107">앱이 환경별로 데이터베이스 컨텍스트를 구성하도록 허용하는 여러 환경 구성에 대한 지침은 <xref:fundamentals/environments#environment-based-startup-class-and-methods>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90338-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]