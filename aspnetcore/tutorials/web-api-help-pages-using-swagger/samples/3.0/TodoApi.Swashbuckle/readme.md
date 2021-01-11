---
page_type: sample
description: ASP.NET Core Web API 프로젝트에 Swashbuckle을 추가하여 Swagger UI를 통합하는 방법을 알아봅니다.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: e02247325f430b0ce23dbb3f5bc344a60a1a164a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "78647589"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="0d819-102">Swashbuckle 및 ASP.NET Core 시작</span><span class="sxs-lookup"><span data-stu-id="0d819-102">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="0d819-103">Web API를 사용할 때 여러 메서드를 이해하는 것이 개발자에게 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-103">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="0d819-104">[Swagger](https://swagger.io/)([OpenAPI](https://www.openapis.org/)라고도 함)는 Web API에 대한 유용한 설명서 및 도움말 페이지를 생성하는 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-104">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="0d819-105">대화형 설명서, 클라이언트 SDK 생성 및 API 검색 기능과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-105">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="0d819-106">이 샘플에서는 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 및 .NET 구현을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-106">In this sample, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) the .NET implementation is shown.</span></span>

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="0d819-107">Swagger 미들웨어 추가 및 구성</span><span class="sxs-lookup"><span data-stu-id="0d819-107">Add and configure Swagger middleware</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

