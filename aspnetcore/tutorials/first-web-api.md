---
title: '자습서: ASP.NET Core를 사용하여 웹 API 만들기'
author: rick-anderson
description: ASP.NET Core를 사용하여 웹 API를 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: fc41dd13e7d027d9630cd596162f9b5fd2ef9e2b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058495"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>자습서: ASP.NET Core를 사용하여 웹 API 만들기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Mike Wasson](https://github.com/mikewasson)

이 자습서에서는 ASP.NET Core를 사용하여 웹 API를 빌드하는 작업의 기본 사항을 설명합니다.

::: moniker range=">= aspnetcore-5.0"

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 웹 API 프로젝트를 만듭니다.
> * 모델 클래스와 데이터베이스 컨텍스트를 추가합니다.
> * CRUD 메서드로 컨트롤러를 스캐폴드합니다.
> * 라우팅, URL 경로 및 반환 값을 구성합니다.
> * Postman을 사용하여 웹 API를 호출합니다.

과정을 마치면 웹 API를 통해 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있습니다.

## <a name="overview"></a>개요

이 자습서에서는 다음 API를 만듭니다.

|API | 설명 | 요청 본문 | 응답 본문 |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | 할 일 항목 모두 가져오기 | 없음 | 할 일 항목의 배열|
|`GET /api/TodoItems/{id}` | ID로 항목 가져오기 | 없음 | 할 일 항목|
|`POST /api/TodoItems` | 새 항목 추가 | 할 일 항목 | 할 일 항목 |
|`PUT /api/TodoItems/{id}` | 기존 항목 업데이트 &nbsp; | 할 일 항목 | 없음 |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | 항목 삭제 &nbsp; &nbsp; | 없음 | 없음|

다음 다이어그램에서는 앱의 디자인을 보여줍니다.

