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
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Swashbuckle 및 ASP.NET Core 시작

Web API를 사용할 때 여러 메서드를 이해하는 것이 개발자에게 어려울 수 있습니다. [Swagger](https://swagger.io/)([OpenAPI](https://www.openapis.org/)라고도 함)는 Web API에 대한 유용한 설명서 및 도움말 페이지를 생성하는 문제를 해결합니다. 대화형 설명서, 클라이언트 SDK 생성 및 API 검색 기능과 같은 이점을 제공합니다.

이 샘플에서는 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 및 .NET 구현을 보여줍니다.

## <a name="add-and-configure-swagger-middleware"></a>Swagger 미들웨어 추가 및 구성

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

`Startup.Configure` 메서드에서 생성된 JSON 문서 및 Swagger UI를 지원하기 위해 미들웨어를 사용하도록 설정합니다.

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

위의 `UseSwaggerUI` 메서드 호출은 [정적 파일 미들웨어](https://docs.microsoft.com/aspnet/core/fundamentals/static-files)를 활성화합니다. .NET Framework 또는 NET Core 1.x를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet 패키지를 프로젝트에 추가합니다.

앱을 시작하고 `http://localhost:<port>/swagger/v1/swagger.json`으로 이동합니다. 엔드포인트를 설명하는 생성된 문서는 [Swagger 사양(swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)에 표시된 것처럼 나타납니다.

Swagger UI는 `http://localhost:<port>/swagger`에 있습니다. Swagger UI를 통해 API를 탐색하고 다른 프로그램에 통합합니다.

> [!TIP]
> 앱의 루트(`http://localhost:<port>/`)에서 Swagger UI를 제공하려면 `RoutePrefix` 속성을 빈 문자열로 설정합니다.
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

IIS 또는 역방향 프록시에서 디렉터리를 사용하는 경우 `./` 접두사를 사용하여 Swagger 엔드포인트를 상대 경로로 설정합니다. 예: `./swagger/v1/swagger.json`. `/swagger/v1/swagger.json`을 사용하면 앱이 URL의 실제 루트(사용되는 경우 경로 접두사)에서 JSON 파일을 찾도록 지시합니다. 예를 들어 `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json` 대신 `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json`을 사용합니다.

## <a name="customize-and-extend"></a>사용자 지정 및 확장

Swagger는 개체 모델을 설명하고 테마와 일치하도록 UI를 사용자 지정하는 옵션을 제공합니다.

`Startup` 클래스에서 다음 네임스페이스를 추가합니다.
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>API 정보 및 설명

`AddSwaggerGen` 메서드에 전달되는 구성 작업은 작성자, 라이선스 및 설명과 같은 정보를 추가합니다.

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

Swagger UI는 버전의 정보를 표시합니다.

![버전 정보가 포함된 Swagger UI: 설명, 작성자 및 추가 링크 확인](sample_images/custom-info.png)

### <a name="xml-comments"></a>XML 주석

XML 주석은 다음 방법으로 사용하도록 설정할 수 있습니다.

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **<project_name>.csproj 편집** 을 선택합니다.
* 강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* *Solution Pad* 에서 **control** 키를 누르고 프로젝트 이름을 클릭합니다. **도구** > **파일 편집** 으로 이동합니다.
* 강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

XML 주석을 사용하면 문서화되지 않은 public 형식과 멤버에 대한 디버그 정보를 제공합니다. 문서화되지 않은 형식 및 멤버는 경고 메시지로 표시됩니다. 예를 들어 다음 메시지는 경고 코드 1591 위반을 나타냅니다.

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

프로젝트 전반에서 경고를 표시하지 않으려면 프로젝트 파일에서 무시할 경고 코드의 세미콜론으로 구분된 목록을 정의합니다. `$(NoWarn);`에 경고 코드를 추가하면 [C# 기본값](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)도 적용됩니다.

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

특정 멤버에 대해서만 경고를 표시하지 않으려면 [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) 전처리기 지시문에 코드를 포함합니다. 이 방법은 API 문서를 통해 노출하지 않아야 하는 코드에 유용합니다. 다음 예제에서는 전체 `Program` 클래스에 대해 경고 코드 CS1591이 무시됩니다. 클래스 정의를 닫으면 경고 코드 적용이 복원됩니다. 여러 경고 코드는 쉼표로 구분된 목록으로 지정합니다.

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

이전 지침에 따라 생성된 XML 파일을 사용하도록 Swagger를 구성합니다. Linux 또는 Windows가 아닌 운영 체제의 경우 파일 이름 및 경로는 대/소문자를 구분할 수 있습니다. 예를 들어 *TodoApi.XML* 파일은 Windows에는 유효하지만 CentOS에는 그렇지 않습니다.

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

이전 코드에서 [리플렉션](/dotnet/csharp/programming-guide/concepts/reflection)은 웹 API 프로젝트의 이름과 일치하는 XML 파일 이름을 빌드하는 데 사용됩니다. [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) 속성은 XML 파일에 대한 경로를 생성하는 데 사용됩니다. 일부 Swagger 기능(예: 각 특성에서 입력 매개 변수 또는 HTTP 메서드와 응답 코드의 schemata)은 XML 문서 파일을 사용하지 않고 작동합니다. 대부분 기능, 메서드 요약 및 매개 변수/응답 코드 설명의 경우 XML 파일을 사용해야 합니다.

작업에 3중 슬래시 주석을 추가하면 섹션 헤더에 설명이 추가되어 Swagger UI가 향상됩니다. `Delete` 작업 위에 [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) 요소를 추가합니다.

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
Swagger UI는 이전 코드 `<summary>` 요소의 내부 텍스트를 표시합니다.

![DELETE 메서드에 대한 XML 주석 ‘특정 TodoItem을 삭제합니다.’를 보여 주는 Swagger UI](sample_images/triple-slash-comments.png)

UI는 생성된 JSON 스키마에 의해 구동됩니다.

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
[\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) 요소를 `Create` 작업 메서드 문서에 추가합니다. 이는 `<summary>` 요소에 지정된 정보를 보충하고 더 강력한 Swagger UI를 제공합니다. `<remarks>` 요소 콘텐츠는 텍스트, JSON 또는 XML로 구성될 수 있습니다.

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
이러한 추가 주석이 포함된 향상된 UI 기능을 확인할 수 있습니다.

![추가 주석이 표시된 Swagger UI](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a>데이터 주석

[System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 네임스페이스에 있는 특성으로 모델을 표시하여 Swagger UI 구성 요소를 구동합니다.

`[Required]` 특성을 `TodoItem` 클래스의 `Name` 속성에 추가합니다.

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

이 특성이 있으면 UI 동작이 변경되고 기본 JSON 스키마가 변경됩니다.

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

API 컨트롤러에 `[Produces("application/json")]` 특성을 추가합니다. 이 특성은 컨트롤러 동작이 *application/json* 의 응답 콘텐츠 형식을 지원함을 선언하는 데 사용됩니다.

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
컨트롤러 GET 작업의 경우 **응답 콘텐츠 형식** 드롭다운에서 이 콘텐츠 형식이 기본값으로 선택됩니다.

![기본 응답 콘텐츠 형식이 포함된 Swagger UI](sample_images/json-response-content-type.png)

웹 API에서 데이터 주석 사용이 증가하면 UI 및 API 도움말 페이지에는 더 자세한 설명과 유용한 정보가 제공됩니다.

### <a name="describe-response-types"></a>응답 형식 설명

웹 API를 사용하는 개발자는 반환된 내용&mdash; 중에서도 특히 응답 형식 및 오류 코드(표준이 아닌 경우)를 가장 중요하게 생각합니다. 응답 형식 및 오류 코드는 XML 주석 및 데이터 주석에 표시됩니다.

`Create` 작업은 성공 시 HTTP 201 상태 코드를 반환합니다. 게시된 요청 본문이 null일 경우 HTTP 400 상태 코드가 반환됩니다. Swagger UI에 적절한 문서가 없으면 소비자는 이러한 예상 결과에 대한 정보를 알 수 없습니다. 다음 예제에서 강조 표시된 줄을 추가하여 해당 문제를 해결합니다.

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

이제 Swagger UI에서는 예상 HTTP 응답 코드를 분명히 설명합니다.

![POST 응답 클래스 설명 ‘새로 만들어진 Todo 항목 반환’ 및 ‘400 - 응답 메시지에서 항목의 상태 코드 및 이유가 null인 경우’를 보여 주는 Swagger UI](sample_images/data-annotations-response-types.png)

ASP.NET Core 2.2 이상에서는 `[ProducesResponseType]`을 사용하여 명시적으로 개별 작업을 데코레이팅하는 대신 규칙을 사용할 수 있습니다. 자세한 내용은 [웹 API 규칙 사용](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions)을 참조하세요.

UI 사용자 지정에 대한 자세한 내용은 다음을 참조하세요. [UI 사용자 지정](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)