<span data-ttu-id="0d819-108">`Startup.Configure` 메서드에서 생성된 JSON 문서 및 Swagger UI를 지원하기 위해 미들웨어를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-108">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1"); 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="0d819-109">위의 `UseSwaggerUI` 메서드 호출은 [정적 파일 미들웨어](https://docs.microsoft.com/aspnet/core/fundamentals/static-files)를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-109">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span></span> <span data-ttu-id="0d819-110">.NET Framework 또는 NET Core 1.x를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-110">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="0d819-111">앱을 시작하고 `http://localhost:<port>/swagger/v1/swagger.json`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-111">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="0d819-112">엔드포인트를 설명하는 생성된 문서는 [Swagger 사양(swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)에 표시된 것처럼 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-112">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="0d819-113">Swagger UI는 `http://localhost:<port>/swagger`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-113">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="0d819-114">Swagger UI를 통해 API를 탐색하고 다른 프로그램에 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-114">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="0d819-115">앱의 루트(`http://localhost:<port>/`)에서 Swagger UI를 제공하려면 `RoutePrefix` 속성을 빈 문자열로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-115">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

<span data-ttu-id="0d819-116">IIS 또는 역방향 프록시에서 디렉터리를 사용하는 경우 `./` 접두사를 사용하여 Swagger 엔드포인트를 상대 경로로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-116">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="0d819-117">예: `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="0d819-117">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="0d819-118">`/swagger/v1/swagger.json`을 사용하면 앱이 URL의 실제 루트(사용되는 경우 경로 접두사)에서 JSON 파일을 찾도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-118">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="0d819-119">예를 들어 `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json` 대신 `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-119">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="0d819-120">사용자 지정 및 확장</span><span class="sxs-lookup"><span data-stu-id="0d819-120">Customize and extend</span></span>

<span data-ttu-id="0d819-121">Swagger는 개체 모델을 설명하고 테마와 일치하도록 UI를 사용자 지정하는 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-121">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="0d819-122">`Startup` 클래스에서 다음 네임스페이스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-122">In the `Startup` class, add the following namespaces:</span></span>
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="0d819-123">API 정보 및 설명</span><span class="sxs-lookup"><span data-stu-id="0d819-123">API info and description</span></span>

<span data-ttu-id="0d819-124">`AddSwaggerGen` 메서드에 전달되는 구성 작업은 작성자, 라이선스 및 설명과 같은 정보를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-124">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

<span data-ttu-id="0d819-125">Swagger UI는 버전의 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-125">The Swagger UI displays the version's information:</span></span>

![버전 정보가 포함된 Swagger UI: 설명, 작성자 및 추가 링크 확인](sample_images/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="0d819-127">XML 주석</span><span class="sxs-lookup"><span data-stu-id="0d819-127">XML comments</span></span>

<span data-ttu-id="0d819-128">XML 주석은 다음 방법으로 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-128">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="0d819-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d819-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d819-130">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **<project_name>.csproj 편집** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-130">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="0d819-131">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-131">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d819-132">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d819-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0d819-133">*Solution Pad* 에서 **control** 키를 누르고 프로젝트 이름을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-133">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="0d819-134">**도구** > **파일 편집** 으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-134">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="0d819-135">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-135">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[<span data-ttu-id="0d819-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d819-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0d819-137">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-137">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

<span data-ttu-id="0d819-138">XML 주석을 사용하면 문서화되지 않은 public 형식과 멤버에 대한 디버그 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-138">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="0d819-139">문서화되지 않은 형식 및 멤버는 경고 메시지로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-139">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="0d819-140">예를 들어 다음 메시지는 경고 코드 1591 위반을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-140">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="0d819-141">프로젝트 전반에서 경고를 표시하지 않으려면 프로젝트 파일에서 무시할 경고 코드의 세미콜론으로 구분된 목록을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-141">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="0d819-142">`$(NoWarn);`에 경고 코드를 추가하면 [C# 기본값](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-142">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

<span data-ttu-id="0d819-143">특정 멤버에 대해서만 경고를 표시하지 않으려면 [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) 전처리기 지시문에 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-143">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="0d819-144">이 방법은 API 문서를 통해 노출하지 않아야 하는 코드에 유용합니다. 다음 예제에서는 전체 `Program` 클래스에 대해 경고 코드 CS1591이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-144">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="0d819-145">클래스 정의를 닫으면 경고 코드 적용이 복원됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-145">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="0d819-146">여러 경고 코드는 쉼표로 구분된 목록으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-146">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="0d819-147">이전 지침에 따라 생성된 XML 파일을 사용하도록 Swagger를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-147">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="0d819-148">Linux 또는 Windows가 아닌 운영 체제의 경우 파일 이름 및 경로는 대/소문자를 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-148">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="0d819-149">예를 들어 *TodoApi.XML* 파일은 Windows에는 유효하지만 CentOS에는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-149">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

<span data-ttu-id="0d819-150">이전 코드에서 [리플렉션](/dotnet/csharp/programming-guide/concepts/reflection)은 웹 API 프로젝트의 이름과 일치하는 XML 파일 이름을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-150">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="0d819-151">[AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) 속성은 XML 파일에 대한 경로를 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-151">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="0d819-152">일부 Swagger 기능(예: 각 특성에서 입력 매개 변수 또는 HTTP 메서드와 응답 코드의 schemata)은 XML 문서 파일을 사용하지 않고 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-152">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="0d819-153">대부분 기능, 메서드 요약 및 매개 변수/응답 코드 설명의 경우 XML 파일을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-153">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="0d819-154">작업에 3중 슬래시 주석을 추가하면 섹션 헤더에 설명이 추가되어 Swagger UI가 향상됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-154">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="0d819-155">`Delete` 작업 위에 [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-155">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
<span data-ttu-id="0d819-156">Swagger UI는 이전 코드 `<summary>` 요소의 내부 텍스트를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-156">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![DELETE 메서드에 대한 XML 주석 ‘특정 TodoItem을 삭제합니다.’를 보여 주는](sample_images/triple-slash-comments.png)

<span data-ttu-id="0d819-159">UI는 생성된 JSON 스키마에 의해 구동됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-159">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```
<span data-ttu-id="0d819-160">[\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) 요소를 `Create` 작업 메서드 문서에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-160">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="0d819-161">이는 `<summary>` 요소에 지정된 정보를 보충하고 더 강력한 Swagger UI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-161">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="0d819-162">`<remarks>` 요소 콘텐츠는 텍스트, JSON 또는 XML로 구성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-162">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
<span data-ttu-id="0d819-163">이러한 추가 주석이 포함된 향상된 UI 기능을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-163">Notice the UI enhancements with these additional comments:</span></span>

![추가 주석이 표시된 Swagger UI](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="0d819-165">데이터 주석</span><span class="sxs-lookup"><span data-stu-id="0d819-165">Data annotations</span></span>

<span data-ttu-id="0d819-166">[System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 네임스페이스에 있는 특성으로 모델을 표시하여 Swagger UI 구성 요소를 구동합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-166">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="0d819-167">`[Required]` 특성을 `TodoItem` 클래스의 `Name` 속성에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-167">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="0d819-168">이 특성이 있으면 UI 동작이 변경되고 기본 JSON 스키마가 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-168">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="0d819-169">API 컨트롤러에 `[Produces("application/json")]` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-169">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="0d819-170">이 특성은 컨트롤러 동작이 *application/json* 의 응답 콘텐츠 형식을 지원함을 선언하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-170">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
<span data-ttu-id="0d819-171">컨트롤러 GET 작업의 경우 **응답 콘텐츠 형식** 드롭다운에서 이 콘텐츠 형식이 기본값으로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-171">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![기본 응답 콘텐츠 형식이 포함된 Swagger UI](sample_images/json-response-content-type.png)

<span data-ttu-id="0d819-173">웹 API에서 데이터 주석 사용이 증가하면 UI 및 API 도움말 페이지에는 더 자세한 설명과 유용한 정보가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-173">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="0d819-174">응답 형식 설명</span><span class="sxs-lookup"><span data-stu-id="0d819-174">Describe response types</span></span>

<span data-ttu-id="0d819-175">웹 API를 사용하는 개발자는 반환된 내용&mdash; 중에서도 특히 응답 형식 및 오류 코드(표준이 아닌 경우)를 가장 중요하게 생각합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-175">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="0d819-176">응답 형식 및 오류 코드는 XML 주석 및 데이터 주석에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-176">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="0d819-177">`Create` 작업은 성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-177">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="0d819-178">게시된 요청 본문이 null일 경우 HTTP 400 상태 코드가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-178">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="0d819-179">Swagger UI에 적절한 문서가 없으면 소비자는 이러한 예상 결과에 대한 정보를 알 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-179">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="0d819-180">다음 예제에서 강조 표시된 줄을 추가하여 해당 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-180">Fix that problem by adding the highlighted lines in the following example:</span></span>

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

<span data-ttu-id="0d819-181">이제 Swagger UI에서는 예상 HTTP 응답 코드를 분명히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-181">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![POST 응답 클래스 설명 ‘새로 만들어진 Todo 항목 반환’ 및 ‘400 - 응답 메시지에서 항목의 상태 코드 및 이유가 null인 경우’를 보여 주는 Swagger UI](sample_images/data-annotations-response-types.png)

<span data-ttu-id="0d819-183">ASP.NET Core 2.2 이상에서는 `[ProducesResponseType]`을 사용하여 명시적으로 개별 작업을 데코레이팅하는 대신 규칙을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d819-183">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="0d819-184">자세한 내용은 [웹 API 규칙 사용](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0d819-184">For more information, see [Use web API conventions](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span></span>

<span data-ttu-id="0d819-185">UI 사용자 지정에 대한 자세한 내용은 다음을 참조하세요. [UI 사용자 지정](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span><span class="sxs-lookup"><span data-stu-id="0d819-185">For information on customizing the UI see: [Customize the UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span></span>
