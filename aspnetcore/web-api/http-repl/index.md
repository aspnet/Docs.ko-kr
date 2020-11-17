---
title: HttpRepl을 사용하여 웹 API 테스트
author: scottaddie
description: HttpRepl .NET Core 전역 도구를 사용하여 ASP.NET Core 웹 API를 찾아보고 테스트하는 방법에 대해 알아보세요.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
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
ms.openlocfilehash: c661141c1bec93c0f7b6404964dc0bca2319b529
ms.sourcegitcommit: 827e8be18cebbcc09b467c089e17fa6f5e430cb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94634615"
---
# <a name="test-web-apis-with-the-httprepl"></a><span data-ttu-id="27ff1-103">HttpRepl을 사용하여 웹 API 테스트</span><span class="sxs-lookup"><span data-stu-id="27ff1-103">Test web APIs with the HttpRepl</span></span>

<span data-ttu-id="27ff1-104">작성자: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="27ff1-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="27ff1-105">HTTP REPL(Read-Eval-Print Loop):</span><span class="sxs-lookup"><span data-stu-id="27ff1-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="27ff1-106">.NET Core가 지원되는 모든 곳에서 경량 플랫폼 간 명령줄 도구가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="27ff1-107">ASP.NET Core 웹 API(및 non-ASP.NET Core 웹 API)를 테스트하고 결과를 확인하는 HTTP 요청을 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="27ff1-108">로컬 호스트, Azure App Service를 포함하여 모든 환경에 호스팅된 웹 API를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="27ff1-109">지원되는 [HTTP 동사](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="27ff1-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="27ff1-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="27ff1-111">GET</span><span class="sxs-lookup"><span data-stu-id="27ff1-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="27ff1-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="27ff1-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="27ff1-113">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="27ff1-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="27ff1-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="27ff1-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="27ff1-115">POST</span><span class="sxs-lookup"><span data-stu-id="27ff1-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="27ff1-116">PUT</span><span class="sxs-lookup"><span data-stu-id="27ff1-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="27ff1-117">본 내용을 따라가려면 [샘플 ASP.NET Core 웹 API를 보거나 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples)([다운로드 방법](xref:index#how-to-download-a-sample))하세요.</span><span class="sxs-lookup"><span data-stu-id="27ff1-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="27ff1-118">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="27ff1-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="27ff1-119">설치</span><span class="sxs-lookup"><span data-stu-id="27ff1-119">Installation</span></span>

<span data-ttu-id="27ff1-120">HttpRepl을 설치하려면 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-120">To install the HttpRepl, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="27ff1-121">[.NET Core 글로벌 도구](/dotnet/core/tools/global-tools#install-a-global-tool)가 [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet 패키지에서 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="27ff1-122">사용</span><span class="sxs-lookup"><span data-stu-id="27ff1-122">Usage</span></span>

<span data-ttu-id="27ff1-123">도구를 성공적으로 설치한 후 다음 명령을 실행하여 HttpRepl을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-123">After successful installation of the tool, run the following command to start the HttpRepl:</span></span>

```console
httprepl
```

<span data-ttu-id="27ff1-124">사용 가능한 HttpRepl 명령을 보려면 다음 명령 중 하나를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-124">To view the available HttpRepl commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="27ff1-125">다음 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-125">The following output is displayed:</span></span>

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

<span data-ttu-id="27ff1-126">HttpRepl은 명령 완성을 제안합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-126">The HttpRepl offers command completion.</span></span> <span data-ttu-id="27ff1-127"><kbd>Tab</kbd> 키를 누르면 입력한 문자 또는 API 엔드포인트를 완성하는 명령 목록이 반복됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="27ff1-128">다음 섹션에서는 사용 가능한 CLI 명령에 대해 간략하게 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="27ff1-129">웹 API에 연결</span><span class="sxs-lookup"><span data-stu-id="27ff1-129">Connect to the web API</span></span>

<span data-ttu-id="27ff1-130">다음 명령을 실행하여 웹 API에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="27ff1-131">`<ROOT URI>`는 웹 API의 기본 URI입니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="27ff1-132">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="27ff1-133">또는 HttpRepl이 실행되는 동안 언제든지 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-133">Alternatively, run the following command at any time while the HttpRepl is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="27ff1-134">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-134">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a><span data-ttu-id="27ff1-135">웹 API에 대한 OpenAPI 설명을 수동으로 가리키기</span><span class="sxs-lookup"><span data-stu-id="27ff1-135">Manually point to the OpenAPI description for the web API</span></span>

<span data-ttu-id="27ff1-136">위의 connect 명령은 OpenAPI 설명을 자동으로 찾으려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-136">The connect command above will attempt to find the OpenAPI description automatically.</span></span> <span data-ttu-id="27ff1-137">이 작업을 할 수 없는 경우에는 다음과 같이 `--openapi` 옵션으로 웹 API에 대한 OpenAPI 설명의 URI를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-137">If for some reason it's unable to do so, you can specify the URI of the OpenAPI description for the web API by using the `--openapi` option:</span></span>

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

<span data-ttu-id="27ff1-138">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-138">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a><span data-ttu-id="27ff1-139">OpenAPI 설명 검색, 구문 분석 및 유효성 검사에 대한 자세한 내용은 자세한 정보 출력을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-139">Enable verbose output for details on OpenAPI description searching, parsing, and validation</span></span>

<span data-ttu-id="27ff1-140">`connect` 명령으로 `--verbose` 옵션을 지정하면 도구에서 OpenAPI 설명을 검색하고, 구문 분석하고, 유효성을 검사할 때 자세한 정보가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-140">Specifying the `--verbose` option with the `connect` command will produce more details when the tool searches for the OpenAPI description, parses, and validates it.</span></span>

```console
connect <ROOT URI> --verbose
```

<span data-ttu-id="27ff1-141">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-141">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="27ff1-142">웹 API 탐색</span><span class="sxs-lookup"><span data-stu-id="27ff1-142">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="27ff1-143">사용 가능한 엔드포인트 보기</span><span class="sxs-lookup"><span data-stu-id="27ff1-143">View available endpoints</span></span>

<span data-ttu-id="27ff1-144">웹 API 주소의 현재 경로에 있는 다른 엔드포인트(컨트롤러)를 나열하려면 `ls` 또는 `dir` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-144">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhost:5001/> ls
```

<span data-ttu-id="27ff1-145">다음 출력 형식이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-145">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="27ff1-146">위의 출력은 사용 가능한 두 컨트롤러 `Fruits` 및 `People`을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-146">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="27ff1-147">두 컨트롤러 모두 매개 변수 없는 HTTP GET 및 POST 작업을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-147">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="27ff1-148">특정 컨트롤러로 이동하면 자세한 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-148">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="27ff1-149">예를 들어 다음 명령의 출력은 `Fruits` 컨트롤러에서 HTTP GET, PUT 및 DELETE 작업도 지원함을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-149">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="27ff1-150">각 작업의 경로에는 `id` 매개 변수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-150">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

<span data-ttu-id="27ff1-151">아니면 `ui` 명령을 실행하여 브라우저에서 웹 API의 Swagger UI 페이지를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-151">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="27ff1-152">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-152">For example:</span></span>

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="27ff1-153">엔드포인트로 이동</span><span class="sxs-lookup"><span data-stu-id="27ff1-153">Navigate to an endpoint</span></span>

<span data-ttu-id="27ff1-154">웹 API에서 다른 엔드포인트로 이동하려면 `cd` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-154">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/> cd people
```

<span data-ttu-id="27ff1-155">`cd` 명령 다음의 경로는 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-155">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="27ff1-156">다음 출력 형식이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-156">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a><span data-ttu-id="27ff1-157">HttpRepl 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="27ff1-157">Customize the HttpRepl</span></span>

<span data-ttu-id="27ff1-158">HttpRepl의 기본 [색](#set-color-preferences)을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-158">The HttpRepl's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="27ff1-159">또한 [기본 텍스트 편집기](#set-the-default-text-editor)를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-159">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="27ff1-160">HttpRepl 기본 설정은 현재 세션에서 유지되고 이후 세션에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-160">The HttpRepl preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="27ff1-161">기본 설정을 수정하는 경우 다음 파일에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-161">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="27ff1-162">Linux</span><span class="sxs-lookup"><span data-stu-id="27ff1-162">Linux</span></span>](#tab/linux)

<span data-ttu-id="27ff1-163">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="27ff1-163">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="27ff1-164">macOS</span><span class="sxs-lookup"><span data-stu-id="27ff1-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="27ff1-165">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="27ff1-165">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="27ff1-166">Windows</span><span class="sxs-lookup"><span data-stu-id="27ff1-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="27ff1-167">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="27ff1-167">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="27ff1-168">*Httpreplprefs* 파일은 시작할 때 로드되며 런타임 시 변경 내용에 대한 모니터링이 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-168">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="27ff1-169">파일에 직접 수정한 내용은 도구를 다시 시작한 후에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-169">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="27ff1-170">설정 보기</span><span class="sxs-lookup"><span data-stu-id="27ff1-170">View the settings</span></span>

<span data-ttu-id="27ff1-171">사용 가능한 설정을 보려면 `pref get` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-171">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="27ff1-172">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-172">For example:</span></span>

```console
https://localhost:5001/> pref get
```

<span data-ttu-id="27ff1-173">위의 명령은 사용 가능한 키-값 쌍을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-173">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="27ff1-174">색 기본 설정 지정</span><span class="sxs-lookup"><span data-stu-id="27ff1-174">Set color preferences</span></span>

<span data-ttu-id="27ff1-175">응답 색 지정은 현재 JSON에만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-175">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="27ff1-176">기본 HttpRepl 도구 색 지정을 사용자 지정하려면 변경할 색에 해당하는 키를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-176">To customize the default HttpRepl tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="27ff1-177">키를 찾는 방법에 대한 지침은 [설정 보기](#view-the-settings) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="27ff1-177">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="27ff1-178">예를 들어 다음과 같이 `colors.json` 키 값을 `Green`에서 `White`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-178">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people> pref set colors.json White
```

<span data-ttu-id="27ff1-179">[허용되는 색](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-179">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="27ff1-180">이후의 HTTP 요청은 새 색 지정을 사용하여 출력을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-180">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="27ff1-181">특정 색 키가 설정되지 않은 경우에는 보다 일반적인 키가 고려됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-181">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="27ff1-182">이 대체 동작을 보여 주려면 다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="27ff1-182">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="27ff1-183">`colors.json.name`에 값이 없으면 `colors.json.string`이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-183">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="27ff1-184">`colors.json.string`에 값이 없으면 `colors.json.literal`이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-184">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="27ff1-185">`colors.json.literal`에 값이 없으면 `colors.json`이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-185">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="27ff1-186">`colors.json`에 값이 없으면 명령 셸의 기본 텍스트 색(`AllowedColors.None`)이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-186">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="27ff1-187">들여쓰기 크기 설정</span><span class="sxs-lookup"><span data-stu-id="27ff1-187">Set indentation size</span></span>

<span data-ttu-id="27ff1-188">응답 들여쓰기 크기 사용자 지정은 현재 JSON에 대해서만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-188">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="27ff1-189">기본 크기는 두 개의 공백입니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-189">The default size is two spaces.</span></span> <span data-ttu-id="27ff1-190">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-190">For example:</span></span>

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

<span data-ttu-id="27ff1-191">기본 크기를 변경하려면 `formatting.json.indentSize` 키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-191">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="27ff1-192">예를 들어 항상 네 개의 공백을 사용하려면:</span><span class="sxs-lookup"><span data-stu-id="27ff1-192">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="27ff1-193">후속 응답은 네 개의 공백 설정을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-193">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="27ff1-194">기본 텍스트 편집기 설정</span><span class="sxs-lookup"><span data-stu-id="27ff1-194">Set the default text editor</span></span>

<span data-ttu-id="27ff1-195">기본적으로 HttpRepl에는 사용하도록 구성된 텍스트 편집기가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-195">By default, the HttpRepl has no text editor configured for use.</span></span> <span data-ttu-id="27ff1-196">HTTP 요청 본문이 필요한 웹 API 메서드를 테스트하려면 기본 텍스트 편집기를 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-196">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="27ff1-197">HttpRepl 도구는 요청 본문을 작성할 목적으로만 구성된 텍스트 편집기를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-197">The HttpRepl tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="27ff1-198">다음 명령을 실행하여 기본 설정 텍스트 편집기를 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-198">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="27ff1-199">앞의 명령에서는 `<EXECUTABLE>`이 텍스트 편집기 실행 파일의 전체 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-199">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="27ff1-200">예를 들어 다음 명령을 실행하여 Visual Studio Code를 기본 텍스트 편집기로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-200">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="27ff1-201">Linux</span><span class="sxs-lookup"><span data-stu-id="27ff1-201">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="27ff1-202">macOS</span><span class="sxs-lookup"><span data-stu-id="27ff1-202">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="27ff1-203">Windows</span><span class="sxs-lookup"><span data-stu-id="27ff1-203">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="27ff1-204">특정 CLI 인수를 사용하여 기본 텍스트 편집기를 시작하려면 `editor.command.default.arguments` 키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-204">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="27ff1-205">예를 들어 Visual Studio Code가 기본 텍스트 편집기이며 항상 HttpRepl이 확장을 사용하지 않도록 설정한 새 세션에서 Visual Studio Code를 열도록 하는 경우를 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-205">For example, assume Visual Studio Code is the default text editor and that you always want the HttpRepl to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="27ff1-206">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-206">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> <span data-ttu-id="27ff1-207">기본 편집기가 Visual Studio Code인 경우 일반적으로 `-w` 또는 `--wait` 인수를 전달하여 Visual Studio Code가 반환하기 전에 사용자가 파일을 닫을 때까지 대기하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-207">If your default editor is Visual Studio Code, you'll usually want to pass the `-w` or `--wait` argument to force Visual Studio Code to wait for you to close the file before returning.</span></span>

### <a name="set-the-openapi-description-search-paths"></a><span data-ttu-id="27ff1-208">OpenAPI 설명 검색 경로 설정</span><span class="sxs-lookup"><span data-stu-id="27ff1-208">Set the OpenAPI Description search paths</span></span>

<span data-ttu-id="27ff1-209">기본적으로 HttpRepl에는 `--openapi` 옵션 없이 `connect` 명령을 실행할 때 OpenAPI 설명을 찾는 데 사용하는 상대 경로 세트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-209">By default, the HttpRepl has a set of relative paths that it uses to find the OpenAPI description when executing the `connect` command without the `--openapi` option.</span></span> <span data-ttu-id="27ff1-210">이러한 상대 경로는 `connect` 명령에서 지정된 루트 및 기본 경로와 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-210">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="27ff1-211">기본 상대 경로는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-211">The default relative paths are:</span></span>

- <span data-ttu-id="27ff1-212">*swagger.json*</span><span class="sxs-lookup"><span data-stu-id="27ff1-212">*swagger.json*</span></span>
- <span data-ttu-id="27ff1-213">*swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="27ff1-213">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="27ff1-214">*/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="27ff1-214">*/swagger.json*</span></span>
- <span data-ttu-id="27ff1-215">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="27ff1-215">*/swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="27ff1-216">*openapi. json*</span><span class="sxs-lookup"><span data-stu-id="27ff1-216">*openapi.json*</span></span>
- <span data-ttu-id="27ff1-217">*/openapi.json*</span><span class="sxs-lookup"><span data-stu-id="27ff1-217">*/openapi.json*</span></span>

<span data-ttu-id="27ff1-218">사용자의 환경에서 서로 다른 검색 경로 집합을 사용하려면 `swagger.searchPaths` 기본 설정을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-218">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="27ff1-219">값은 파이프로 구분된 상대 경로 목록이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-219">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="27ff1-220">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-220">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

<span data-ttu-id="27ff1-221">기본 목록을 완전히 바꾸는 대신 경로를 추가하거나 제거하여 목록을 수정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-221">Instead of replacing the default list altogether, the list can also be modified by adding or removing paths.</span></span>

<span data-ttu-id="27ff1-222">기본 목록에 하나 이상의 검색 경로를 추가하려면 `swagger.addToSearchPaths` 기본 설정으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-222">To add one or more search paths to the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="27ff1-223">값은 파이프로 구분된 상대 경로 목록이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-223">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="27ff1-224">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-224">For example:</span></span>

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

<span data-ttu-id="27ff1-225">기본 목록에서 하나 이상의 검색 경로를 제거하려면 `swagger.addToSearchPaths` 기본 설정으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-225">To remove one or more search paths from the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="27ff1-226">값은 파이프로 구분된 상대 경로 목록이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-226">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="27ff1-227">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-227">For example:</span></span>

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="27ff1-228">HTTP GET 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="27ff1-228">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="27ff1-229">개요</span><span class="sxs-lookup"><span data-stu-id="27ff1-229">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="27ff1-230">인수</span><span class="sxs-lookup"><span data-stu-id="27ff1-230">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="27ff1-231">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-231">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="27ff1-232">옵션</span><span class="sxs-lookup"><span data-stu-id="27ff1-232">Options</span></span>

<span data-ttu-id="27ff1-233">다음 옵션은 `get` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-233">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="27ff1-234">예</span><span class="sxs-lookup"><span data-stu-id="27ff1-234">Example</span></span>

<span data-ttu-id="27ff1-235">HTTP GET 요청을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="27ff1-235">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="27ff1-236">이를 지원하는 엔드포인트에서 `get` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-236">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> get
    ```

    <span data-ttu-id="27ff1-237">앞의 명령으로 인해 다음 출력 형식이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-237">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="27ff1-238">`get` 명령에 매개 변수를 전달하여 특정 레코드를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-238">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people> get 2
    ```

    <span data-ttu-id="27ff1-239">앞의 명령으로 인해 다음 출력 형식이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-239">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="27ff1-240">HTTP POST 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="27ff1-240">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="27ff1-241">개요</span><span class="sxs-lookup"><span data-stu-id="27ff1-241">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="27ff1-242">인수</span><span class="sxs-lookup"><span data-stu-id="27ff1-242">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="27ff1-243">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-243">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="27ff1-244">옵션</span><span class="sxs-lookup"><span data-stu-id="27ff1-244">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="27ff1-245">예</span><span class="sxs-lookup"><span data-stu-id="27ff1-245">Example</span></span>

<span data-ttu-id="27ff1-246">HTTP POST 요청을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="27ff1-246">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="27ff1-247">이를 지원하는 엔드포인트에서 `post` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-247">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    <span data-ttu-id="27ff1-248">앞의 명령에서 `Content-Type` HTTP 요청 헤더는 JSON의 요청 본문 미디어 유형을 나타내도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-248">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="27ff1-249">기본 텍스트 편집기는 HTTP 요청 본문을 나타내는 JSON 템플릿을 사용하여 *.tmp* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-249">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="27ff1-250">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-250">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="27ff1-251">기본 텍스트 편집기를 설정하려면 [기본 텍스트 편집기 설정](#set-the-default-text-editor) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="27ff1-251">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="27ff1-252">모델 유효성 검사 요구 사항을 충족하도록 JSON 템플릿을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-252">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="27ff1-253">*.tmp* 파일을 저장하고 텍스트 편집기를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-253">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="27ff1-254">명령 셸에 다음 출력이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-254">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="27ff1-255">HTTP PUT 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="27ff1-255">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="27ff1-256">개요</span><span class="sxs-lookup"><span data-stu-id="27ff1-256">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="27ff1-257">인수</span><span class="sxs-lookup"><span data-stu-id="27ff1-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="27ff1-258">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="27ff1-259">옵션</span><span class="sxs-lookup"><span data-stu-id="27ff1-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="27ff1-260">예</span><span class="sxs-lookup"><span data-stu-id="27ff1-260">Example</span></span>

<span data-ttu-id="27ff1-261">HTTP PUT 요청을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="27ff1-261">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="27ff1-262">*선택 사항*: `get` 명령을 실행하여 데이터를 수정하기 전에 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-262">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

1. <span data-ttu-id="27ff1-263">이를 지원하는 엔드포인트에서 `put` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-263">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="27ff1-264">앞의 명령에서 `Content-Type` HTTP 요청 헤더는 JSON의 요청 본문 미디어 유형을 나타내도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-264">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="27ff1-265">기본 텍스트 편집기는 HTTP 요청 본문을 나타내는 JSON 템플릿을 사용하여 *.tmp* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-265">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="27ff1-266">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-266">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="27ff1-267">기본 텍스트 편집기를 설정하려면 [기본 텍스트 편집기 설정](#set-the-default-text-editor) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="27ff1-267">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="27ff1-268">모델 유효성 검사 요구 사항을 충족하도록 JSON 템플릿을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-268">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="27ff1-269">*.tmp* 파일을 저장하고 텍스트 편집기를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-269">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="27ff1-270">명령 셸에 다음 출력이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-270">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="27ff1-271">*선택 사항*: `get` 명령을 실행하여 수정 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-271">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="27ff1-272">예를 들어 텍스트 편집기에서 "Cherry"를 입력한 경우 `get`은 다음 출력을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-272">For example, if you typed "Cherry" in the text editor, a `get` returns the following output:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="27ff1-273">HTTP DELETE 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="27ff1-273">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="27ff1-274">개요</span><span class="sxs-lookup"><span data-stu-id="27ff1-274">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="27ff1-275">인수</span><span class="sxs-lookup"><span data-stu-id="27ff1-275">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="27ff1-276">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-276">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="27ff1-277">옵션</span><span class="sxs-lookup"><span data-stu-id="27ff1-277">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="27ff1-278">예</span><span class="sxs-lookup"><span data-stu-id="27ff1-278">Example</span></span>

<span data-ttu-id="27ff1-279">HTTP DELETE 요청을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="27ff1-279">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="27ff1-280">*선택 사항*: `get` 명령을 실행하여 데이터를 수정하기 전에 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-280">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

1. <span data-ttu-id="27ff1-281">이를 지원하는 엔드포인트에서 `delete` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-281">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    <span data-ttu-id="27ff1-282">앞의 명령으로 인해 다음 출력 형식이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-282">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="27ff1-283">*선택 사항*: `get` 명령을 실행하여 수정 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-283">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="27ff1-284">이 예제에서 `get`은 다음 출력을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-284">In this example, a `get` returns the following output:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="27ff1-285">HTTP PATCH 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="27ff1-285">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="27ff1-286">개요</span><span class="sxs-lookup"><span data-stu-id="27ff1-286">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="27ff1-287">인수</span><span class="sxs-lookup"><span data-stu-id="27ff1-287">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="27ff1-288">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-288">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="27ff1-289">옵션</span><span class="sxs-lookup"><span data-stu-id="27ff1-289">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="27ff1-290">HTTP HEAD 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="27ff1-290">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="27ff1-291">개요</span><span class="sxs-lookup"><span data-stu-id="27ff1-291">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="27ff1-292">인수</span><span class="sxs-lookup"><span data-stu-id="27ff1-292">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="27ff1-293">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-293">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="27ff1-294">옵션</span><span class="sxs-lookup"><span data-stu-id="27ff1-294">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="27ff1-295">HTTP OPTIONS 요청 테스트</span><span class="sxs-lookup"><span data-stu-id="27ff1-295">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="27ff1-296">개요</span><span class="sxs-lookup"><span data-stu-id="27ff1-296">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="27ff1-297">인수</span><span class="sxs-lookup"><span data-stu-id="27ff1-297">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="27ff1-298">연결된 컨트롤러 동작 메서드에서 예상되는 경로 매개 변수(있는 경우)입니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-298">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="27ff1-299">옵션</span><span class="sxs-lookup"><span data-stu-id="27ff1-299">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="27ff1-300">HTTP 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="27ff1-300">Set HTTP request headers</span></span>

<span data-ttu-id="27ff1-301">HTTP 요청 헤더를 설정하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-301">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="27ff1-302">HTTP 요청을 사용하여 인라인으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-302">Set inline with the HTTP request.</span></span> <span data-ttu-id="27ff1-303">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-303">For example:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="27ff1-304">앞의 방법을 사용하는 경우 개별 HTTP 요청 헤더에는 자체 `-h` 옵션이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-304">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="27ff1-305">HTTP 요청을 보내기 전에 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-305">Set before sending the HTTP request.</span></span> <span data-ttu-id="27ff1-306">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-306">For example:</span></span>

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    <span data-ttu-id="27ff1-307">요청을 보내기 전에 헤더를 설정하는 경우 헤더는 명령 셸 세션 기간에 설정된 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-307">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="27ff1-308">헤더를 지우려면 빈 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-308">To clear the header, provide an empty value.</span></span> <span data-ttu-id="27ff1-309">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-309">For example:</span></span>
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="27ff1-310">보안 엔드포인트 테스트</span><span class="sxs-lookup"><span data-stu-id="27ff1-310">Test secured endpoints</span></span>

<span data-ttu-id="27ff1-311">HttpRepl은 다음과 같은 방법으로 보안 엔드포인트의 테스트를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-311">The HttpRepl supports the testing of secured endpoints in the following ways:</span></span>

* <span data-ttu-id="27ff1-312">로그인한 사용자의 기본 자격 증명을 통해</span><span class="sxs-lookup"><span data-stu-id="27ff1-312">Via the default credentials of the logged in user.</span></span>
* <span data-ttu-id="27ff1-313">HTTP 요청 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-313">Through the use of HTTP request headers.</span></span>

### <a name="default-credentials"></a><span data-ttu-id="27ff1-314">기본 자격 증명</span><span class="sxs-lookup"><span data-stu-id="27ff1-314">Default credentials</span></span>

<span data-ttu-id="27ff1-315">IIS에서 호스팅되고 Windows 인증으로 보호되는 테스트 중인 웹 API를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="27ff1-315">Consider a web API you're testing that's hosted in IIS and secured with Windows authentication.</span></span> <span data-ttu-id="27ff1-316">도구를 실행하는 사용자의 자격 증명이 테스트 중인 HTTP 엔드포인트로 이동하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-316">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="27ff1-317">로그인한 사용자의 기본 자격 증명을 전달하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-317">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="27ff1-318">`httpClient.useDefaultCredentials` 기본 설정을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-318">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="27ff1-319">웹 API에 다른 요청을 보내기 전에 도구를 종료하고 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-319">Exit and restart the tool before sending another request to the web API.</span></span>
 
### <a name="default-proxy-credentials"></a><span data-ttu-id="27ff1-320">기본 프록시 자격 증명</span><span class="sxs-lookup"><span data-stu-id="27ff1-320">Default proxy credentials</span></span>

<span data-ttu-id="27ff1-321">테스트 중인 웹 API에서 Windows 인증으로 보호되는 프록시 뒤에 있는 시나리오를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="27ff1-321">Consider a scenario in which the web API you're testing is behind a proxy secured with Windows authentication.</span></span> <span data-ttu-id="27ff1-322">도구를 실행하는 사용자의 자격 증명이 프록시로 이동하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-322">You want the credentials of the user running the tool to flow to the proxy.</span></span> <span data-ttu-id="27ff1-323">로그인한 사용자의 기본 자격 증명을 전달하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-323">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="27ff1-324">`httpClient.proxy.useDefaultCredentials` 기본 설정을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-324">Set the `httpClient.proxy.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. <span data-ttu-id="27ff1-325">웹 API에 다른 요청을 보내기 전에 도구를 종료하고 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-325">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="27ff1-326">HTTP 요청 헤더</span><span class="sxs-lookup"><span data-stu-id="27ff1-326">HTTP request headers</span></span>

<span data-ttu-id="27ff1-327">지원되는 인증 및 권한 부여 스키마의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-327">Examples of supported authentication and authorization schemes include:</span></span>

* <span data-ttu-id="27ff1-328">basic authentication</span><span class="sxs-lookup"><span data-stu-id="27ff1-328">basic authentication</span></span>
* <span data-ttu-id="27ff1-329">JWT 전달자 토큰</span><span class="sxs-lookup"><span data-stu-id="27ff1-329">JWT bearer tokens</span></span>
* <span data-ttu-id="27ff1-330">다이제스트 인증</span><span class="sxs-lookup"><span data-stu-id="27ff1-330">digest authentication</span></span>

<span data-ttu-id="27ff1-331">예를 들어 다음 명령을 사용하여 엔드포인트에 전달자 토큰을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-331">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="27ff1-332">Azure에서 호스트되는 엔드포인트에 액세스하거나 [Azure REST API](/rest/api/azure/)를 사용하려면 전달자 토큰이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-332">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="27ff1-333">[Azure CLI](/cli/azure/)를 통해 Azure 구독에 대한 전달자 토큰을 가져오려면 다음 단계를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-333">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="27ff1-334">HttpRepl은 HTTP 요청 헤더에서 전달자 토큰을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-334">The HttpRepl sets the bearer token in an HTTP request header.</span></span> <span data-ttu-id="27ff1-335">Azure App Service Web Apps 목록이 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-335">A list of Azure App Service Web Apps is retrieved.</span></span>

1. <span data-ttu-id="27ff1-336">Azure에 로그인:</span><span class="sxs-lookup"><span data-stu-id="27ff1-336">Sign in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="27ff1-337">다음 명령을 사용하여 구독 ID를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-337">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="27ff1-338">구독 ID를 복사하고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-338">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="27ff1-339">다음 명령을 사용하여 전달자 토큰을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-339">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="27ff1-340">HttpRepl을 통해 Azure REST API에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-340">Connect to the Azure REST API via the HttpRepl:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="27ff1-341">`Authorization` HTTP 요청 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-341">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="27ff1-342">구독으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-342">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="27ff1-343">구독의 Azure App Service Web Apps 목록을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-343">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="27ff1-344">다음 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-344">The following response is displayed:</span></span>

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

## <a name="toggle-http-request-display"></a><span data-ttu-id="27ff1-345">HTTP 요청 표시 토글</span><span class="sxs-lookup"><span data-stu-id="27ff1-345">Toggle HTTP request display</span></span>

<span data-ttu-id="27ff1-346">기본적으로 보내는 HTTP 요청 표시는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-346">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="27ff1-347">명령 셸 세션 기간에 해당하는 설정을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-347">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="27ff1-348">요청 표시 사용</span><span class="sxs-lookup"><span data-stu-id="27ff1-348">Enable request display</span></span>

<span data-ttu-id="27ff1-349">`echo on` 명령을 실행하여 보내는 HTTP 요청을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-349">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="27ff1-350">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-350">For example:</span></span>

```console
https://localhost:5001/people> echo on
Request echoing is on
```

<span data-ttu-id="27ff1-351">현재 세션의 후속 HTTP 요청은 요청 헤더를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-351">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="27ff1-352">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-352">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="27ff1-353">요청 표시 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="27ff1-353">Disable request display</span></span>

<span data-ttu-id="27ff1-354">`echo off` 명령을 실행하여 보내는 HTTP 요청을 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-354">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="27ff1-355">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-355">For example:</span></span>

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="27ff1-356">스크립트 실행</span><span class="sxs-lookup"><span data-stu-id="27ff1-356">Run a script</span></span>

<span data-ttu-id="27ff1-357">동일한 HttpRepl 명령 세트를 자주 실행하는 경우에는 텍스트 파일에 저장하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-357">If you frequently execute the same set of HttpRepl commands, consider storing them in a text file.</span></span> <span data-ttu-id="27ff1-358">파일의 명령은 명령줄에서 수동으로 실행되는 명령과 동일한 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-358">Commands in the file take the same form as commands executed manually on the command line.</span></span> <span data-ttu-id="27ff1-359">`run` 명령을 사용하여 일괄적으로 명령을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-359">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="27ff1-360">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-360">For example:</span></span>

1. <span data-ttu-id="27ff1-361">줄 바꿈 기호로 분리된 명령 세트를 포함하는 텍스트 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-361">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="27ff1-362">다음 명령을 포함하는 *people-script.txt* 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-362">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="27ff1-363">텍스트 파일의 경로를 전달하여 `run` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-363">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="27ff1-364">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-364">For example:</span></span>

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="27ff1-365">다음과 같은 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-365">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="27ff1-366">출력 지우기</span><span class="sxs-lookup"><span data-stu-id="27ff1-366">Clear the output</span></span>

<span data-ttu-id="27ff1-367">HttpRepl 도구에 의해 명령 셸에 작성된 모든 출력을 제거하려면 `clear` 또는 `cls` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-367">To remove all output written to the command shell by the HttpRepl tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="27ff1-368">설명을 위해 명령 셸에 다음 출력이 포함되어 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-368">To illustrate, imagine the command shell contains the following output:</span></span>

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

<span data-ttu-id="27ff1-369">다음 명령을 실행하여 출력을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-369">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/> clear
```

<span data-ttu-id="27ff1-370">앞의 명령을 실행한 후 명령 셸은 다음 출력만 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="27ff1-370">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a><span data-ttu-id="27ff1-371">추가 자료</span><span class="sxs-lookup"><span data-stu-id="27ff1-371">Additional resources</span></span>

* [<span data-ttu-id="27ff1-372">REST API 요청</span><span class="sxs-lookup"><span data-stu-id="27ff1-372">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="27ff1-373">HttpRepl GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="27ff1-373">HttpRepl GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
* [<span data-ttu-id="27ff1-374">HttpRepl을 시작하도록 Visual Studio 구성</span><span class="sxs-lookup"><span data-stu-id="27ff1-374">Configure Visual Studio to launch HttpRepl</span></span>](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-windows-to-launch-httprepl-on-f5)
* [<span data-ttu-id="27ff1-375">HttpRepl을 시작하도록 Visual Studio Code 구성</span><span class="sxs-lookup"><span data-stu-id="27ff1-375">Configure Visual Studio Code to launch HttpRepl</span></span>](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-code-to-launch-httprepl-on-debug)
* [<span data-ttu-id="27ff1-376">HttpRepl을 시작하도록 Mac용 Visual Studio 구성</span><span class="sxs-lookup"><span data-stu-id="27ff1-376">Configure Visual Studio for Mac to launch HttpRepl</span></span>](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-mac-to-launch-httprepl-as-a-custom-tool)
