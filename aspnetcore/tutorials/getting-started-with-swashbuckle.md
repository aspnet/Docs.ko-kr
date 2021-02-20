---
title: Swashbuckle 및 ASP.NET Core 시작
author: zuckerthoben
description: ASP.NET Core Web API 프로젝트에 Swashbuckle을 추가하여 Swagger UI를 통합하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
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
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: df9c78770f3494b1ae9e0d3604926d77cb1e67bb
ms.sourcegitcommit: 20a41c8e40a2e69e99291e2fe18caa04c02e7109
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99578323"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Swashbuckle 및 ASP.NET Core 시작

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

Swashbuckle에 대한 세 가지 주 구성 요소는 다음과 같습니다.

* [Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): `SwaggerDocument` 개체를 JSON 엔드포인트로 노출하기 위한 Swagger 개체 모델 및 미들웨어.

* [Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): 경로, 컨트롤러 및 모델에서 직접 `SwaggerDocument` 개체를 빌드하는 Swagger 생성기. 일반적으로 Swagger 엔드포인트 미들웨어와 결합되어 자동으로 Swagger JSON을 노출합니다.

* [Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): Swagger UI 도구의 포함된 버전. Swagger JSON을 해석하여 웹 API 기능을 설명하기 위한 다양한 사용자 지정 가능한 환경을 빌드합니다. 여기에는 공용 메서드에 대한 기본 제공 테스트 도구가 포함됩니다.

## <a name="package-installation"></a>패키지 설치

다음 방법으로 Swashbuckle을 추가할 수 있습니다.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **패키지 관리자 콘솔** 창에서:
  * **보기** > **다른 창** > **패키지 관리자 콘솔** 로 이동
  * *TodoApi.csproj* 파일이 위치한 디렉터리로 이동
  * 다음 명령을 실행합니다.

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.6.3
    ```

* **NuGet 패키지 관리** 대화 상자에서:
  * **솔루션 탐색기** > **NuGet 패키지 관리** 에서 프로젝트를 마우스 오른쪽 단추로 클릭
  * **패키지 소스** 를 “nuget.org”로 설정
  * "시험판 포함" 옵션을 사용하도록 설정했는지 확인
  * 검색 상자에 “Swashbuckle.AspNetCore” 입력
  * **찾아보기** 탭에서 최신 “Swashbuckle.AspNetCore” 패키지를 선택하고 **설치** 클릭

### <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* **Solution Pad** 에서 *Packages* 폴더를 마우스 오른쪽 단추로 클릭 > **패키지 추가...** 선택
* **패키지 추가** 창의 **소스** 드롭다운을 “nuget.org”로 설정
* "시험판 패키지 표시" 옵션을 사용하도록 설정했는지 확인
* 검색 상자에 “Swashbuckle.AspNetCore” 입력
* 결과 창에서 최신 "Swashbuckle.AspNetCore" 패키지를 선택하고 **패키지 추가** 클릭

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**통합 터미널** 에서 다음 명령을 실행합니다.

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.6.3
```

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

다음 명령을 실행합니다.

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.6.3
```

---

## <a name="add-and-configure-swagger-middleware"></a>Swagger 미들웨어 추가 및 구성

`Startup.ConfigureServices` 메서드의 서비스 컬렉션에 Swagger 생성기를 추가합니다.

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

`Startup.Configure` 메서드에서 생성된 JSON 문서 및 Swagger UI를 지원하기 위해 미들웨어를 사용하도록 설정합니다.

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> Swashbuckle은 MVC의 <xref:Microsoft.AspNetCore.Mvc.ApiExplorer>를 사용하여 경로 및 엔드포인트를 검색합니다. 프로젝트에서 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A>를 호출하면 경로 및 엔드포인트가 자동으로 검색됩니다. <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A>를 호출하는 경우 <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> 메서드를 명시적으로 호출해야 합니다. 자세한 내용은 [Swashbuckle, ApiExplorer 및 라우팅](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing)을 참조하세요.

위의 `UseSwaggerUI` 메서드 호출은 [정적 파일 미들웨어](xref:fundamentals/static-files)를 활성화합니다. .NET Framework 또는 NET Core 1.x를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet 패키지를 프로젝트에 추가합니다.

앱을 시작하고 `http://localhost:<port>/swagger/v1/swagger.json`으로 이동합니다. 엔드포인트를 설명하는 생성된 문서는 [OpenAPI 사양(openapi.json)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson)에 표시된 것과 같이 나타납니다.

