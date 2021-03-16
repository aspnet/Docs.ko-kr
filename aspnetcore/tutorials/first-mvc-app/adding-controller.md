---
title: 2부. ASP.NET Core MVC 앱에 컨트롤러 추가
author: rick-anderson
description: ASP.NET Core MVC에 대한 자습서 시리즈의 2부입니다.
ms.author: riande
ms.date: 01/23/2021
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
uid: tutorials/first-mvc-app/adding-controller
ms.custom: contperf-fy21q3
ms.openlocfilehash: 4afee8c09a3eb1030bf68e7591e1686b18d5f7e9
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394449"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a>2부. ASP.NET Core MVC 앱에 컨트롤러 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

MVC( Model-View-Controller) 아키텍처 패턴은 다음 세 가지 주요 구성 요소로 앱을 구분합니다. **M** odel, **V** iew 및 **C** ontroller. MVC 패턴을 통해 기존 모놀리식 응용 프로그램보다 쉽게 테스트 가능하고 업데이트할 수 있는 앱을 만들 수 있습니다.

MVC 기반 앱에는 다음이 포함됩니다.

* **M** odels: 앱의 데이터를 나타내는 클래스입니다. 모델 클래스는 유효성 검사 논리를 사용하여 해당 데이터에 대한 비즈니스 규칙을 적용합니다. 일반적으로 모델 개체는 데이터베이스에서 모델 상태를 검색하고 저장합니다. 이 자습서에서 `Movie` 모델은 데이터베이스에서 영화 데이터를 검색하고 이를 뷰에 제공하거나 수정합니다. 수정된 데이터는 데이터베이스에 기록됩니다.
* **V** iews: 보기는 앱의 UI(사용자 인터페이스)를 표시하는 구성 요소입니다. 일반적으로 UI는 모델 데이터를 표시합니다.
* **C** ontrollers(컨트롤러): 다음을 수행하는 클래스입니다.
  * 브라우저 요청을 처리합니다.
  * 모델 데이터를 검색합니다.
  * 응답을 반환하는 뷰 템플릿을 호출합니다.

MVC 앱에서는 뷰에 정보만 표시됩니다. 컨트롤러는 사용자 입력 및 상호 작용을 처리하고 응답합니다. 예를 들어 컨트롤러는 URL 세그먼트 및 쿼리 문자열 값을 처리하고 이러한 값을 모델에 전달합니다. 모델은 이러한 값을 사용하여 데이터베이스를 쿼리할 수 있습니다. 다음은 그 예입니다. 

* `https://localhost:5001/Home/Privacy`: `Home` 컨트롤러 및 `Privacy` 작업을 지정합니다.
* `https://localhost:5001/Movies/Edit/5`: `Movies` 컨트롤러 및 `Edit` 작업을 사용하여 ID가 5인 동영상을 편집하는 요청으로, 자습서의 뒷부분에서 자세히 설명합니다.

경로 데이터는 자습서의 뒷 부분에서 설명합니다.

MVC 아키텍처 패턴은 앱을 모델, 뷰 및 컨트롤러의 세 가지 주요 구성 요소로 구분합니다. 이 패턴은 문제의 분리를 달성하는 데 도움이 됩니다. UI 논리는 뷰에 속합니다. 입력 논리는 컨트롤러에 속합니다. 비즈니스 논리는 모델에 속합니다. 이렇게 분리하면 다른 코드에 영향을 주지 않고 한 번에 한 구현 측면에서 작업할 수 있기 때문에 앱을 작성할 때 복잡성을 관리하는 데 도움이 됩니다. 예를 들어 비즈니스 논리 코드와 무관하게 보기 코드를 작업할 수 있습니다.

이 자습서 시리즈에서는 동영상 앱을 빌드하면서 이러한 개념을 소개하고 보여 줍니다. MVC 프로젝트는 *컨트롤러* 및 *보기* 를 위한 폴더를 포함하고 있습니다.

## <a name="add-a-controller"></a>컨트롤러 추가

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**솔루션 탐색기** 에서 **컨트롤러 > 추가 > 컨트롤러** 를 마우스 오른쪽 단추로 클릭합니다.

![솔루션 탐색기, 컨트롤러 > 추가 > 컨트롤러를 마우스 오른쪽 단추로 클릭](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)

**스캐폴드 추가** 대화 상자에서 **MVC 컨트롤러 - 비어 있음** 을 선택합니다.

