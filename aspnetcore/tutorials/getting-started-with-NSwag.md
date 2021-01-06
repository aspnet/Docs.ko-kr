---
title: NSwag 및 ASP.NET Core 시작
author: zuckerthoben
description: NSwag를 사용하여 ASP.NET Core Web API의 설명서 및 도움말 페이지를 생성하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
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
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 78d58d4d544c33862cf502ce63e83560e8009c65
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060575"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>NSwag 및 ASP.NET Core 시작

작성자: [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com) 및 [Dave Brock](https://twitter.com/daveabrock)

::: moniker range=">= aspnetcore-2.1"

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([다운로드 방법](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([다운로드 방법](xref:index#how-to-download-a-sample))

::: moniker-end

NSwag는 다음 기능을 제공합니다.

* Swagger UI 및 Swagger 생성기를 사용하는 기능
* 유연한 코드 생성 기능

NSwag를 사용하면 기존 API가 필요하지 않으므로 Swagger를 통합하고 클라이언트 구현을 생성하는 타사 API를 사용할 수 있습니다. NSwag를 사용하면 개발 주기를 단축하고 API 변경에 쉽게 대응할 수 있습니다.

## <a name="register-the-nswag-middleware"></a>NSwag 미들웨어 등록

다음 작업을 수행하려면 NSwag 미들웨어를 등록합니다.

* 구현된 Web API에 대한 Swagger 사양을 생성합니다.
* Web API를 찾아보고 테스트하는 Swagger UI를 제공합니다.

[NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core 미들웨어를 사용하려면 [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet 패키지를 설치합니다. 이 패키지에는 Swagger 사양, Swagger UI(v2 및 v3) 및 [ReDoc UI](https://github.com/Rebilly/ReDoc)를 생성하고 제공하는 미들웨어가 포함되어 있습니다.

다음 방법 중 하나를 사용하여 NSwag NuGet 패키지를 설치합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **패키지 관리자 콘솔** 창에서:
  * **보기** > **다른 창** > **패키지 관리자 콘솔** 로 이동
  * *TodoApi.csproj* 파일이 위치한 디렉터리로 이동
  * 다음 명령을 실행합니다.

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* **NuGet 패키지 관리** 대화 상자에서:
  * **솔루션 탐색기** > **NuGet 패키지 관리** 에서 프로젝트를 마우스 오른쪽 단추로 클릭
  * **패키지 소스** 를 “nuget.org”로 설정
  * 검색 상자에 “NSwag.AspNetCore” 입력
  * **찾아보기** 탭에서 "NSwag.AspNetCore" 패키지를 선택하고 **설치** 클릭

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* **Solution Pad** 에서 *Packages* 폴더를 마우스 오른쪽 단추로 클릭 > **패키지 추가...** 선택
* **패키지 추가** 창의 **소스** 드롭다운을 “nuget.org”로 설정
* 검색 상자에 “NSwag.AspNetCore” 입력
* 결과 창에서 "NSwag.AspNetCore" 패키지를 선택하고 **패키지 추가** 클릭

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

다음 명령을 실행합니다.

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Swagger 미들웨어 추가 및 구성

다음 단계를 수행하여 ASP.NET Core 앱에서 Swagger를 추가하고 구성합니다.

* `Startup.ConfigureServices` 메서드에서 필수 Swagger 서비스를 등록합니다.

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* `Startup.Configure` 메서드에서 생성된 Swagger 사양 및 Swagger UI를 지원하기 위해 미들웨어를 사용하도록 설정합니다.

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* 앱을 시작합니다. 다음으로 이동합니다.
  * `http://localhost:<port>/swagger` - Swagger UI를 봅니다.
  * `http://localhost:<port>/swagger/v1/swagger.json` - Swagger 사양을 봅니다.

## <a name="code-generation"></a>코드 생성

다음 옵션 중 하나를 선택하여 NSwag의 코드 생성 기능을 활용할 수 있습니다.

* [NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): C# 또는 TypeScript로 API 클라이언트 코드를 생성하기 위한 Windows 데스크톱 앱.
* 프로젝트 내에서 코드 생성을 위한 [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) 또는 [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet 패키지.
* [명령줄](https://github.com/RicoSuter/NSwag/wiki/CommandLine)의 NSwag.
* [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet 패키지.
* [Unchase OpenAPI(Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): C# 또는 TypeScript로 API 클라이언트 코드를 생성하기 위한 Visual Studio Connected Service. 또한 NSwag를 사용하여 OpenAPI 서비스용 C# 컨트롤러를 생성합니다.

### <a name="generate-code-with-nswagstudio"></a>NSwagStudio로 코드 생성

* [NSwagStudio GitHub 리포지토리](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio)의 지침에 따라 NSwagStudio를 설치합니다. NSwag 릴리스 페이지에서 설치 및 관리자 권한 없이 시작할 수 있는 xcopy 버전을 다운로드할 수 있습니다.
* NSwagStudio를 시작하고 **Swagger 사양 URL** 텍스트 상자에 *swagger.json* 파일 URL을 입력합니다. 예를 들어 *http://localhost:44354/swagger/v1/swagger.json* 와 같습니다.
* **로컬 복사본 만들기** 단추를 클릭하여 Swagger 사양의 JSON 표시를 생성합니다.

  ![Swagger 사양의 로컬 복사본 만들기](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* **출력** 영역에서 **CSharp 클라이언트** 확인란을 클릭합니다. 프로젝트에 따라 **TypeScript 클라이언트** 또는 **CSharp 웹 API 컨트롤러** 를 선택할 수도 있습니다. **CSharp 웹 API 컨트롤러** 를 선택하면 서비스 사양에서 역방향 생성으로 사용되는 서비스를 다시 빌드합니다.
* **출력 생성** 을 클릭하여 *TodoApi.NSwag* 프로젝트의 완전한 C# 클라이언트 구현을 생성합니다. 생성된 클라이언트 코드를 보려면 **CSharp 클라이언트** 탭을 클릭합니다.

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> C# 클라이언트 코드는 **설정** 탭의 선택에 따라 생성됩니다. 기본 네임 스페이스 이름 바꾸기 및 동기 메서드 생성 등의 작업을 수행하도록 설정을 수정합니다.

* API를 사용할 클라이언트 프로젝트의 파일에 생성된 C# 코드를 복사합니다.
* Web API를 사용하기 시작합니다.

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a>API 문서 사용자 지정

Swagger는 Web API를 보다 쉽게 사용하도록 개체 모델을 문서화하는 옵션을 제공합니다.

### <a name="api-info-and-description"></a>API 정보 및 설명

`Startup.ConfigureServices` 메서드에서 `AddSwaggerDocument` 메서드에 전달되는 구성 작업은 작성자, 라이선스 및 설명과 같은 정보를 추가합니다.

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

Swagger UI는 버전의 정보를 표시합니다.

![버전 정보를 포함한 Swagger UI](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>XML 주석

XML 주석을 사용하려면 다음 단계를 수행합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **<project_name>.csproj 편집** 을 선택합니다.
* 강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성** 선택
* **빌드** 탭의 **출력** 섹션에서 **XML 문서 파일** 상자 선택

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* *Solution Pad* 에서 **control** 키를 누르고 프로젝트 이름을 클릭합니다. **도구** > **파일 편집** 으로 이동합니다.
* 강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* **프로젝트 옵션** 대화 상자 > **빌드** > **컴파일러** 를 엽니다.
* **일반 옵션** 섹션에서 **XML 문서 생성** 상자 선택

::: moniker-end

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>데이터 주석

::: moniker range="<= aspnetcore-2.0"

NSwag는 [리플렉션](/dotnet/csharp/programming-guide/concepts/reflection)을 사용하고 웹 API 작업의 권장 반환 형식은 [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult)이므로 작업이 수행 중인 작업과 반환 결과를 유추할 수 없습니다.

다음 예제를 참조하세요.

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

이전 작업은 `IActionResult`를 반환하지만 작업 내에서는 [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) 또는 [BadRequest](xref:System.Web.Http.ApiController.BadRequest*)를 반환합니다. 데이터 주석을 사용하여 이 작업이 반환하는 것으로 알려진 HTTP 상태 코드를 클라이언트에 알립니다. 다음 특성을 사용하여 작업을 표시합니다.

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

NSwag는 [리플렉션](/dotnet/csharp/programming-guide/concepts/reflection)을 사용하고 웹 API 작업의 권장 반환 형식은 [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601)이므로 `T`로 정의된 반환 형식만 유추할 수 있습니다. 다른 가능한 반환 형식은 자동으로 유추할 수 없습니다.

다음 예제를 참조하세요.

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

이전 작업이 `ActionResult<T>`를 반환합니다. 작업 내에서 [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*)를 반환합니다. 컨트롤러에는 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성이 있으므로 [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) 응답도 가능합니다. 자세한 정보는 [자동 HTTP 400 응답](xref:web-api/index#automatic-http-400-responses)을 참조하세요. 데이터 주석을 사용하여 이 작업이 반환하는 것으로 알려진 HTTP 상태 코드를 클라이언트에 알립니다. 다음 특성을 사용하여 작업을 표시합니다.

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

ASP.NET Core 2.2 이상에서는 `[ProducesResponseType]`을 사용하여 명시적으로 개별 작업을 데코레이트하는 대신 규칙을 사용할 수 있습니다. 자세한 내용은 <xref:web-api/advanced/conventions>를 참조하세요.

::: moniker-end

이제 Swagger 생성기는 이 작업을 정확하게 설명할 수 있으며, 생성된 클라이언트가 엔드포인트를 호출할 때 수신한 내용을 알 수 있습니다. 모든 작업을 이러한 특성으로 표시하는 것이 좋습니다.

API 작업에서 반환해야 하는 HTTP 응답에 대한 지침은 [RFC 7231 사양](https://tools.ietf.org/html/rfc7231#section-4.3)을 참조하세요.