Swagger UI는 `http://localhost:<port>/swagger`에 있습니다. Swagger UI를 통해 API를 탐색하고 다른 프로그램에 통합합니다.

> [!TIP]
> 앱의 루트(`http://localhost:<port>/`)에서 Swagger UI를 제공하려면 `RoutePrefix` 속성을 빈 문자열로 설정합니다.
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

IIS 또는 역방향 프록시에서 디렉터리를 사용하는 경우 `./` 접두사를 사용하여 Swagger 엔드포인트를 상대 경로로 설정합니다. 예: `./swagger/v1/swagger.json`. `/swagger/v1/swagger.json`을 사용하면 앱이 URL의 실제 루트(사용되는 경우 경로 접두사)에서 JSON 파일을 찾도록 지시합니다. 예를 들어 `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json` 대신 `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json`을 사용합니다.

> [!NOTE]
> 기본적으로 Swashbuckle은 공식적으로 OpenAPI 사양이라고 하는 사양의 버전 3.0에서 Swagger JSON을 생성하고 공개합니다. 이전 버전과의 호환성을 지원하기 위해 2.0 형식으로 JSON을 공개하도록 옵트인할 수 있습니다. 현재 OpenAPI 버전 2.0을 지원하는 Microsoft Power Apps 및 Microsoft Flow와 같은 통합의 경우 이 2.0 형식이 중요합니다. 2\.0 형식을 옵트인하려면 `Startup.Configure`에서 `SerializeAsV2` 속성을 설정합니다.
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

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

`Startup` 클래스에서는 `OpenApiInfo` 클래스를 사용하기 위해 다음 네임스페이스를 가져옵니다.

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

`OpenApiInfo` 클래스를 사용하여 UI에 표시되는 정보를 수정합니다.

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

Swagger UI는 버전의 정보를 표시합니다.

