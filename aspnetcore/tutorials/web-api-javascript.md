---
title: '자습서: JavaScript로 ASP.NET Core 웹 API 호출하기'
author: rick-anderson
description: JavaScript를 사용하여 ASP.NET Core 웹 API를 호출하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: c32c5befe0be3b1ad4bd87649d3cc74b0296a134
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94703711"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>자습서: JavaScript로 ASP.NET Core 웹 API 호출하기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)를 사용하여 JavaScript로 ASP.NET Core 웹 API를 호출하는 방법을 보여줍니다.

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.2의 경우에는 [JavaScript를 사용하여 웹 API 호출하기](xref:tutorials/first-web-api#call-the-web-api-with-javascript) 버전 2.2를 참조하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>사전 요구 사항

* [자습서: 웹 API 만들기](xref:tutorials/first-web-api) 완료
* CSS, HTML, JavaScript에 대한 지식

## <a name="call-the-web-api-with-javascript"></a>JavaScript를 사용하여 웹 API 호출

이 섹션에서는 할 일 항목의 생성과 관리를 위한 양식이 포함된 HTML 페이지를 추가하게 됩니다. 이벤트 처리기는 페이지의 요소에 연결됩니다. 이벤트 처리기에 의해 웹 API의 작업 메서드에 대한 HTTP 요청이 발생합니다. Fetch API의 `fetch` 함수는 각 HTTP 요청을 시작합니다.

`fetch` 함수는 [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) 개체를 반환하는데, 해당 개체에는 `Response` 개체로 나타나는 HTTP 응답이 포함됩니다. 일반적인 패턴은 `Response` 개체에 대해 `json` 함수를 호출하여 JSON 응답 본문을 추출하는 것입니다. JavaScript는 웹 API 응답의 세부 정보를 토대로 페이지를 업데이트합니다.

가장 간단한 `fetch` 호출에서는 경로를 나타내는 단일 매개 변수가 허용됩니다. `init` 개체라고 하는 두 번째 매개 변수는 선택 사항입니다. `init`은 HTTP 요청 구성에 사용됩니다.

1. [정적 파일을 제공](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하고 [기본 파일 매핑을 사용](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하도록 앱을 구성합니다. 다음의 강조 표시된 코드는 *Startup.cs* 의 `Configure` 메서드에서 필요합니다.

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. 프로젝트 루트에서 *wwwroot* 폴더를 만듭니다.

1. *wwwroot* 폴더 안에 *js* 폴더를 만듭니다.

1. *index.html* 이라는 HTML 파일을 *wwwroot* 폴더에 추가합니다. *index.html* 의 콘텐츠를 다음 마크업으로 바꿉니다.

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. *wwwroot/css* 폴더에 *site.css* 라는 CSS 파일을 추가합니다. *site.css* 의 콘텐츠를 다음 스타일로 바꿉니다.

    [!code-css[](first-web-api/samples/3.0/TodoApi/wwwroot/css/site.css)]

1. *site.js* 라는 JavaScript 파일을 *wwwroot/js* 폴더에 추가합니다. *site.js* 의 콘텐츠를 다음 코드로 바꿉니다.

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.

1. *Properties\launchSettings.json* 을 엽니다.
1. `launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.

이 샘플은 웹 API의 CRUD 메서드를 모두 호출합니다. 웹 API 요청에 대한 설명은 다음과 같습니다.

### <a name="get-a-list-of-to-do-items"></a>할 일 항목의 목록 가져오기

다음 코드에서는 HTTP GET 요청이 *api/TodoItems* 경로로 전송됩니다.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

웹 API가 성공한 상태 코드를 반환하면 `_displayItems` 함수가 호출됩니다. `_displayItems`에서 허용하는 배열 매개 변수의 각 할 일 항목은 **편집** 및 **삭제** 단추가 포함된 테이블로 추가됩니다. 웹 API 요청이 실패하는 경우 브라우저의 콘솔에 오류가 기록됩니다.

### <a name="add-a-to-do-item"></a>할 일 항목 추가

다음 코드에서:

* `item` 변수는 할 일 항목의 개체 리터럴 표현을 생성하기 위해 선언됩니다.
* Fetch 요청은 다음 옵션으로 구성됩니다.
  * `method` - POST HTTP 작업 동사를 지정합니다.
  * `body` - 요청 본문의 JSON 표현을 지정합니다. JSON은 `item`에 저장된 개체 리터럴을 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 함수로 전달하여 생성됩니다.
  * `headers` - `Accept` 및 `Content-Type` HTTP 요청 헤더를 지정합니다. 두 헤더 모두 `application/json`으로 설정되어 개별적으로 수신 및 전송되는 미디어 유형을 지정합니다.
* HTTP POST 요청은 *api/TodoItems* 경로에 전송됩니다.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

웹 API가 성공적인 상태 코드를 반환하면 `getItems` 함수가 호출되어 HTML 테이블을 업데이트합니다. 웹 API 요청이 실패하는 경우 브라우저의 콘솔에 오류가 기록됩니다.

### <a name="update-a-to-do-item"></a>할 일 항목 업데이트

할 일 항목 업데이트는 항목 추가와 비슷하지만 크게 두 가지에서 차이를 보입니다.

* 경로는 업데이트할 항목의 고유 식별자를 접미사로 가집니다. 예를 들면 *api/TodoItems/1* 과 같습니다.
* HTTP 작업 동사는 `method` 옵션으로 표시되는 바와 같이 PUT입니다.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>할 일 항목 삭제

할 일 항목을 삭제하려면 요청의 `method` 옵션을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정합니다.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

웹 API 도움말 페이지를 생성하는 방법을 알아보려면 다음 자습서로 계속 진행합니다.

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