![MVC 컨트롤러 추가 및 이름 지정](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

**새 항목 추가 - MvcMovie 대화 상자** 에서 **HelloWorldController.cs** 를 입력하고 **추가** 를 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**탐색기** 아이콘을 선택한 다음, **컨트롤러 > 새 파일** 을 컨트롤 클릭(마우스 오른쪽 단추로 클릭)하고 새 파일의 이름을 *HelloWorldController.cs* 로 지정합니다.

![상황에 맞는 메뉴](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

**솔루션 탐색기** 에서 **컨트롤러 > 추가 > 새 파일** 을 마우스 오른쪽 단추로 클릭합니다.

![상황에 맞는 메뉴](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

**ASP.NET Core** 및 **컨트롤러 클래스** 를 선택합니다.

컨트롤러의 이름을 **HelloWorldController** 로 지정합니다.

![MVC 컨트롤러 추가 및 이름 지정](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

*Controllers/HelloWorldController.cs* 의 내용을 다음으로 바꿉니다.

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

컨트롤러의 모든 `public` 메서드는 HTTP 엔드포인트로 호출할 수 있습니다. 위의 샘플에서 메서드는 모두 문자열을 반환합니다. 각 메서드 앞의 주석에 주목하세요.

HTTP 엔드포인트:

* 웹 애플리케이션에서 대상으로 지정 가능한 URL(예: `https://localhost:5001/HelloWorld`)입니다.
* 다음을 결합합니다.
  * 사용된 프로토콜: `HTTPS`.
  * TCP 포트를 포함한 웹 서버의 네트워크 위치: `localhost:5001`.
  * 대상 URI: `HelloWorld`.

첫 번째 주석은 이 항목이 기본 URL에 `/HelloWorld/`를 추가하여 호출되는 [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) 메서드라고 명시합니다.

두 번째 주석은 URL에 `/HelloWorld/Welcome/`을 추가하여 호출되는 [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) 메서드를 명시합니다. 자습서 뒷부분에서는 스캐폴딩 엔진을 사용하여 데이터를 업데이트하는 `HTTP POST` 메서드를 생성합니다.

디버거 없이 앱을 실행합니다.

주소 표시줄의 경로에 “HelloWorld”를 추가합니다. `Index` 메서드가 문자열을 반환합니다.

![This is my default action이라는 앱 응답을 보여 주는 브라우저 창](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC는 들어오는 URL에 따라 컨트롤러 클래스와 해당 클래스 내의 작업 메서드를 호출합니다. MVC에서 사용하는 기본 [URL 라우팅 논리](xref:mvc/controllers/routing)는 다음과 같은 형식을 사용하여 호출할 코드를 결정합니다.

`/[Controller]/[ActionName]/[Parameters]`

라우팅 서식은 *Startup.cs* 파일의 `Configure` 메서드에서 설정됩니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

앱으로 이동할 때 URL 세그먼트를 제공하지 않으면 위에 강조 표시된 템플릿 줄에 지정된 "Home" 컨트롤러 및 "Index" 메서드가 기본값으로 사용됩니다.  위 URL 세그먼트는 다음과 같습니다.

* 첫 번째 URL 세그먼트는 실행할 컨트롤러 클래스를 결정합니다. 따라서 `localhost:5001/HelloWorld`는 **HelloWorld** Controller 클래스에 매핑됩니다.
* URL 세그먼트의 두 번째 부분은 클래스의 작업 메서드를 결정합니다. 따라서 `localhost:5001/HelloWorld/Index`는 `HelloWorldController` 클래스의 `Index` 메서드가 실행되도록 합니다. `localhost:5001/HelloWorld`로만 이동했음에도 기본적으로 `Index` 메서드가 호출되었음에 주의하세요. `Index`는 메서드 이름이 명시적으로 지정되지 않은 경우 컨트롤러에서 호출되는 기본 메서드입니다.
* URL 세그먼트의 세 번째 부분(`id`)은 경로 데이터입니다. 경로 데이터는 자습서의 뒷 부분에서 설명합니다.

`https://localhost:{PORT}/HelloWorld/Welcome`으로 이동합니다. `{PORT}`를 포트 번호로 바꿉니다.

`Welcome` 메서드가 실행되고 문자열 `This is the Welcome action method...`를 반환합니다. 이 URL의 경우 컨트롤러는 `HelloWorld`이고 `Welcome`이 작업 메서드입니다. 아직 URL의 `[Parameters]` 부분을 사용하지 않았습니다.

![브라우저 창에 This is the Welcome action method라는 애플리케이션 응답이 표시됩니다.](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

URL에서 컨트롤러에 일부 매개 변수 정보를 전달하도록 코드를 수정합니다. 예: `/HelloWorld/Welcome?name=Rick&numtimes=4`.

다음 코드와 같이 `Welcome` 메서드가 두 개의 매개 변수를 포함하도록 변경합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

위의 코드는

* C#의 선택적 매개 변수 기능을 사용하여 `numTimes` 매개 변수에 대해 전달된 값이 없는 경우 해당 매개 변수의 기본값이 1임을 나타냅니다.
* `HtmlEncoder.Default.Encode`를 사용하여 JavaScript 사용과 같은 악의적인 입력으로부터 앱을 보호합니다.
* `$"Hello {name}, NumTimes is: {numTimes}"`에서 [보간된 문자열](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)을 사용합니다.

앱을 실행하고 `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`로 이동합니다. `{PORT}`를 포트 번호로 바꿉니다.

URL에서 `name` 및 `numtimes`에 다른 값을 사용합니다. MVC [모델 바인딩](xref:mvc/models/model-binding) 시스템은 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수를 자동으로 매핑합니다. 자세한 정보는 [모델 바인딩](xref:mvc/models/model-binding)을 참조하세요.

![Hello Rick, NumTimes의 애플리케이션 응답을 보여주는 브라우저 창\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

위 이미지는 다음과 같습니다.

* URL 세그먼트 `Parameters`가 사용되지 않습니다.
* `name` 및 `numTimes` 매개 변수는 [쿼리 문자열](https://wikipedia.org/wiki/Query_string)로 전달됩니다.
* 위 URL에서 `?`(물음표)는 구분 기호이며, 다음에 쿼리 문자열이 옵니다.
* `&` 문자는 필드-값 쌍을 구분합니다.

`Welcome` 메서드를 다음 코드로 바꿉니다.

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

앱을 실행하고 `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick` URL을 입력합니다.

위 URL은 다음과 같습니다.

* 세 번째 URL 세그먼트는 경로 매개 변수 `id`와 일치합니다. 
* `Welcome` 메서드는 `MapControllerRoute` 메서드의 URL 템플릿과 일치하는 `id` 매개 변수를 포함하고 있습니다.
* 후행 `?`는 [쿼리 문자열](https://wikipedia.org/wiki/Query_string)을 시작합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?name=snippet_route&highlight=5)]

앞의 예제에서:

* 세 번째 URL 세그먼트는 경로 매개 변수 `id`와 일치합니다.
* `Welcome` 메서드는 `MapControllerRoute` 메서드의 URL 템플릿과 일치하는 `id` 매개 변수를 포함하고 있습니다.
* 후행 `?`(`id?`의)는 `id` 매개 변수가 선택 사항임을 나타냅니다.

> [!div class="step-by-step"]
> [이전: 시작하기](start-mvc.md)
> [다음: 뷰 추가](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

MVC( Model-View-Controller) 아키텍처 패턴은 다음 세 가지 주요 구성 요소로 앱을 구분합니다. **M** odel, **V** iew 및 **C** ontroller. MVC 패턴을 통해 기존 모놀리식 응용 프로그램보다 쉽게 테스트 가능하고 업데이트할 수 있는 앱을 만들 수 있습니다. MVC 기반 앱에는 다음이 포함됩니다.

* **M** odels: 앱의 데이터를 나타내는 클래스입니다. 모델 클래스는 유효성 검사 논리를 사용하여 해당 데이터에 대한 비즈니스 규칙을 적용합니다. 일반적으로 모델 개체는 데이터베이스에서 모델 상태를 검색하고 저장합니다. 이 자습서에서 `Movie` 모델은 데이터베이스에서 영화 데이터를 검색하고 이를 뷰에 제공하거나 수정합니다. 수정된 데이터는 데이터베이스에 기록됩니다.

* **V** iews: 보기는 앱의 UI(사용자 인터페이스)를 표시하는 구성 요소입니다. 일반적으로 UI는 모델 데이터를 표시합니다.

* **C** ontrollers: 브라우저 요청을 처리하는 클래스입니다. 모델 데이터를 검색하고 응답을 반환하는 보기 템플릿을 호출합니다. MVC 앱에서 보기는 단지 정보만 표시하며, 사용자 입력 및 상호 작용은 컨트롤러가 처리하고 응답합니다. 예를 들어 컨트롤러는 경로 데이터 및 쿼리 문자열 값을 처리하고 모델에 이러한 값을 전달합니다. 모델은 이러한 값을 사용하여 데이터베이스를 쿼리할 수 있습니다. 예를 들어 `https://localhost:5001/Home/About`에는 `Home`(컨트롤러) 및 `About`(호출할 홈 컨트롤러의 작업 메서드)라는 경로 데이터가 있습니다. `https://localhost:5001/Movies/Edit/5`는 영화 컨트롤러를 사용하여 ID=5인 영화를 편집하기 위한 요청입니다. 경로 데이터는 자습서의 뒷 부분에서 설명합니다.

MVC 패턴을 사용하면 앱의 다양한 측면(입력 논리, 비즈니스 논리 및 UI 논리)을 분리하면서 이러한 요소 간에 느슨한 결합을 제공하는 앱을 만들 수 있습니다. 이 패턴은 각 종류의 논리가 앱에 있어야 하는 위치를 지정합니다. UI 논리는 보기에 속합니다. 입력 논리는 컨트롤러에 속합니다. 비즈니스 논리는 모델에 속합니다. 이렇게 분리하면 다른 코드에 영향을 주지 않고 한 번에 한 구현 측면에서 작업할 수 있기 때문에 앱을 만들 때 복잡성을 관리하는 데 도움이 됩니다. 예를 들어 비즈니스 논리 코드와 무관하게 보기 코드를 작업할 수 있습니다.

이 자습서 시리즈에서는 이러한 개념을 설명하고 영화 앱을 만들기 위해 사용하는 방법을 보여줍니다. MVC 프로젝트는 *컨트롤러* 및 *보기* 를 위한 폴더를 포함하고 있습니다.

## <a name="add-a-controller"></a>컨트롤러 추가

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기** 에서 **컨트롤러 > 추가 > 컨트롤러** 를 마우스 오른쪽 단추로 클릭합니다.

  ![상황에 맞는 메뉴](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* **스캐폴드 추가** 대화 상자에서 **MVC 컨트롤러 - 비어 있음** 을 선택합니다.

  ![MVC 컨트롤러 추가 및 이름 지정](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* **빈 MVC 컨트롤러 추가 대화 상자** 에 **HelloWorldController** 를 입력하고 **추가** 를 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**탐색기** 아이콘을 선택한 다음, **컨트롤러 > 새 파일** 을 컨트롤 클릭(마우스 오른쪽 단추로 클릭)하고 새 파일의 이름을 *HelloWorldController.cs* 로 지정합니다.

  ![상황에 맞는 메뉴](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

**솔루션 탐색기** 에서 **컨트롤러 > 추가 > 새 파일** 을 마우스 오른쪽 단추로 클릭합니다.

![상황에 맞는 메뉴](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

**ASP.NET Core** 및 **MVC 컨트롤러 클래스** 를 선택합니다.

컨트롤러의 이름을 **HelloWorldController** 로 지정합니다.

![MVC 컨트롤러 추가 및 이름 지정](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

*Controllers/HelloWorldController.cs* 의 내용을 다음으로 바꿉니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

컨트롤러의 모든 `public` 메서드는 HTTP 엔드포인트로 호출할 수 있습니다. 위의 샘플에서 메서드는 모두 문자열을 반환합니다. 각 메서드 앞의 주석에 주목하세요.

HTTP 엔드포인트는 웹 응용 프로그램에서 대상으로 지정 가능한 URL(예: `https://localhost:5001/HelloWorld`)로써, 사용되는 프로토콜(`HTTPS`), 웹 서버의 네트워크 위치(`localhost:5001`, TCP 포트 포함) 및 대상 URI인 `HelloWorld`를 결합합니다.

첫 번째 주석은 이 항목이 기본 URL에 `/HelloWorld/`를 추가하여 호출되는 [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) 메서드라고 명시합니다. 두 번째 주석은 URL에 `/HelloWorld/Welcome/`을 추가하여 호출되는 [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 메서드를 명시합니다. 나중에 이 자습서에서는 스캐폴딩 엔진을 사용하여 데이터를 수정하는 `HTTP POST` 메서드를 생성합니다.

비 디버그 모드로 앱을 실행하고 주소 표시줄의 경로에 "HelloWorld"를 추가합니다. `Index` 메서드가 문자열을 반환합니다.

![브라우저 창에 This is my default action이라는 애플리케이션 응답이 표시됩니다.](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC는 들어오는 URL에 따라 컨트롤러 클래스(및 해당 클래스의 작업 메서드)를 호출합니다. MVC에서 사용하는 기본 [URL 라우팅 논리](xref:mvc/controllers/routing)는 다음과 같은 형식을 사용하여 호출할 코드를 결정합니다.

`/[Controller]/[ActionName]/[Parameters]`

라우팅 서식은 *Startup.cs* 파일의 `Configure` 메서드에서 설정됩니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

앱으로 이동할 때 URL 세그먼트를 제공하지 않으면 위에 강조 표시된 템플릿 줄에 지정된 "Home" 컨트롤러 및 "Index" 메서드가 기본값으로 사용됩니다.

첫 번째 URL 세그먼트는 실행할 컨트롤러 클래스를 결정합니다. 따라서 `localhost:{PORT}/HelloWorld`은 `HelloWorldController` 클래스에 매핑됩니다. URL 세그먼트의 두 번째 부분은 클래스의 작업 메서드를 결정합니다. 따라서 `localhost:{PORT}/HelloWorld/Index`는 `HelloWorldController` 클래스의 `Index` 메서드를 실행합니다. `localhost:{PORT}/HelloWorld`로만 이동했음에도 기본적으로 `Index` 메서드가 호출되었음에 주의하세요. 메서드 이름이 명시적으로 지정되지 않으면 `Index`가 컨트롤러에서 호출되는 기본 메서드이기 때문입니다. URL 세그먼트의 세 번째 부분(`id`)은 경로 데이터입니다. 경로 데이터는 자습서의 뒷 부분에서 설명합니다.

`https://localhost:{PORT}/HelloWorld/Welcome`으로 이동합니다. `Welcome` 메서드가 실행되고 문자열 `This is the Welcome action method...`를 반환합니다. 이 URL의 경우 컨트롤러는 `HelloWorld`이고 `Welcome`이 작업 메서드입니다. 아직 URL의 `[Parameters]` 부분을 사용하지 않았습니다.

![브라우저 창에 This is the Welcome action method라는 애플리케이션 응답이 표시됩니다.](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

URL에서 컨트롤러에 일부 매개 변수 정보를 전달하도록 코드를 수정합니다. 예: `/HelloWorld/Welcome?name=Rick&numtimes=4`. 다음 코드와 같이 `Welcome` 메서드가 두 개의 매개 변수를 포함하도록 변경합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

위의 코드는

* C#의 선택적 매개 변수 기능을 사용하여 `numTimes` 매개 변수에 대해 전달된 값이 없는 경우 해당 매개 변수의 기본값이 1임을 나타냅니다. <!-- remove for simplified -->
* `HtmlEncoder.Default.Encode`를 사용하여 악의적인 입력(예: JavaScript)으로부터 응용 프로그램을 보호합니다.
* `$"Hello {name}, NumTimes is: {numTimes}"`에서 [보간된 문자열](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)을 사용합니다. <!-- remove for simplified -->

앱을 실행하고 다음으로 이동합니다.

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(`{PORT}`를 포트 번호로 바꿉니다.) URL에서 `name` 및 `numtimes`에 다른 값을 사용할 수 있습니다. MVC [모델 바인딩](xref:mvc/models/model-binding) 시스템은 주소 표시줄의 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수를 자동으로 매핑합니다. 자세한 정보는 [모델 바인딩](xref:mvc/models/model-binding)을 참조하세요.

![Hello Rick, NumTimes의 애플리케이션 응답을 보여주는 브라우저 창\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

위의 이미지에서는 URL 세그먼트(`Parameters`)를 사용하지 않고 `name` 및 `numTimes` 매개 변수가 [쿼리 문자열](https://wikipedia.org/wiki/Query_string)로 전달됩니다. 위 URL에서 `?`(물음표)는 구분 기호이며, 다음에 쿼리 문자열이 옵니다. `&` 문자는 필드-값 쌍을 구분합니다.

`Welcome` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

앱을 실행하고 `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick` URL을 입력합니다.

이번에는 세 번째 URL 세그먼트가 `id` 경로 매개 변수와 일치합니다. `Welcome` 메서드는 `MapRoute` 메서드의 URL 템플릿과 일치하는 `id` 매개 변수를 포함하고 있습니다. 후행 `?`(`id?`의)는 `id` 매개 변수가 선택 사항임을 나타냅니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

이러한 예제에서 컨트롤러는 MVC의 "VC" 부분을 사용했습니다. 즉, 보기 및 컨트롤러 작업입니다. 컨트롤러가 HTML을 직접 반환하고 있습니다. 코드 및 유지 관리가 매우 복잡해지므로 일반적으로 컨트롤러에서 직접 HTML을 반환하지 않습니다. 대신 일반적으로 별도의 Razor 뷰 템플릿 파일을 사용하여 HTML 응답을 생성합니다. 다음 자습서에서는 해당 작업을 수행합니다.

> [!div class="step-by-step"]
> [이전](start-mvc.md)
> [다음](adding-view.md)

::: moniker-end