![버전 정보가 포함된 Swagger UI: 설명, 작성자 및 추가 링크 확인](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>XML 주석

XML 주석은 다음 방법으로 사용하도록 설정할 수 있습니다.

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **<project_name>.csproj 편집** 을 선택합니다.
* 강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.
* **빌드** 탭의 **출력** 섹션에서 **XML 설명서 파일** 상자를 선택합니다.

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* *Solution Pad* 에서 **control** 키를 누르고 프로젝트 이름을 클릭합니다. **도구** > **파일 편집** 으로 이동합니다.
* 강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* **프로젝트 옵션** 대화 상자 > **빌드** > **컴파일러** 를 엽니다.
* **일반 옵션** 섹션에서 **XML 문서 생성** 상자 선택

::: moniker-end

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

XML 주석을 사용하면 문서화되지 않은 public 형식과 멤버에 대한 디버그 정보를 제공합니다. 문서화되지 않은 형식 및 멤버는 경고 메시지로 표시됩니다. 예를 들어 다음 메시지는 경고 코드 1591 위반을 나타냅니다.

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

프로젝트 전반에서 경고를 표시하지 않으려면 프로젝트 파일에서 무시할 경고 코드의 세미콜론으로 구분된 목록을 정의합니다. `$(NoWarn);`에 경고 코드를 추가하면 [C# 기본값](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)도 적용됩니다.

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

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

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

이전 코드에서 [리플렉션](/dotnet/csharp/programming-guide/concepts/reflection)은 웹 API 프로젝트의 이름과 일치하는 XML 파일 이름을 빌드하는 데 사용됩니다. [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) 속성은 XML 파일에 대한 경로를 생성하는 데 사용됩니다. 일부 Swagger 기능(예: 각 특성에서 입력 매개 변수 또는 HTTP 메서드와 응답 코드의 schemata)은 XML 문서 파일을 사용하지 않고 작동합니다. 대부분 기능, 메서드 요약 및 매개 변수/응답 코드 설명의 경우 XML 파일을 사용해야 합니다.

작업에 3중 슬래시 주석을 추가하면 섹션 헤더에 설명이 추가되어 Swagger UI가 향상됩니다. `Delete` 작업 위에 [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) 요소를 추가합니다.

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

Swagger UI는 이전 코드 `<summary>` 요소의 내부 텍스트를 표시합니다.

![DELETE 메서드에 대한 XML 주석 ‘특정 TodoItem을 삭제합니다.’를 보여 주는 Swagger UI](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

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

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

이러한 추가 주석이 포함된 향상된 UI 기능을 확인할 수 있습니다.

![추가 주석이 표시된 Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>데이터 주석

[System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 네임스페이스에 있는 특성으로 모델을 표시하여 Swagger UI 구성 요소를 구동합니다.

`[Required]` 특성을 `TodoItem` 클래스의 `Name` 속성에 추가합니다.

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

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

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

컨트롤러 GET 작업의 경우 **응답 콘텐츠 형식** 드롭다운에서 이 콘텐츠 형식이 기본값으로 선택됩니다.

![기본 응답 콘텐츠 형식이 포함된 Swagger UI](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

웹 API에서 데이터 주석 사용이 증가하면 UI 및 API 도움말 페이지에는 더 자세한 설명과 유용한 정보가 제공됩니다.

### <a name="describe-response-types"></a>응답 형식 설명

웹 API를 사용하는 개발자는 반환된 내용&mdash; 중에서도 특히 응답 형식 및 오류 코드(표준이 아닌 경우)를 가장 중요하게 생각합니다. 응답 형식 및 오류 코드는 XML 주석 및 데이터 주석에 표시됩니다.

`Create` 작업은 성공 시 HTTP 201 상태 코드를 반환합니다. 게시된 요청 본문이 null일 경우 HTTP 400 상태 코드가 반환됩니다. Swagger UI에 적절한 문서가 없으면 소비자는 이러한 예상 결과에 대한 정보를 알 수 없습니다. 다음 예제에서 강조 표시된 줄을 추가하여 해당 문제를 해결합니다.

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

이제 Swagger UI에서는 예상 HTTP 응답 코드를 분명히 설명합니다.

![POST 응답 클래스 설명 ‘새로 만들어진 Todo 항목 반환’ 및 ‘400 - 응답 메시지에서 항목의 상태 코드 및 이유가 null인 경우’를 보여 주는 Swagger UI](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

ASP.NET Core 2.2 이상에서는 `[ProducesResponseType]`을 사용하여 명시적으로 개별 작업을 데코레이팅하는 대신 규칙을 사용할 수 있습니다. 자세한 내용은 <xref:web-api/advanced/conventions>를 참조하세요.

`[ProducesResponseType]` 데코레이션을 지원하기 위해 [Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) 패키지는 응답, 스키마 및 매개 변수 메타데이터를 사용하도록 지원하고 보강하는 확장을 제공합니다.

::: moniker-end

### <a name="customize-the-ui"></a>UI 사용자 지정

기본 UI는 기능적이며 표시 가능합니다. 그러나 API 설명서 페이지는 브랜드 또는 테마를 나타내야 합니다. Swashbuckle 구성 요소를 브랜딩하려면 리소스를 추가하여 정적 파일을 지원하고 폴더 구조를 빌드하여 해당 파일을 호스트해야 합니다.

.NET Framework 또는 NET Core 1.x를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet 패키지를 프로젝트에 추가합니다.

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

이전 NuGet 패키지는 .NET Core 2.x를 대상으로 하고 [metapackage](xref:fundamentals/metapackage)를 사용하는 경우 이미 설치되어 있습니다.

정적 파일 미들웨어 사용:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

CSS 스타일시트를 추가로 삽입하려면 프로젝트의 *wwwroot* 폴더에 추가하고 미들웨어 옵션에서 상대 경로를 지정합니다.

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```

## <a name="additional-resources"></a>추가 자료

* [Microsoft Learn: Swagger 문서화 기능을 사용하여 개발자의 API 환경 개선](/learn/modules/improve-api-developer-experience-with-swagger/)
