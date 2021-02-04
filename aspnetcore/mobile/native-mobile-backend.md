---
title: ASP.NET Core를 사용하여 네이티브 모바일 앱용 백 엔드 서비스 만들기
author: ardalis
description: ASP.NET Core MVC를 사용하여 네이티브 모바일 앱을 지원하는 백 엔드 서비스를 만드는 방법을 배웁니다.
ms.author: riande
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: 4e86241771e884ba9079bcdf9a09eebc6acd62c8
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530218"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>ASP.NET Core를 사용하여 네이티브 모바일 앱용 백 엔드 서비스 만들기

작성자: [Steve Smith](https://ardalis.com/)

모바일 앱은 ASP.NET Core 백 엔드 서비스와 통신할 수 있습니다. iOS 시뮬레이터 및 Android 에뮬레이터에서 로컬 웹 서비스를 연결하는 방법에 대한 지침은 [iOS 시뮬레이터 및 Android 에뮬레이터에서 로컬 웹 서비스에 연결](/xamarin/cross-platform/deploy-test/connect-to-local-web-services)을 참조하세요.

[샘플 백 엔드 서비스 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a>샘플 네이티브 모바일 앱

이 자습서에서는 네이티브 모바일 앱을 지원하기 위해 ASP.NET Core MVC를 사용하여 백 엔드 서비스를 만드는 방법을 보여 줍니다. Android, iOS, Windows 유니버설 및 Window Phone 디바이스에 대한 별도 네이티브 클라이언트를 포함하는 네이티브 클라이언트로 [Xamarin Forms ToDoRest 앱](/xamarin/xamarin-forms/data-cloud/consuming/rest)을 사용합니다. 연결된 자습서를 따라 네이티브 앱을 만들고(필요한 무료 Xamarin 도구 설치) Xamarin 샘플 솔루션을 다운로드할 수 있습니다. Xamarin 샘플에는 이 문서의 ASP.NET Core 앱이 바꾸는 ASP.NET Web API 2 서비스 프로젝트가 포함되어 있습니다(클라이언트에서 필요한 변경 내용 없이).

![Android 스마트폰에서 실행되는 To Do Rest 애플리케이션](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>기능

ToDoRest 앱은 할 일 항목 나열, 추가, 삭제 및 업데이트를 지원합니다. 각 항목에는 ID, 이름, 메모 및 완료되었는지 여부를 나타내는 속성이 있습니다.

위에 표시된 것처럼 항목의 주 보기는 각 항목의 이름을 나열하고 확인 표시로 완료되었는지를 나타냅니다.

`+` 아이콘을 누르면 항목 추가 대화 상자를 엽니다.

![항목 추가 대화 상자](native-mobile-backend/_static/todo-android-new-item.png)

기본 목록 화면에서 항목을 누르면 항목의 이름, 메모 및 완료 설정을 수정할 수 있거나 항목을 삭제할 수 있는 편집 대화 상자를 엽니다.

![항목 편집 대화 상자](native-mobile-backend/_static/todo-android-edit-item.png)

이 샘플은 기본적으로 읽기 전용 작업을 허용하는 developer.xamarin.com에서 호스팅되는 백 엔드 서비스를 사용하도록 구성됩니다. 컴퓨터에서 실행되는 다음 섹션에서 만든 ASP.NET Core 앱을 직접 테스트하려면 앱의 `RestUrl` 상수를 업데이트해야 합니다. `ToDoREST` 프로젝트로 이동하고 *Constants.cs* 파일을 엽니다. `RestUrl`을 컴퓨터의 IP 주소를 포함하는 URL로 바꿉니다(이 주소는 컴퓨터에서가 아니라 디바이스 에뮬레이터에서 사용되므로 localhost 또는 127.0.0.1이 아님). 포트 번호도 포함합니다(5000). 서비스가 디바이스와 작동하는지 테스트하기 위해 이 포트에 대한 액세스를 차단하는 활성 방화벽이 없는지 확인합니다.

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a>ASP.NET Core 프로젝트 만들기

Visual Studio에서 새 ASP.NET Core 웹 애플리케이션을 만듭니다. 웹 API 템플릿과 인증 안 함을 선택합니다. 프로젝트 이름을 *ToDoApi* 로 지정합니다.

![Web API 프로젝트 템플릿이 선택된 새 ASP.NET 웹 애플리케이션 대화 상자](native-mobile-backend/_static/web-api-template.png)

애플리케이션은 포트 5000에 대한 모든 요청에 응답해야 합니다. 이를 수행하기 위해 `.UseUrls("http://*:5000")`를 포함하도록 *Program.cs* 를 업데이트합니다.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> 기본적으로 로컬이 아닌 요청을 무시하는 IIS Express 뒤에서보다 애플리케이션을 직접 실행해야 합니다. 명령 프롬프트에서 [dotnet run](/dotnet/core/tools/dotnet-run)을 실행하거나 Visual Studio 도구 모음의 디버그 대상 드롭다운에서 애플리케이션 이름 프로필을 선택합니다.

할 일 항목을 나타내도록 모델 클래스를 추가합니다. `[Required]` 특성으로 필수 필드를 표시합니다.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

API 메서드에는 데이터를 사용하는 방법이 필요합니다. 원래 Xamarin 샘플에서 사용하는 동일한 `IToDoRepository` 인터페이스를 사용합니다.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

이 샘플의 경우 구현은 항목의 개별 컬렉션을 사용합니다.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

*Startup.cs* 에서 구현을 구성합니다.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

이 시점에서 *ToDoItemsController* 를 만들 준비가 되었습니다.

> [!TIP]
> [ASP.NET Core MVC 및 Visual Studio를 사용하여 첫 번째 Web API 빌드](../tutorials/first-web-api.md)에서 Web API를 만드는 방법에 대해 자세히 알아봅니다.

## <a name="creating-the-controller"></a>컨트롤러 만들기

프로젝트에 새 컨트롤러, *ToDoItemsController* 를 추가합니다. Microsoft.AspNetCore.Mvc.Controller에서 상속해야 합니다. `Route` 특성을 추가하여 컨트롤러에서 `api/todoitems`로 시작하는 경로에 대해 만든 요청을 처리하는 것을 나타냅니다. 경로의 `[controller]` 토큰은 컨트롤러의 이름으로 대체되며(`Controller` 접미사 생략) 글로벌 경로에 대해 특히 유용합니다. [라우팅](../fundamentals/routing.md)에 대해 자세히 알아봅니다.

컨트롤러는 작동하기 위해 `IToDoRepository`가 필요합니다. 컨트롤러의 생성자를 통해 이 유형의 인스턴스를 요청합니다. 런타임 시 이 인스턴스는 [종속성 주입](../fundamentals/dependency-injection.md)에 대한 프레임워크의 지원을 사용하여 제공됩니다.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

이 API는 데이터 원본에서 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 수행하도록 4개의 다른 HTTP 동사를 지원합니다. 이 중 가장 간단한 것은 HTTP GET 요청에 해당하는 읽기 작업입니다.

### <a name="reading-items"></a>항목 읽기

항목의 목록 요청은 `List` 메서드에 대한 GET 요청으로 수행됩니다. `List` 메서드의 `[HttpGet]` 특성은 이 작업이 GET 요청만을 처리해야 함을 나타냅니다. 이 작업에 대한 경로는 컨트롤러에 지정된 경로입니다. 경로의 일부로 작업 이름을 사용할 필요가 없습니다. 각 작업에 고유하고 명확한 경로가 있도록 하기만 하면 됩니다. 라우팅 특성은 특정 경로를 작성하도록 컨트롤러와 메서드 수준 모두에 적용될 수 있습니다.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

`List` 메서드는 200 정상 응답 코드와 JSON으로 직렬화된 모든 할 일 항목을 반환합니다.

여기에 표시된 [Postman](https://www.getpostman.com/docs/)과 같은 다양한 도구를 사용하여 새 API 메서드를 테스트할 수 있습니다.

![할 일 항목에 대한 GET 요청을 보여 주는 Postman 콘솔 및 반환되는 세 가지 항목에 대한 JSON을 보여 주는 응답의 본문](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>항목 만들기

일반적으로 새 데이터 항목 만들기는 HTTP POST 동사로 매핑됩니다. `Create` 메서드는 `[HttpPost]` 특성이 적용되어 있으며 `ToDoItem` 인스턴스를 허용합니다. `item` 인수는 POST 본문에 전달되기 때문에 이 매개 변수는 `[FromBody]` 특성을 지정합니다.

메서드 내부에서 유효성 및 데이터 저장소의 이전 존재에 대해 항목이 선택되며 문제가 발생하지 않는 경우 리포지토리를 사용하여 추가됩니다. `ModelState.IsValid`를 확인하면 [모델 유효성 검사](../mvc/models/validation.md)를 수행하고, 사용자 입력을 허용하는 모든 API 메서드에서 수행되어야 합니다.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

샘플은 모바일 클라이언트에 전달되는 오류 코드를 포함하는 열거형을 사용합니다.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

요청의 본문에 JSON 형식의 새 개체를 제공하는 POST 동사를 선택하여 Postman을 사용하는 새 항목 추가를 테스트합니다. 또한 `application/json`의 `Content-Type`을 지정하는 요청 헤더를 추가해야 합니다.

![POST 및 응답을 보여 주는 Postman 콘솔](native-mobile-backend/_static/postman-post.png)

메서드는 응답에서 새로 만든 항목을 반환합니다.

### <a name="updating-items"></a>항목 업데이트

레코드 수정은 HTTP PUT 요청을 사용하여 수행됩니다. 이 변경 외에 `Edit` 메서드는 `Create`와 거의 동일합니다. 레코드를 찾을 수 없는 경우 `Edit` 작업은 `NotFound`(404) 응답을 반환합니다.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

Postman으로 테스트하려면 동사를 PUT으로 변경합니다. 요청의 본문에서 업데이트된 개체 데이터를 지정합니다.

![PUT 및 응답을 보여 주는 Postman 콘솔](native-mobile-backend/_static/postman-put.png)

이 메서드는 성공한 경우 기존 API와의 일관성을 위해 `NoContent`(204) 응답을 반환합니다.

### <a name="deleting-items"></a>항목 삭제

레코드 삭제는 서비스에 대해 DELETE 요청을 만들고, 삭제될 항목의 ID를 전달하여 수행됩니다. 업데이트를 사용하면 존재하지 않는 항목에 대한 요청은 `NotFound` 응답을 수신합니다. 그렇지 않으면 성공한 요청은 `NoContent`(204) 응답을 받습니다.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

삭제 기능을 테스트할 때 요청의 본문에 아무 것도 필요하지 않습니다.

![DELETE 및 응답을 보여 주는 Postman 콘솔](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a>공통 Web API 규칙

앱에 대해 백 엔드 서비스를 개발하는 경우 교차 편집 문제 처리를 위해 일관적인 규칙의 집합 또는 정책을 찾을 수 있습니다. 예를 들어 위에 표시된 서비스에서 발견되지 않았던 특정 레코드에 대한 요청은 `BadRequest` 응답 대신 `NotFound` 응답을 받았습니다. 마찬가지로, 모델 바인딩 형식을 전달한 이 서비스에 대해 만든 명령은 항상 `ModelState.IsValid`를 확인했고 잘못된 모델 유형에 대해 `BadRequest`를 반환했습니다.

API에 대한 일반적인 정책을 식별했으면 [필터](../mvc/controllers/filters.md)에서 캡슐화할 수 있습니다. [ASP.NET Core MVC 애플리케이션에서 일반적인 API 정책을 캡슐화하는 방법](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters)에 대해 자세히 알아봅니다.

## <a name="additional-resources"></a>추가 자료

- [Xamarin.ios: 웹 서비스 인증](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin.ios: RESTful 웹 서비스 사용](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn: Xamarin 앱에서 REST 웹 서비스 사용](/learn/modules/consume-rest-services/)
- [Microsoft Learn: ASP.NET Core를 사용 하 여 web API 만들기](/learn/modules/build-web-api-aspnet-core/)
