---
title: HttpRepl을 사용하여 웹 API 테스트
author: scottaddie
description: HttpRepl .NET Core 전역 도구를 사용하여 ASP.NET Core 웹 API를 찾아보고 테스트하는 방법에 대해 알아보세요.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2020
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
uid: web-api/http-repl
ms.openlocfilehash: 1027887738740d50c30e24e800c0402b1ce4ad02
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854667"
---
# <a name="test-web-apis-with-the-httprepl"></a>HttpRepl을 사용하여 웹 API 테스트

작성자: [Scott Addie](https://twitter.com/Scott_Addie)

HTTP REPL(Read-Eval-Print Loop):

* .NET Core가 지원되는 모든 곳에서 경량 플랫폼 간 명령줄 도구가 지원됩니다.
* ASP.NET Core 웹 API(및 non-ASP.NET Core 웹 API)를 테스트하고 결과를 확인하는 HTTP 요청을 만드는 데 사용됩니다.
* 로컬 호스트, Azure App Service를 포함하여 모든 환경에 호스팅된 웹 API를 테스트할 수 있습니다.

지원되는 [HTTP 동사](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)는 다음과 같습니다.

* [DELETE](#test-http-delete-requests)
* [GET](#test-http-get-requests)
* [HEAD](#test-http-head-requests)
* [OPTIONS](#test-http-options-requests)
* [PATCH](#test-http-patch-requests)
* [POST](#test-http-post-requests)
* [PUT](#test-http-put-requests)

본 내용을 따라가려면 [샘플 ASP.NET Core 웹 API를 보거나 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples)([다운로드 방법](xref:index#how-to-download-a-sample))하세요.

## <a name="prerequisites"></a>사전 요구 사항

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>설치

HttpRepl을 설치하려면 다음 명령을 실행합니다.

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

[.NET Core 글로벌 도구](/dotnet/core/tools/global-tools#install-a-global-tool)가 [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet 패키지에서 설치됩니다.

## <a name="usage"></a>사용량

도구를 성공적으로 설치한 후 다음 명령을 실행하여 HttpRepl을 시작합니다.

```console
httprepl
```

사용 가능한 HttpRepl 명령을 보려면 다음 명령 중 하나를 실행합니다.

```console
httprepl -h
```

```console
httprepl --help
```

다음 출력이 표시됩니다.

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

HttpRepl은 명령 완성을 제안합니다. <kbd>Tab</kbd> 키를 누르면 입력한 문자 또는 API 엔드포인트를 완성하는 명령 목록이 반복됩니다. 다음 섹션에서는 사용 가능한 CLI 명령에 대해 간략하게 설명합니다.

## <a name="connect-to-the-web-api"></a>웹 API에 연결

다음 명령을 실행하여 웹 API에 연결합니다.

```console
httprepl <ROOT URI>
```

`<ROOT URI>`는 웹 API의 기본 URI입니다. 예를 들면 다음과 같습니다.

```console
httprepl https://localhost:5001
```

또는 HttpRepl이 실행되는 동안 언제든지 다음 명령을 실행합니다.

```console
connect <ROOT URI>
```

예를 들면 다음과 같습니다.

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a>웹 API에 대한 OpenAPI 설명을 수동으로 가리키기

위의 connect 명령은 OpenAPI 설명을 자동으로 찾으려고 시도합니다. 이 작업을 할 수 없는 경우에는 다음과 같이 `--openapi` 옵션으로 웹 API에 대한 OpenAPI 설명의 URI를 지정할 수 있습니다.

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

예를 들면 다음과 같습니다.

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a>OpenAPI 설명 검색, 구문 분석 및 유효성 검사에 대한 자세한 내용은 자세한 정보 출력을 활성화합니다.

`connect` 명령으로 `--verbose` 옵션을 지정하면 도구에서 OpenAPI 설명을 검색하고, 구문 분석하고, 유효성을 검사할 때 자세한 정보가 생성됩니다.

```console
connect <ROOT URI> --verbose
```

예를 들면 다음과 같습니다.

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a>웹 API 탐색

### <a name="view-available-endpoints"></a>사용 가능한 엔드포인트 보기

웹 API 주소의 현재 경로에 있는 다른 엔드포인트(컨트롤러)를 나열하려면 `ls` 또는 `dir` 명령을 실행합니다.

```console
https://localhost:5001/> ls
```

다음 출력 형식이 표시됩니다.

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

위의 출력은 사용 가능한 두 컨트롤러 `Fruits` 및 `People`을 나타냅니다. 두 컨트롤러 모두 매개 변수 없는 HTTP GET 및 POST 작업을 지원합니다.

특정 컨트롤러로 이동하면 자세한 정보가 표시됩니다. 예를 들어 다음 명령의 출력은 `Fruits` 컨트롤러에서 HTTP GET, PUT 및 DELETE 작업도 지원함을 보여 줍니다. 각 작업의 경로에는 `id` 매개 변수가 필요합니다.

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

아니면 `ui` 명령을 실행하여 브라우저에서 웹 API의 Swagger UI 페이지를 엽니다. 예를 들면 다음과 같습니다.

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a>엔드포인트로 이동

웹 API에서 다른 엔드포인트로 이동하려면 `cd` 명령을 실행합니다.

```console
https://localhost:5001/> cd people
```

`cd` 명령 다음의 경로는 대/소문자를 구분하지 않습니다. 다음 출력 형식이 표시됩니다.

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a>HttpRepl 사용자 지정

HttpRepl의 기본 [색](#set-color-preferences)을 사용자 지정할 수 있습니다. 또한 [기본 텍스트 편집기](#set-the-default-text-editor)를 정의할 수 있습니다. HttpRepl 기본 설정은 현재 세션에서 유지되고 이후 세션에 적용됩니다. 기본 설정을 수정하는 경우 다음 파일에 저장됩니다.

# <a name="linux"></a>[Linux](#tab/linux)

*%HOME%/.httpreplprefs*

# <a name="macos"></a>[macOS](#tab/macos)

*%HOME%/.httpreplprefs*

# <a name="windows"></a>[Windows](#tab/windows)

*%USERPROFILE%\\.httpreplprefs*

---

*Httpreplprefs* 파일은 시작할 때 로드되며 런타임 시 변경 내용에 대한 모니터링이 되지 않습니다. 파일에 직접 수정한 내용은 도구를 다시 시작한 후에만 적용됩니다.

### <a name="view-the-settings"></a>설정 보기

사용 가능한 설정을 보려면 `pref get` 명령을 실행합니다. 예를 들면 다음과 같습니다.

```console
https://localhost:5001/> pref get
```

위의 명령은 사용 가능한 키-값 쌍을 표시합니다.

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a>색 기본 설정 지정

응답 색 지정은 현재 JSON에만 지원됩니다. 기본 HttpRepl 도구 색 지정을 사용자 지정하려면 변경할 색에 해당하는 키를 찾습니다. 키를 찾는 방법에 대한 지침은 [설정 보기](#view-the-settings) 섹션을 참조하세요. 예를 들어 다음과 같이 `colors.json` 키 값을 `Green`에서 `White`로 변경합니다.

```console
https://localhost:5001/people> pref set colors.json White
```

[허용되는 색](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)만 사용할 수 있습니다. 이후의 HTTP 요청은 새 색 지정을 사용하여 출력을 표시합니다.

특정 색 키가 설정되지 않은 경우에는 보다 일반적인 키가 고려됩니다. 이 대체 동작을 보여 주려면 다음 예제를 참조하세요.

* `colors.json.name`에 값이 없으면 `colors.json.string`이 사용됩니다.
* `colors.json.string`에 값이 없으면 `colors.json.literal`이 사용됩니다.
* `colors.json.literal`에 값이 없으면 `colors.json`이 사용됩니다. 
* `colors.json`에 값이 없으면 명령 셸의 기본 텍스트 색(`AllowedColors.None`)이 사용됩니다.

### <a name="set-indentation-size"></a>들여쓰기 크기 설정

응답 들여쓰기 크기 사용자 지정은 현재 JSON에 대해서만 지원됩니다. 기본 크기는 두 개의 공백입니다. 예를 들면 다음과 같습니다.

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

기본 크기를 변경하려면 `formatting.json.indentSize` 키를 설정합니다. 예를 들어 항상 네 개의 공백을 사용하려면:

```console
pref set formatting.json.indentSize 4
```

후속 응답은 네 개의 공백 설정을 사용합니다.

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a>기본 텍스트 편집기 설정

기본적으로 HttpRepl에는 사용하도록 구성된 텍스트 편집기가 없습니다. HTTP 요청 본문이 필요한 웹 API 메서드를 테스트하려면 기본 텍스트 편집기를 설정해야 합니다. HttpRepl 도구는 요청 본문을 작성할 목적으로만 구성된 텍스트 편집기를 시작합니다. 다음 명령을 실행하여 기본 설정 텍스트 편집기를 기본값으로 설정합니다.

```console
pref set editor.command.default "<EXECUTABLE>"
```

앞의 명령에서는 `<EXECUTABLE>`이 텍스트 편집기 실행 파일의 전체 경로입니다. 예를 들어 다음 명령을 실행하여 Visual Studio Code를 기본 텍스트 편집기로 설정합니다.

# <a name="linux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

특정 CLI 인수를 사용하여 기본 텍스트 편집기를 시작하려면 `editor.command.default.arguments` 키를 설정합니다. 예를 들어 Visual Studio Code가 기본 텍스트 편집기이며 항상 HttpRepl이 확장을 사용하지 않도록 설정한 새 세션에서 Visual Studio Code를 열도록 하는 경우를 가정합니다. 다음 명령을 실행합니다.

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> 기본 편집기가 Visual Studio Code인 경우 일반적으로 `-w` 또는 `--wait` 인수를 전달하여 Visual Studio Code가 반환하기 전에 사용자가 파일을 닫을 때까지 대기하도록 합니다.

### <a name="set-the-openapi-description-search-paths"></a>OpenAPI 설명 검색 경로 설정

기본적으로 HttpRepl에는 `--openapi` 옵션 없이 `connect` 명령을 실행할 때 OpenAPI 설명을 찾는 데 사용하는 상대 경로 세트가 있습니다. 이러한 상대 경로는 `connect` 명령에서 지정된 루트 및 기본 경로와 결합됩니다. 기본 상대 경로는 다음과 같습니다.

- *swagger.json*
- *swagger/v1/swagger.json*
- */swagger.json*
- */swagger/v1/swagger.json*
- *openapi. json*
- */openapi.json*

사용자의 환경에서 서로 다른 검색 경로 집합을 사용하려면 `swagger.searchPaths` 기본 설정을 지정합니다. 값은 파이프로 구분된 상대 경로 목록이어야 합니다. 예를 들면 다음과 같습니다.

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

기본 목록을 완전히 바꾸는 대신 경로를 추가하거나 제거하여 목록을 수정할 수도 있습니다.

기본 목록에 하나 이상의 검색 경로를 추가하려면 `swagger.addToSearchPaths` 기본 설정으로 설정합니다. 값은 파이프로 구분된 상대 경로 목록이어야 합니다. 예를 들면 다음과 같습니다.

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

기본 목록에서 하나 이상의 검색 경로를 제거하려면 `swagger.addToSearchPaths` 기본 설정으로 설정합니다. 값은 파이프로 구분된 상대 경로 목록이어야 합니다. 예를 들면 다음과 같습니다.

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a>HTTP GET 요청 테스트

### <a name="synopsis"></a>개요

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>인수

`PARAMETER`

연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.

### <a name="options"></a>옵션

다음 옵션은 `get` 명령에 사용할 수 있습니다.

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>예

HTTP GET 요청을 실행하려면:

1. 이를 지원하는 엔드포인트에서 `get` 명령을 실행합니다.

    ```console
    https://localhost:5001/people> get
    ```

    앞의 명령으로 인해 다음 출력 형식이 표시됩니다.

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people>
    ```

1. `get` 명령에 매개 변수를 전달하여 특정 레코드를 검색합니다.

    ```console
    https://localhost:5001/people> get 2
    ```

    앞의 명령으로 인해 다음 출력 형식이 표시됩니다.

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people>
    ```

## <a name="test-http-post-requests"></a>HTTP POST 요청 테스트

### <a name="synopsis"></a>개요

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>인수

`PARAMETER`

연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.

### <a name="options"></a>옵션

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>예

HTTP POST 요청을 실행하려면:

1. 이를 지원하는 엔드포인트에서 `post` 명령을 실행합니다.

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    앞의 명령에서 `Content-Type` HTTP 요청 헤더는 JSON의 요청 본문 미디어 유형을 나타내도록 설정됩니다. 기본 텍스트 편집기는 HTTP 요청 본문을 나타내는 JSON 템플릿을 사용하여 *.tmp* 파일을 엽니다. 예를 들면 다음과 같습니다.

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > 기본 텍스트 편집기를 설정하려면 [기본 텍스트 편집기 설정](#set-the-default-text-editor) 섹션을 참조하세요.

1. 모델 유효성 검사 요구 사항을 충족하도록 JSON 템플릿을 수정합니다.

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. *.tmp* 파일을 저장하고 텍스트 편집기를 닫습니다. 명령 셸에 다음 출력이 나타납니다.

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people>
    ```

## <a name="test-http-put-requests"></a>HTTP PUT 요청 테스트

### <a name="synopsis"></a>개요

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>인수

`PARAMETER`

연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.

### <a name="options"></a>옵션

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>예

HTTP PUT 요청을 실행하려면:

1. *선택 사항*: `get` 명령을 실행하여 데이터를 수정하기 전에 확인합니다.

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. 이를 지원하는 엔드포인트에서 `put` 명령을 실행합니다.

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    앞의 명령에서 `Content-Type` HTTP 요청 헤더는 JSON의 요청 본문 미디어 유형을 나타내도록 설정됩니다. 기본 텍스트 편집기는 HTTP 요청 본문을 나타내는 JSON 템플릿을 사용하여 *.tmp* 파일을 엽니다. 예를 들면 다음과 같습니다.

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > 기본 텍스트 편집기를 설정하려면 [기본 텍스트 편집기 설정](#set-the-default-text-editor) 섹션을 참조하세요.

1. 모델 유효성 검사 요구 사항을 충족하도록 JSON 템플릿을 수정합니다.

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. *.tmp* 파일을 저장하고 텍스트 편집기를 닫습니다. 명령 셸에 다음 출력이 나타납니다.

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *선택 사항*: `get` 명령을 실행하여 수정 내용을 확인합니다. 예를 들어 텍스트 편집기에서 "Cherry"를 입력한 경우 `get`은 다음 출력을 반환합니다.

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-delete-requests"></a>HTTP DELETE 요청 테스트

### <a name="synopsis"></a>개요

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>인수

`PARAMETER`

연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.

### <a name="options"></a>옵션

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>예

HTTP DELETE 요청을 실행하려면:

1. *선택 사항*: `get` 명령을 실행하여 데이터를 수정하기 전에 확인합니다.

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. 이를 지원하는 엔드포인트에서 `delete` 명령을 실행합니다.

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    앞의 명령으로 인해 다음 출력 형식이 표시됩니다.

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *선택 사항*: `get` 명령을 실행하여 수정 내용을 확인합니다. 이 예제에서 `get`은 다음 출력을 반환합니다.

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-patch-requests"></a>HTTP PATCH 요청 테스트

### <a name="synopsis"></a>개요

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>인수

`PARAMETER`

연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.

### <a name="options"></a>옵션

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>HTTP HEAD 요청 테스트

### <a name="synopsis"></a>개요

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>인수

`PARAMETER`

연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.

### <a name="options"></a>옵션

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>HTTP OPTIONS 요청 테스트

### <a name="synopsis"></a>개요

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>인수

`PARAMETER`

연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.

### <a name="options"></a>옵션

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>HTTP 요청 헤더 설정

HTTP 요청 헤더를 설정하려면 다음 방법 중 하나를 사용합니다.

* HTTP 요청을 사용하여 인라인으로 설정합니다. 예를 들면 다음과 같습니다.

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    앞의 방법을 사용하는 경우 개별 HTTP 요청 헤더에는 자체 `-h` 옵션이 필요합니다.

* HTTP 요청을 보내기 전에 설정합니다. 예를 들면 다음과 같습니다.

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    요청을 보내기 전에 헤더를 설정하는 경우 헤더는 명령 셸 세션 기간에 설정된 상태로 유지됩니다. 헤더를 지우려면 빈 값을 제공합니다. 예를 들면 다음과 같습니다.
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a>보안 엔드포인트 테스트

HttpRepl은 다음과 같은 방법으로 보안 엔드포인트의 테스트를 지원합니다.

* 로그인한 사용자의 기본 자격 증명을 통해
* HTTP 요청 헤더를 사용합니다.

### <a name="default-credentials"></a>기본 자격 증명

IIS에서 호스팅되고 Windows 인증으로 보호되는 테스트 중인 웹 API를 고려해 보세요. 도구를 실행하는 사용자의 자격 증명이 테스트 중인 HTTP 엔드포인트로 이동하려고 합니다. 로그인한 사용자의 기본 자격 증명을 전달하려면 다음을 수행합니다.

1. `httpClient.useDefaultCredentials` 기본 설정을 `true`로 설정합니다.

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. 웹 API에 다른 요청을 보내기 전에 도구를 종료하고 다시 시작합니다.
 
### <a name="default-proxy-credentials"></a>기본 프록시 자격 증명

테스트 중인 웹 API에서 Windows 인증으로 보호되는 프록시 뒤에 있는 시나리오를 고려해 보세요. 도구를 실행하는 사용자의 자격 증명이 프록시로 이동하려고 합니다. 로그인한 사용자의 기본 자격 증명을 전달하려면 다음을 수행합니다.

1. `httpClient.proxy.useDefaultCredentials` 기본 설정을 `true`로 설정합니다.

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. 웹 API에 다른 요청을 보내기 전에 도구를 종료하고 다시 시작합니다.

### <a name="http-request-headers"></a>HTTP 요청 헤더

지원되는 인증 및 권한 부여 스키마의 예는 다음과 같습니다.

* basic authentication
* JWT 전달자 토큰
* 다이제스트 인증

예를 들어 다음 명령을 사용하여 엔드포인트에 전달자 토큰을 보낼 수 있습니다.

```console
set header Authorization "bearer <TOKEN VALUE>"
```

Azure에서 호스트되는 엔드포인트에 액세스하거나 [Azure REST API](/rest/api/azure/)를 사용하려면 전달자 토큰이 필요합니다. [Azure CLI](/cli/azure/)를 통해 Azure 구독에 대한 전달자 토큰을 가져오려면 다음 단계를 사용합니다. HttpRepl은 HTTP 요청 헤더에서 전달자 토큰을 설정합니다. Azure App Service Web Apps 목록이 검색됩니다.

1. Azure에 로그인:

    ```azurecli
    az login
    ```

1. 다음 명령을 사용하여 구독 ID를 가져옵니다.

    ```azurecli
    az account show --query id
    ```

1. 구독 ID를 복사하고 다음 명령을 실행합니다.

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. 다음 명령을 사용하여 전달자 토큰을 가져옵니다.

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. HttpRepl을 통해 Azure REST API에 연결합니다.

    ```console
    httprepl https://management.azure.com
    ```

1. `Authorization` HTTP 요청 헤더를 설정합니다.

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. 구독으로 이동합니다.

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. 구독의 Azure App Service Web Apps 목록을 가져옵니다.

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    다음 응답이 표시됩니다.

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a>HTTP 요청 표시 토글

기본적으로 보내는 HTTP 요청 표시는 표시되지 않습니다. 명령 셸 세션 기간에 해당하는 설정을 변경할 수 있습니다.

### <a name="enable-request-display"></a>요청 표시 사용

`echo on` 명령을 실행하여 보내는 HTTP 요청을 표시합니다. 예를 들면 다음과 같습니다.

```console
https://localhost:5001/people> echo on
Request echoing is on
```

현재 세션의 후속 HTTP 요청은 요청 헤더를 표시합니다. 예를 들면 다음과 같습니다.

```console
https://localhost:5001/people> post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people>
```

### <a name="disable-request-display"></a>요청 표시 사용 안 함

`echo off` 명령을 실행하여 보내는 HTTP 요청을 표시하지 않습니다. 예를 들면 다음과 같습니다.

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a>스크립트 실행

동일한 HttpRepl 명령 세트를 자주 실행하는 경우에는 텍스트 파일에 저장하는 것이 좋습니다. 파일의 명령은 명령줄에서 수동으로 실행되는 명령과 동일한 형식을 사용합니다. `run` 명령을 사용하여 일괄적으로 명령을 실행할 수 있습니다. 예를 들면 다음과 같습니다.

1. 줄 바꿈 기호로 분리된 명령 세트를 포함하는 텍스트 파일을 만듭니다. 다음 명령을 포함하는 *people-script.txt* 파일을 설명합니다.

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. 텍스트 파일의 경로를 전달하여 `run` 명령을 실행합니다. 예를 들면 다음과 같습니다.

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    다음과 같은 출력이 표시됩니다.

    ```console
    https://localhost:5001/> set base https://localhost:5001
    Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/> ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/> cd People
    /People    [get|post]
    
    https://localhost:5001/People> ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People> get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People>
    ```

## <a name="clear-the-output"></a>출력 지우기

HttpRepl 도구에 의해 명령 셸에 작성된 모든 출력을 제거하려면 `clear` 또는 `cls` 명령을 실행합니다. 설명을 위해 명령 셸에 다음 출력이 포함되어 있다고 가정합니다.

```console
httprepl https://localhost:5001
(Disconnected)> set base "https://localhost:5001"
Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/> ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

다음 명령을 실행하여 출력을 지웁니다.

```console
https://localhost:5001/> clear
```

앞의 명령을 실행한 후 명령 셸은 다음 출력만 포함합니다.

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a>추가 리소스

* [REST API 요청](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [HttpRepl GitHub 리포지토리](https://github.com/dotnet/HttpRepl)
* [HttpRepl을 시작하도록 Visual Studio 구성](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-windows-to-launch-httprepl-on-f5)
* [HttpRepl을 시작하도록 Visual Studio Code 구성](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-code-to-launch-httprepl-on-debug)
* [HttpRepl을 시작하도록 Mac용 Visual Studio 구성](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-mac-to-launch-httprepl-as-a-custom-tool)