![클라이언트는 왼쪽에 상자로 표시됩니다. 클라이언트는 요청을 제출하고 오른쪽에 그려진 상자인 애플리케이션에서 응답을 받습니다. 애플리케이션 상자 내에서 3개의 상자는 컨트롤러, 모델 및 데이터 액세스 계층을 나타냅니다. 요청은 애플리케이션의 컨트롤러로 들어오고 읽기/쓰기 작업은 컨트롤러와 데이터 액세스 계층 간에 발생합니다. 모델은 직렬화되며 응답에서 클라이언트에 반환됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>사전 요구 사항

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a>웹 프로젝트 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.
* **ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음** 을 클릭합니다.
* 프로젝트 이름을 *TodoApi* 로 지정하고 **만들기** 를 클릭합니다.
* **새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 5.0** 이 선택되었는지 확인합니다. **API** 템플릿을 선택하고 **만들기** 를 클릭합니다.

![VS 새 프로젝트 대화 상자](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.
* 디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.
* 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* 프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.

  이전 명령은

  * 새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.
  * 다음 섹션에서 필요한 NuGet 패키지를 추가합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* **파일** > **새 솔루션** 을 선택합니다.

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음** 을 선택합니다. 버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음** 을 선택합니다.

  ![macOS API 템플릿 선택](first-web-api-mac/_static/api_template.png)

* **새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 3.x **대상 프레임워크** 를 선택합니다. **새로 만들기** 를 선택합니다.

* **프로젝트 이름** 으로 *TodoApi* 를 입력한 다음, **만들기** 를 선택합니다.

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

프로젝트 폴더에서 명령 터미널을 열고 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a>프로젝트 테스트

프로젝트 템플릿은 [Swagger](xref:tutorials/web-api-help-pages-using-swagger)를 지원하는 `WeatherForecast` API를 만듭니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ctrl+F5를 눌러 디버거 없이 실행합니다.

[!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio에서 다음을 시작합니다.

* IIS Express 웹 서버.
* 기본 브라우저를 시작하고 `https://localhost:<port>/https://localhost:5001/swagger/index.html`로 이동합니다. 여기서 `<port>`는 임의로 선택한 포트 번호입니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

Ctrl+F5 키를 눌러 앱을 실행합니다. 브라우저에서 [https://localhost:5001/swagger](https://localhost:5001/swagger) URL로 이동합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

**실행** > **디버깅 시작** 을 선택하여 앱을 시작합니다. Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다. HTTP 404(찾을 수 없음) 오류가 반환됩니다. `/swagger`를 URL에 추가합니다(URL을 `https://localhost:<port>/swagger`로 변경).

---

Swagger 페이지 `/swagger/index.html`이 표시됩니다. **GET** > **사용해 보기** > **실행** 을 선택합니다. 페이지에 다음이 표시됩니다.

* WeatherForecast API를 테스트할 [Curl](https://curl.haxx.se/) 명령
* WeatherForecast API를 테스트할 URL
* 응답 코드, 본문 및 헤더
* 미디어 유형과 예제 값 및 스키마가 포함된 드롭다운 목록 상자

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
Swagger는 웹 API에 유용한 설명서 및 도움말 페이지를 생성하는 데 사용됩니다. 이 자습서에서는 웹 API 만들기에 대해 집중적으로 다룹니다. Swagger에 대한 자세한 내용은 <xref:tutorials/web-api-help-pages-using-swagger>를 참조하세요.

브라우저에서 **요청 URL** `https://localhost:<port>/WeatherForecast`를 복사하여 붙여넣습니다.

다음과 비슷한 JSON이 반환됩니다.

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a>launchUrl 업데이트

*Properties\launchSettings.json* 에서 `launchUrl`을 `"swagger"`에서 `"api/TodoItems"`로 업데이트합니다.

```json
"launchUrl": "api/TodoItems",
```

Swagger가 제거되었기 때문에 위의 태그는 다음 섹션에 추가된 컨트롤러의 GET 메서드로 시작되는 URL을 변경합니다.

## <a name="add-a-model-class"></a>모델 클래스 추가

*모델* 은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다. 이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가** > **새 폴더** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.

* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다. 클래스 이름을 *TodoItem* 으로 지정하고 **추가** 를 선택합니다.

* 템플릿 코드를 다음으로 바꿉니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Models* 폴더를 추가합니다.

* 다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가** > **새 폴더** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.

  ![새 폴더](first-web-api-mac/_static/folder.png)

* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스** 를 선택합니다.

* 클래스 이름을 *TodoItem* 으로 지정한 다음, **새로 만들기** 를 클릭합니다.

* 템플릿 코드를 다음으로 바꿉니다.

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.

모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.

## <a name="add-a-database-context"></a>데이터베이스 컨텍스트 추가

*데이터베이스 컨텍스트* 는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다. <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>NuGet 패키지 추가

* **도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리** 를 선택합니다.
* **찾아보기** 탭을 선택하고 **Microsoft.
**EntityFrameworkCore.SqlServer** 를 검색 상자에 입력합니다.
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* 5\.0 RC 버전을 사용할 수 있도록 **시험판 포함** 확인란을 선택합니다. 
* 왼쪽 창에서 **Microsoft.EntityFrameworkCore.SqlServer** 를 선택합니다.
* 오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치** 를 선택합니다.
* 앞의 지침을 사용하여 **Microsoft.EntityFrameworkCore.InMemory** NuGet 패키지를 추가합니다.

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
![NuGet 패키지 관리자](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a>TodoContext 데이터베이스 컨텍스트 추가

* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다. 클래스 이름을 *TodoContext* 로 지정하고 **추가** 를 클릭합니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* `TodoContext` 클래스를 *Models* 폴더에 추가합니다.

---

* 다음 코드를 입력합니다.

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>데이터베이스 컨텍스트 등록

ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다. 컨테이너는 컨트롤러에 서비스를 제공합니다.

다음 코드로 *Startup.cs* 를 업데이트합니다.

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

위의 코드는

* Swagger 호출을 제거합니다.
* 사용되지 않는 `using` 선언을 제거합니다.
* DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.
* 데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.

## <a name="scaffold-a-controller"></a>컨트롤러 스캐폴드

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.
* **추가** > **스캐폴드 항목 새로 만들기** 를 선택합니다.
* **Entity Framework를 사용하며 동작이 포함된 API 컨트롤러** 를 선택하고 **추가** 를 선택합니다.
* **Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:

  * **모델 클래스** 에서 **TodoItem(TodoApi.Models)** 을 선택합니다.
  * **데이터 컨텍스트 클래스** 에서 **TodoContext(TodoApi.Models)** 를 선택합니다.
  * **추가** 를 선택합니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

다음 명령을 실행합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

이전 명령은

* 스캐폴딩에 필요한 NuGet 패키지를 추가합니다.
* 스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다
* `TodoItemsController`를 스캐폴드합니다.

---

생성된 코드는:

* [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다. 이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다. 특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.
* DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다. 컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.

ASP.NET Core 템플릿과 관련해서 다음 사항을 확인합니다.

* 뷰가 있는 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함됩니다.
* API 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함되지 않습니다.

`[action]` 토큰이 경로 템플릿에 없는 경우 경로에서 [작업](xref:mvc/controllers/routing#action) 이름이 제외됩니다. 즉, 일치하는 경로에서 작업과 연결된 메서드 이름이 사용되지 않습니다.

## <a name="update-the-posttodoitem-create-method"></a>PostTodoItem 만들기 메서드 업데이트

`PostTodoItem`의 return 문이 [nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 바꿉니다.

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다. 이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.

자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.

* 성공하면 [HTTP 201 상태 코드](https://developer.mozilla.org/docs/Web/HTTP/Status/201)를 반환합니다. HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.
* 응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다. `Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다. 자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.
* `Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다. C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.

### <a name="install-postman"></a>Postman을 설치합니다.

이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.

* [Postman](https://www.getpostman.com/downloads/)을 설치합니다.
* 웹앱을 시작합니다.
* Postman을 시작합니다.
* **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.
  * **파일** > **설정** ( **일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.
    > [!WARNING]
    > 컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Postman을 사용하여 PostTodoItem 테스트

* 새 요청을 만듭니다.
* HTTP 메서드를 `POST`로 설정합니다.
* URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다. 예: `https://localhost:5001/api/TodoItems`.
* **본문** 탭을 선택합니다.
* **원시** 라디오 단추를 선택합니다.
* 유형을 **JSON(application/json)** 으로 설정합니다.
* 요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* **보내기** 를 선택합니다.

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>위치 헤더 URI 테스트

위치 헤더 URI는 브라우저에서 테스트할 수 있습니다. 위치 헤더 URI를 복사하여 브라우저에 붙여넣습니다.

Postman에서 테스트하려면 다음을 수행합니다.

* **응답** 창에서 **헤더** 탭을 선택합니다.
* **위치** 헤더 값을 복사합니다.

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* HTTP 메서드를 `GET`로 설정합니다.
* URI를 `https://localhost:<port>/api/TodoItems/1`로 설정합니다. 예: `https://localhost:5001/api/TodoItems/1`.
* **보내기** 를 선택합니다.

## <a name="examine-the-get-methods"></a>GET 메서드 검사

두 개의 GET 엔드포인트가 구현됩니다.

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다. 예를 들어:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Postman을 사용하여 Get 테스트

* 새 요청을 만듭니다.
* HTTP 메서드를 **GET** 으로 설정합니다.
* 요청 URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다. 예: `https://localhost:5001/api/TodoItems`.
* Postman에서 **두 개의 창 보기** 를 설정합니다.
* **보내기** 를 선택합니다.

이 앱은 메모리 내 데이터베이스를 사용합니다. 앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다. 데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.

## <a name="routing-and-url-paths"></a>라우팅 및 URL 경로

[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다. 각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.

* 컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* `[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다. 이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems** Controller이므로 컨트롤러 이름은 “TodoItems”입니다. ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.
* `[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다. 이 샘플은 템플릿을 사용하지 않습니다. 자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.

다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다. `GetTodoItem`이 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>반환 값

`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다. ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다. 이 반환 형식의 응답 코드는 [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200)이며 처리되지 않은 예외가 없다고 가정합니다. 처리되지 않은 예외는 5xx 오류로 변환됩니다.

`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다. 예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.

* 요청된 ID와 일치하는 항목이 없는 경우 메서드에서 [404 상태](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.
* 그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다. `item`을 반환하면 HTTP 200 응답이 발생합니다.

## <a name="the-puttodoitem-method"></a>PutTodoItem 메서드

다음과 같이 `PutTodoItem` 메서드를 검사합니다.

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다. 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다. HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다. 부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.

`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.

### <a name="test-the-puttodoitem-method"></a>PutTodoItem 메서드 테스트

이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다. PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다. GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.

Id = 1인 할 일 항목을 업데이트하고 해당 이름을 `"feed fish"`로 설정합니다.

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

다음 이미지는 Postman 업데이트를 보여줍니다.

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>DeleteTodoItem 메서드

다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>DeleteTodoItem 메서드 테스트

Postman을 사용하여 할 일 항목을 삭제합니다.

* 메서드를 `DELETE`로 설정합니다.
* 예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.
* **보내기** 를 선택합니다.

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a>과도한 게시 방지

현재 샘플 앱은 전체 `TodoItem` 개체를 공개합니다. 일반적으로 프로덕션 앱은 모델의 하위 집합을 사용하여 입력 및 반환되는 데이터를 제한합니다. 이 동작에는 여러 가지 이유가 있으며, 보안이 주요 이유 중 하나입니다. 일반적으로 모델의 하위 집합을 DTO(데이터 전송 개체), 입력 모델 또는 뷰 모델이라고 합니다. 이 문서에서는 **DTO** 를 사용합니다.

DTO는 다음과 같은 용도로 사용할 수 있습니다.

* 과도한 게시를 방지합니다.
* 클라이언트에 표시되지 않아야 하는 속성을 숨깁니다.
* 페이로드 크기를 줄이기 위해 일부 속성을 생략합니다.
* 중첩된 개체를 포함하는 개체 그래프를 평면화합니다. 클라이언트에는 평면화된 개체 그래프가 더 편리할 수 있습니다.

DTO 방법을 설명하려면 비밀 필드를 포함하도록 `TodoItem` 클래스를 업데이트합니다.

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

이 앱에서는 숨겨진 필드를 숨겨야 하지만, 관리 앱은 숨겨진 필드를 공개할 수 있습니다.

비밀 필드를 게시하고 가져올 수 있는지 확인합니다.

DTO 모델을 만듭니다.

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

`TodoItemDTO`를 사용하도록 `TodoItemsController`를 업데이트합니다.

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

비밀 필드를 게시하거나 가져올 수 없음을 확인합니다.

## <a name="call-the-web-api-with-javascript"></a>JavaScript를 사용하여 웹 API 호출

[자습서: JavaScript로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-javascript)를 참조하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 웹 API 프로젝트를 만듭니다.
> * 모델 클래스와 데이터베이스 컨텍스트를 추가합니다.
> * CRUD 메서드로 컨트롤러를 스캐폴드합니다.
> * 라우팅, URL 경로 및 반환 값을 구성합니다.
> * Postman을 사용하여 웹 API를 호출합니다.

과정을 마치면 웹 API를 통해 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있습니다.

## <a name="overview"></a>개요

이 자습서에서는 다음 API를 만듭니다.

|API | 설명 | 요청 본문 | 응답 본문 |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | 할 일 항목 모두 가져오기 | 없음 | 할 일 항목의 배열|
|`GET /api/TodoItems/{id}` | ID로 항목 가져오기 | 없음 | 할 일 항목|
|`POST /api/TodoItems` | 새 항목 추가 | 할 일 항목 | 할 일 항목 |
|`PUT /api/TodoItems/{id}` | 기존 항목 업데이트 &nbsp; | 할 일 항목 | 없음 |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | 항목 삭제 &nbsp; &nbsp; | 없음 | 없음|

다음 다이어그램에서는 앱의 디자인을 보여줍니다.

![클라이언트는 왼쪽에 상자로 표시됩니다. 클라이언트는 요청을 제출하고 오른쪽에 그려진 상자인 애플리케이션에서 응답을 받습니다. 애플리케이션 상자 내에서 3개의 상자는 컨트롤러, 모델 및 데이터 액세스 계층을 나타냅니다. 요청은 애플리케이션의 컨트롤러로 들어오고 읽기/쓰기 작업은 컨트롤러와 데이터 액세스 계층 간에 발생합니다. 모델은 직렬화되며 응답에서 클라이언트에 반환됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>사전 요구 사항

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>웹 프로젝트 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.
* **ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음** 을 클릭합니다.
* 프로젝트 이름을 *TodoApi* 로 지정하고 **만들기** 를 클릭합니다.
* **새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.1** 이 선택되었는지 확인합니다. **API** 템플릿을 선택하고 **만들기** 를 클릭합니다.

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.
* 디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.
* 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* 프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.

  이전 명령은

  * 새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.
  * 다음 섹션에서 필요한 NuGet 패키지를 추가합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* **파일** > **새 솔루션** 을 선택합니다.

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음** 을 선택합니다. 버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음** 을 선택합니다.

  ![macOS API 템플릿 선택](first-web-api-mac/_static/api_template.png)

* **새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 3.x **대상 프레임워크** 를 선택합니다. **새로 만들기** 를 선택합니다.

* **프로젝트 이름** 으로 *TodoApi* 를 입력한 다음, **만들기** 를 선택합니다.

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

프로젝트 폴더에서 명령 터미널을 열고 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>API 테스트

프로젝트 템플릿은 `WeatherForecast` API를 만듭니다. 브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ctrl+F5 키를 눌러 앱을 실행합니다. Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/WeatherForecast`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.

IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예** 를 선택합니다. 다음으로, **보안 경고** 대화 상자가 나타나면 **예** 를 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ctrl+F5 키를 눌러 앱을 실행합니다. 브라우저에서 `https://localhost:5001/WeatherForecast` URL로 이동합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

**실행** > **디버깅 시작** 을 선택하여 앱을 시작합니다. Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다. HTTP 404(찾을 수 없음) 오류가 반환됩니다. `/WeatherForecast`를 URL에 추가합니다(URL을 `https://localhost:<port>/WeatherForecast`로 변경).

---

다음과 비슷한 JSON이 반환됩니다.

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>모델 클래스 추가

*모델* 은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다. 이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가** > **새 폴더** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.

* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다. 클래스 이름을 *TodoItem* 으로 지정하고 **추가** 를 선택합니다.

* 템플릿 코드를 다음 코드로 바꿉니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Models* 폴더를 추가합니다.

* 다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가** > **새 폴더** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.

  ![새 폴더](first-web-api-mac/_static/folder.png)

* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스** 를 선택합니다.

* 클래스 이름을 *TodoItem* 으로 지정한 다음, **새로 만들기** 를 클릭합니다.

* 템플릿 코드를 다음 코드로 바꿉니다.

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.

모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.

## <a name="add-a-database-context"></a>데이터베이스 컨텍스트 추가

*데이터베이스 컨텍스트* 는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다. `Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>NuGet 패키지 추가

* **도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리** 를 선택합니다.
* **찾아보기** 탭을 선택한 다음 검색 상자에 **Microsoft.EntityFrameworkCore.SqlServer** 를 입력합니다.
* 왼쪽 창에서 **Microsoft.EntityFrameworkCore.SqlServer** 를 선택합니다.
* 오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치** 를 선택합니다.
* 앞의 지침을 사용하여 **Microsoft.EntityFrameworkCore.InMemory** NuGet 패키지를 추가합니다.

![NuGet 패키지 관리자](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>TodoContext 데이터베이스 컨텍스트 추가

* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다. 클래스 이름을 *TodoContext* 로 지정하고 **추가** 를 클릭합니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* `TodoContext` 클래스를 *Models* 폴더에 추가합니다.

---

* 다음 코드를 입력합니다.

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>데이터베이스 컨텍스트 등록

ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다. 컨테이너는 컨트롤러에 서비스를 제공합니다.

*Startup.cs* 를 다음 강조 표시된 코드로 업데이트합니다.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

위의 코드는

* 사용되지 않는 `using` 선언을 제거합니다.
* DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.
* 데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.

## <a name="scaffold-a-controller"></a>컨트롤러 스캐폴드

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.
* **추가** > **스캐폴드 항목 새로 만들기** 를 선택합니다.
* **Entity Framework를 사용하며 동작이 포함된 API 컨트롤러** 를 선택하고 **추가** 를 선택합니다.
* **Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:

  * **모델 클래스** 에서 **TodoItem(TodoApi.Models)** 을 선택합니다.
  * **데이터 컨텍스트 클래스** 에서 **TodoContext(TodoApi.Models)** 를 선택합니다.
  * **추가** 를 선택합니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

다음 명령을 실행합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

이전 명령은

* 스캐폴딩에 필요한 NuGet 패키지를 추가합니다.
* 스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다
* `TodoItemsController`를 스캐폴드합니다.

---

생성된 코드는:

* [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다. 이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다. 특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.
* DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다. 컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.

ASP.NET Core 템플릿과 관련해서 다음 사항을 확인합니다.

* 뷰가 있는 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함됩니다.
* API 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함되지 않습니다.

`[action]` 토큰이 경로 템플릿에 없는 경우 경로에서 [작업](xref:mvc/controllers/routing#action) 이름이 제외됩니다. 즉, 일치하는 경로에서 작업과 연결된 메서드 이름이 사용되지 않습니다.

## <a name="examine-the-posttodoitem-create-method"></a>PostTodoItem 만들기 메서드 검사

`PostTodoItem`의 return 문이 [nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 바꿉니다.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다. 이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.

자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.

* 성공 시 HTTP 201 상태 코드를 반환합니다. HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.
* 응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다. `Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다. 자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.
* `Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다. C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.

### <a name="install-postman"></a>Postman을 설치합니다.

이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.

* [Postman](https://www.getpostman.com/downloads/)을 설치합니다.
* 웹앱을 시작합니다.
* Postman을 시작합니다.
* **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.
  * **파일** > **설정** ( **일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.
    > [!WARNING]
    > 컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Postman을 사용하여 PostTodoItem 테스트

* 새 요청을 만듭니다.
* HTTP 메서드를 `POST`로 설정합니다.
* URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다. 예: `https://localhost:5001/api/TodoItems`.
* **본문** 탭을 선택합니다.
* **원시** 라디오 단추를 선택합니다.
* 유형을 **JSON(application/json)** 으로 설정합니다.
* 요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* **보내기** 를 선택합니다.

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a>Postman을 사용하여 위치 헤더 URI 테스트

* **응답** 창에서 **헤더** 탭을 선택합니다.
* **위치** 헤더 값을 복사합니다.

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* HTTP 메서드를 `GET`로 설정합니다.
* URI를 `https://localhost:<port>/api/TodoItems/1`로 설정합니다. 예: `https://localhost:5001/api/TodoItems/1`.
* **보내기** 를 선택합니다.

## <a name="examine-the-get-methods"></a>GET 메서드 검사

다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다. 예를 들어:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Postman을 사용하여 Get 테스트

* 새 요청을 만듭니다.
* HTTP 메서드를 **GET** 으로 설정합니다.
* 요청 URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다. 예: `https://localhost:5001/api/TodoItems`.
* Postman에서 **두 개의 창 보기** 를 설정합니다.
* **보내기** 를 선택합니다.

이 앱은 메모리 내 데이터베이스를 사용합니다. 앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다. 데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.

## <a name="routing-and-url-paths"></a>라우팅 및 URL 경로

[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다. 각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.

* 컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* `[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다. 이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems** Controller이므로 컨트롤러 이름은 “TodoItems”입니다. ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.
* `[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다. 이 샘플은 템플릿을 사용하지 않습니다. 자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.

다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다. `GetTodoItem`이 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>반환 값 

`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다. ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다. 이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다. 처리되지 않은 예외는 5xx 오류로 변환됩니다.

`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다. 예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.

* 요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.
* 그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다. `item`을 반환하면 HTTP 200 응답이 발생합니다.

## <a name="the-puttodoitem-method"></a>PutTodoItem 메서드

다음과 같이 `PutTodoItem` 메서드를 검사합니다.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다. 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다. HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다. 부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.

`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.

### <a name="test-the-puttodoitem-method"></a>PutTodoItem 메서드 테스트

이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다. PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다. GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.

ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

다음 이미지는 Postman 업데이트를 보여줍니다.

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>DeleteTodoItem 메서드

다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>DeleteTodoItem 메서드 테스트

Postman을 사용하여 할 일 항목을 삭제합니다.

* 메서드를 `DELETE`로 설정합니다.
* 예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.
* **보내기** 를 선택합니다.

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a>과도한 게시 방지

현재 샘플 앱은 전체 `TodoItem` 개체를 공개합니다. 일반적으로 프로덕션 앱은 모델의 하위 집합을 사용하여 입력 및 반환되는 데이터를 제한합니다. 이 동작에는 여러 가지 이유가 있으며, 보안이 주요 이유 중 하나입니다. 일반적으로 모델의 하위 집합을 DTO(데이터 전송 개체), 입력 모델 또는 뷰 모델이라고 합니다. 이 문서에서는 **DTO** 를 사용합니다.

DTO는 다음과 같은 용도로 사용할 수 있습니다.

* 과도한 게시를 방지합니다.
* 클라이언트에 표시되지 않아야 하는 속성을 숨깁니다.
* 페이로드 크기를 줄이기 위해 일부 속성을 생략합니다.
* 중첩된 개체를 포함하는 개체 그래프를 평면화합니다. 클라이언트에는 평면화된 개체 그래프가 더 편리할 수 있습니다.

DTO 방법을 설명하려면 비밀 필드를 포함하도록 `TodoItem` 클래스를 업데이트합니다.

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

이 앱에서는 숨겨진 필드를 숨겨야 하지만, 관리 앱은 숨겨진 필드를 공개할 수 있습니다.

비밀 필드를 게시하고 가져올 수 있는지 확인합니다.

DTO 모델을 만듭니다.

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

`TodoItemDTO`를 사용하도록 `TodoItemsController`를 업데이트합니다.

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

비밀 필드를 게시하거나 가져올 수 없음을 확인합니다.

## <a name="call-the-web-api-with-javascript"></a>JavaScript를 사용하여 웹 API 호출

[자습서: JavaScript로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-javascript)를 참조하세요.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 웹 API 프로젝트를 만듭니다.
> * 모델 클래스와 데이터베이스 컨텍스트를 추가합니다.
> * 컨트롤러 추가
> * CRUD 메서드 추가
> * 라우팅 및 URL 경로 구성
> * 반환 값 지정
> * Postman을 사용하여 웹 API를 호출합니다.
> * JavaScript를 사용하여 웹 API를 호출합니다.

작업을 완료하면 웹 API는 관계형 데이터베이스에 저장된 "할 일" 항목을 관리할 수 있게 됩니다.

## <a name="overview-21"></a>개요 2.1

이 자습서에서는 다음 API를 만듭니다.

|API | 설명 | 요청 본문 | 응답 본문 |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | 할 일 항목 모두 가져오기 | 없음 | 할 일 항목의 배열|
|GET /api/TodoItems/{id} | ID로 항목 가져오기 | 없음 | 할 일 항목|
|POST /api/TodoItems | 새 항목 추가 | 할 일 항목 | 할 일 항목 |
|PUT /api/TodoItems/{id} | 기존 항목 업데이트 &nbsp; | 할 일 항목 | 없음 |
|DELETE /api/TodoItems/{id} &nbsp; &nbsp; | 항목 삭제 &nbsp; &nbsp; | 없음 | 없음|

다음 다이어그램에서는 앱의 디자인을 보여줍니다.

![클라이언트는 왼쪽에 상자로 표시됩니다. 클라이언트는 요청을 제출하고 오른쪽에 그려진 상자인 애플리케이션에서 응답을 받습니다. 애플리케이션 상자 내에서 3개의 상자는 컨트롤러, 모델 및 데이터 액세스 계층을 나타냅니다. 요청은 애플리케이션의 컨트롤러로 들어오고 읽기/쓰기 작업은 컨트롤러와 데이터 액세스 계층 간에 발생합니다. 모델은 직렬화되며 응답에서 클라이언트에 반환됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a>필수 구성 요소 2.1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a>웹 프로젝트 만들기 2.1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.
* **ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음** 을 클릭합니다.
* 프로젝트 이름을 *TodoApi* 로 지정하고 **만들기** 를 클릭합니다.
* **새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 2.2** 가 선택되었는지 확인합니다. **API** 템플릿을 선택하고 **만들기** 를 클릭합니다. **Docker 지원 사용** 을 선택하지 **마세요**.

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.
* 디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.
* 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  이러한 명령은 새 웹 API 프로젝트를 만들고 새 프로젝트 폴더에서 Visual Studio Code의 새 인스턴스를 엽니다.

* 프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* **파일** > **새 솔루션** 을 선택합니다.

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음** 을 선택합니다. 버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음** 을 선택합니다.
  
* **새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 2.x **대상 프레임워크** 를 선택합니다. **새로 만들기** 를 선택합니다.

* **프로젝트 이름** 으로 *TodoApi* 를 입력한 다음, **만들기** 를 선택합니다.

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a>API 테스트 2.1

프로젝트 템플릿은 `values` API를 만듭니다. 브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ctrl+F5 키를 눌러 앱을 실행합니다. Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/api/values`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.

IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예** 를 선택합니다. 다음으로, **보안 경고** 대화 상자가 나타나면 **예** 를 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ctrl+F5 키를 눌러 앱을 실행합니다. 브라우저에서 `https://localhost:5001/api/values` URL로 이동합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

**실행** > **디버깅 시작** 을 선택하여 앱을 시작합니다. Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다. HTTP 404(찾을 수 없음) 오류가 반환됩니다. `/api/values`를 URL에 추가합니다(URL을 `https://localhost:<port>/api/values`로 변경).

---

다음 JSON이 반환됩니다.

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a>모델 클래스 추가 2.1

*모델* 은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다. 이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가** > **새 폴더** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.

* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다. 클래스 이름을 *TodoItem* 으로 지정하고 **추가** 를 선택합니다.

* 템플릿 코드를 다음 코드로 바꿉니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Models* 폴더를 추가합니다.

* 다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

* 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가** > **새 폴더** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.

  ![새 폴더](first-web-api-mac/_static/folder.png)

* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스** 를 선택합니다.

* 클래스 이름을 *TodoItem* 으로 지정한 다음, **새로 만들기** 를 클릭합니다.

* 템플릿 코드를 다음 코드로 바꿉니다.

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.

모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.

## <a name="add-a-database-context-21"></a>데이터베이스 컨텍스트 추가 2.1

*데이터베이스 컨텍스트* 는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다. `Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다. 클래스 이름을 *TodoContext* 로 지정하고 **추가** 를 클릭합니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* `TodoContext` 클래스를 *Models* 폴더에 추가합니다.

---

* 템플릿 코드를 다음 코드로 바꿉니다.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a>데이터베이스 컨텍스트 등록 2.1

ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다. 컨테이너는 컨트롤러에 서비스를 제공합니다.

*Startup.cs* 를 다음 강조 표시된 코드로 업데이트합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

위의 코드는

* 사용되지 않는 `using` 선언을 제거합니다.
* DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.
* 데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.

## <a name="add-a-controller-21"></a>컨트롤러 추가 2.1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.
* **추가** > **새 항목** 을 선택합니다.
* **새 항목 추가** 대화 상자에서 **API 컨트롤러 클래스** 템플릿을 선택합니다.
* 클래스 이름을 *TodoController* 로 지정하고 **추가** 를 선택합니다.

  ![검색 상자의 컨트롤러 및 웹 API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* *Controllers* 폴더에 `TodoController`라는 클래스를 만듭니다.

---

* 템플릿 코드를 다음 코드로 바꿉니다.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

위의 코드는

* 메서드 없이 API 컨트롤러 클래스를 정의합니다.
* [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다. 이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다. 특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.
* DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다. 컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.
* 데이터베이스가 비어 있는 경우 데이터베이스에 `Item1`이라는 항목을 추가합니다. 이 코드는 생성자에 위치하므로 새 HTTP 요청이 발생할 때마다 실행됩니다. 모든 항목을 삭제하면 생성자는 다음에 API가 호출될 경우 `Item1`을 다시 만듭니다. 따라서 실제로 작동되는 경우 삭제가 작동하지 않는 것처럼 보일 수 있습니다.

## <a name="add-get-methods-21"></a>GET 메서드 추가 2.1

할 일 항목을 가져오는 API를 제공하려면 다음 메서드를 `TodoController` 클래스에 추가합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.

* `GET /api/todo`
* `GET /api/todo/{id}`

앱이 계속 실행되고 있으면 중지합니다. 그런 다음, 다시 실행하여 최신 변경 내용을 포함합니다.

브라우저에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다. 예를 들어:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

`GetTodoItems`를 호출하여 다음 HTTP 응답이 생성됩니다.

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a>라우팅 및 URL 경로 2.1

[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다. 각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.

* 컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* `[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다. 이 샘플의 경우 컨트롤러 클래스 이름은 **Todo** Controller이므로 컨트롤러 이름은 "todo"입니다. ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.
* `[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다. 이 샘플은 템플릿을 사용하지 않습니다. 자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.

다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다. `GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a>반환 값 2.1

`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다. ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다. 이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다. 처리되지 않은 예외는 5xx 오류로 변환됩니다.

`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다. 예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.

* 요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.
* 그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다. `item`을 반환하면 HTTP 200 응답이 발생합니다.

## <a name="test-the-gettodoitems-method-21"></a>GetTodoItems 메서드 테스트 2.1

이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.

* [Postman](https://www.getpostman.com/downloads/)을 설치합니다.
* 웹앱을 시작합니다.
* Postman을 시작합니다.
* **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **파일** > **설정** ( **일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않도록 설정합니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* **Postman** > **기본 설정** ( **일반** 탭)에서 **SSL 인증서 확인** 을 사용하지 않게 설정합니다. 또는 렌치를 선택하고 **설정** 을 선택한 다음 SSL 인증서 확인을 사용하지 않게 설정합니다.

---
  
> [!WARNING]
> 컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.

* 새 요청을 만듭니다.
  * HTTP 메서드를 **GET** 으로 설정합니다.
  * 요청 URI를 `https://localhost:<port>/api/todo`로 설정합니다. 예: `https://localhost:5001/api/todo`.
* Postman에서 **두 개의 창 보기** 를 설정합니다.
* **보내기** 를 선택합니다.

![Get 요청이 있는 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a>Create 메서드 추가 2.1

*Controllers/TodoController.cs* 내부에 다음 `PostTodoItem` 메서드를 추가합니다. 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다. 이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.

`CreatedAtAction` 메서드는 다음 작업을 수행합니다.

* 성공 시 HTTP 201 상태 코드를 반환합니다. HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.
* `Location` 헤더를 응답에 추가합니다. `Location` 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다. 자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.
* `Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다. C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a>PostTodoItem 메서드 테스트 2.1

* 프로젝트를 빌드합니다.
* Postman에서 HTTP 메서드를 `POST`로 설정합니다.
* URI를 `https://localhost:<port>/api/TodoItem`으로 설정합니다. 예: `https://localhost:5001/api/TodoItem`.
* **본문** 탭을 선택합니다.
* **원시** 라디오 단추를 선택합니다.
* 유형을 **JSON(application/json)** 으로 설정합니다.
* 요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* **보내기** 를 선택합니다.

  ![생성 요청이 있는 Postman](first-web-api/_static/create.png)

  405 메서드가 허용되지 않음 오류가 발생할 경우 `PostTodoItem` 메서드를 추가한 후에 프로젝트를 컴파일하지 않아서 발생한 결과일 수 있습니다.

### <a name="test-the-location-header-uri-21"></a>위치 헤더 URI 테스트 2.1

* **응답** 창에서 **헤더** 탭을 선택합니다.
* **위치** 헤더 값을 복사합니다.

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/pmc2.png)

* 메서드를 GET으로 설정합니다.
* URI를 `https://localhost:<port>/api/TodoItems/2`으로 설정합니다. 예: `https://localhost:5001/api/TodoItems/2`.
* **보내기** 를 선택합니다.

## <a name="add-a-puttodoitem-method-21"></a>PutTodoItem 메서드 추가 2.1

다음 `PutTodoItem` 메서드를 추가합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다. 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다. HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다. 부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.

`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.

### <a name="test-the-puttodoitem-method-21"></a>PutTodoItem 메서드 테스트 2.1

이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다. PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다. GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.

ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

다음 이미지는 Postman 업데이트를 보여줍니다.

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a>DeleteTodoItem 메서드 추가 2.1

다음 `DeleteTodoItem` 메서드를 추가합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.

### <a name="test-the-deletetodoitem-method-21"></a>DeleteTodoItem 메서드 테스트 2.1

Postman을 사용하여 할 일 항목을 삭제합니다.

* 메서드를 `DELETE`로 설정합니다.
* 삭제할 개체의 URI를 설정합니다(예: `https://localhost:5001/api/todo/1`).
* **보내기** 를 선택합니다.

샘플 앱을 사용하면 모든 항목을 삭제할 수 있습니다. 하지만 마지막 항목이 삭제되면 다음에 API를 호출하는 경우 모델 클래스 생성자에서 새로운 항목이 생성됩니다.

## <a name="call-the-web-api-with-javascript-21"></a>JavaScript를 사용하여 웹 API 호출 2.1

이 섹션에는 JavaScript를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다. jQuery가 요청을 시작합니다. JavaScript는 웹 API 응답의 세부 정보를 토대로 페이지를 업데이트합니다.

다음 강조 표시된 코드로 *Startup.cs* 를 업데이트하여 앱이 [정적 파일을 제공](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)하고 [기본 파일 매핑을 사용](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)하도록 구성합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.

*index.html* 이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다. 다음 표시로 콘텐츠를 바꿉니다.

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

*site.js* 라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다. 다음 코드로 콘텐츠를 바꿉니다.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.

* *Properties\launchSettings.json* 을 엽니다.
* `launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.

이 샘플은 웹 API의 CRUD 메서드를 모두 호출합니다. API 호출에 대한 설명은 다음과 같습니다.

### <a name="get-a-list-of-to-do-items-21"></a>할 일 항목의 목록 가져오기 2.1

jQuery는 할 일 항목의 배열을 나타내는 JSON을 반환하는 웹 API에 HTTP GET 요청을 보냅니다. 요청이 성공하면 `success` 콜백 함수가 호출됩니다. 콜백에서 DOM은 할 일 정보로 업데이트됩니다.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a>할 일 항목 추가 2.1

jQuery는 요청 본문에 있는 할 일 항목을 사용하여 HTTP POST 요청을 보냅니다. `accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다. [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다. API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a>할 일 항목 업데이트 2.1

할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다. `url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a>할 일 항목 삭제 2.1

할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a>웹 API에 인증 지원 추가 2.1

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a>추가 리소스 2.1

[이 자습서에서 샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples) [다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.

자세한 내용은 다음 자료를 참조하세요.

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [이 자습서의 YouTube 버전](https://www.youtube.com/watch?v=TTkhEyGBfAk)
