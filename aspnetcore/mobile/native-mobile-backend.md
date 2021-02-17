---
title: ASP.NET Core를 사용하여 네이티브 모바일 앱용 백 엔드 서비스 만들기
author: rick-anderson
description: ASP.NET Core MVC를 사용하여 네이티브 모바일 앱을 지원하는 백 엔드 서비스를 만드는 방법을 배웁니다.
ms.author: riande
ms.date: 2/12/2021
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
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564042"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>ASP.NET Core를 사용하여 네이티브 모바일 앱용 백 엔드 서비스 만들기

별 [Montemagno](https://twitter.com/JamesMontemagno)

모바일 앱은 ASP.NET Core 백 엔드 서비스와 통신할 수 있습니다. iOS 시뮬레이터 및 Android 에뮬레이터에서 로컬 웹 서비스를 연결하는 방법에 대한 지침은 [iOS 시뮬레이터 및 Android 에뮬레이터에서 로컬 웹 서비스에 연결](/xamarin/cross-platform/deploy-test/connect-to-local-web-services)을 참조하세요.

[샘플 백 엔드 서비스 코드 보기 및 다운로드](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a>샘플 네이티브 모바일 앱

이 자습서에서는 기본 모바일 앱을 지원 하기 위해 ASP.NET Core를 사용 하 여 백 엔드 서비스를 만드는 방법을 보여 줍니다. 이 도구는 Android, iOS 및 Windows 용 개별 native client를 포함 하는 네이티브 클라이언트로 [Xamarin.ios TodoRest 앱](/xamarin/xamarin-forms/data-cloud/consuming/rest) 을 사용 합니다. 연결된 자습서를 따라 네이티브 앱을 만들고(필요한 무료 Xamarin 도구 설치) Xamarin 샘플 솔루션을 다운로드할 수 있습니다. Xamarin 샘플에는 ASP.NET Core Web API services 프로젝트가 포함 되어 있습니다 .이 프로젝트는이 문서의 ASP.NET Core 앱이 대체 합니다 (클라이언트에서 변경 하지 않아도).

![Android 스마트폰에서 실행되는 To Do Rest 애플리케이션](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>기능

[TodoREST 앱](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) 은 To-Do 항목의 나열, 추가, 삭제 및 업데이트를 지원 합니다. 각 항목에는 ID, 이름, 메모 및 완료되었는지 여부를 나타내는 속성이 있습니다.

위에 표시된 것처럼 항목의 주 보기는 각 항목의 이름을 나열하고 확인 표시로 완료되었는지를 나타냅니다.

`+` 아이콘을 누르면 항목 추가 대화 상자를 엽니다.

![항목 추가 대화 상자](native-mobile-backend/_static/todo-android-new-item.png)

기본 목록 화면에서 항목을 누르면 항목의 이름, 메모 및 완료 설정을 수정할 수 있거나 항목을 삭제할 수 있는 편집 대화 상자를 엽니다.

![항목 편집 대화 상자](native-mobile-backend/_static/todo-android-edit-item.png)

컴퓨터에서 실행 되는 다음 섹션에서 만든 ASP.NET Core 앱에 대해 스스로 테스트 하려면 앱의 상수를 업데이트 [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) 합니다.

Android 에뮬레이터는 로컬 컴퓨터에서 실행 되지 않으며 루프백 IP (10.0.2.2)를 사용 하 여 로컬 컴퓨터와 통신 합니다. [Xamarin.ios DeviceInfo](/xamarin/essentials/device-information/) 를 활용 하 여 올바른 URL을 사용 하기 위해 시스템이 실행 중인 운영 체제를 검색 합니다.

[`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST)프로젝트로 이동 하 여 파일을 엽니다 [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) . *Constants.cs* 파일에는 다음 구성이 포함 됩니다.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

필요에 따라 Azure와 같은 클라우드 서비스에 웹 서비스를 배포 하 고을 업데이트할 수 있습니다 `RestUrl` .

## <a name="creating-the-aspnet-core-project"></a>ASP.NET Core 프로젝트 만들기

Visual Studio에서 새 ASP.NET Core 웹 애플리케이션을 만듭니다. Web API 템플릿을 선택 합니다. 프로젝트 이름을 *TodoAPI* 로 합니다.

![Web API 프로젝트 템플릿이 선택된 새 ASP.NET 웹 애플리케이션 대화 상자](native-mobile-backend/_static/web-api-template.png)

앱은 모바일 클라이언트에 대 한 일반 텍스트 http 트래픽을 포함 하 여 포트 5000에 대 한 모든 요청에 응답 해야 합니다. 업데이트 *Startup.cs* 는 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> 개발 중에 실행 되지 않습니다.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> IIS Express 아닌 앱을 직접 실행 합니다. IIS Express는 기본적으로 로컬이 아닌 요청을 무시 합니다. 명령 프롬프트에서 [dotnet run](/dotnet/core/tools/dotnet-run) 을 실행 하거나 Visual Studio 도구 모음의 디버그 대상 드롭다운에서 앱 이름 프로필을 선택 합니다.

할 일 항목을 나타내도록 모델 클래스를 추가합니다. `[Required]` 특성으로 필수 필드를 표시합니다.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

API 메서드에는 데이터를 사용하는 방법이 필요합니다. 원래 Xamarin 샘플에서 사용하는 동일한 `ITodoRepository` 인터페이스를 사용합니다.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

이 샘플의 경우 구현은 항목의 개별 컬렉션을 사용합니다.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

*Startup.cs* 에서 구현을 구성합니다.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a>컨트롤러 만들기

[TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs)프로젝트에 새 컨트롤러를 추가 합니다. 에서 상속 되어야 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 합니다. `Route` 특성을 추가하여 컨트롤러에서 `api/todoitems`로 시작하는 경로에 대해 만든 요청을 처리하는 것을 나타냅니다. 경로의 `[controller]` 토큰은 컨트롤러의 이름으로 대체되며(`Controller` 접미사 생략) 글로벌 경로에 대해 특히 유용합니다. [라우팅](../fundamentals/routing.md)에 대해 자세히 알아봅니다.

컨트롤러는 작동하기 위해 `ITodoRepository`가 필요합니다. 컨트롤러의 생성자를 통해 이 유형의 인스턴스를 요청합니다. 런타임 시 이 인스턴스는 [종속성 주입](../fundamentals/dependency-injection.md)에 대한 프레임워크의 지원을 사용하여 제공됩니다.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

이 API는 데이터 원본에서 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 수행하도록 4개의 다른 HTTP 동사를 지원합니다. 이 중 가장 간단한 것은 HTTP GET 요청에 해당하는 읽기 작업입니다.

### <a name="reading-items"></a>항목 읽기

항목의 목록 요청은 `List` 메서드에 대한 GET 요청으로 수행됩니다. `List` 메서드의 `[HttpGet]` 특성은 이 작업이 GET 요청만을 처리해야 함을 나타냅니다. 이 작업에 대한 경로는 컨트롤러에 지정된 경로입니다. 경로의 일부로 작업 이름을 사용할 필요가 없습니다. 각 작업에 고유하고 명확한 경로가 있도록 하기만 하면 됩니다. 라우팅 특성은 특정 경로를 작성하도록 컨트롤러와 메서드 수준 모두에 적용될 수 있습니다.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

`List`메서드는 JSON으로 serialize 된 모든 Todo 항목 및 200 OK 응답 코드를 반환 합니다.

여기에 표시된 [Postman](https://www.getpostman.com/docs/)과 같은 다양한 도구를 사용하여 새 API 메서드를 테스트할 수 있습니다.

![할 일 항목에 대한 GET 요청을 보여 주는 Postman 콘솔 및 반환되는 세 가지 항목에 대한 JSON을 보여 주는 응답의 본문](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>항목 만들기

일반적으로 새 데이터 항목 만들기는 HTTP POST 동사로 매핑됩니다. `Create` 메서드는 `[HttpPost]` 특성이 적용되어 있으며 `TodoItem` 인스턴스를 허용합니다. `item` 인수는 POST 본문에 전달되기 때문에 이 매개 변수는 `[FromBody]` 특성을 지정합니다.

메서드 내부에서 유효성 및 데이터 저장소의 이전 존재에 대해 항목이 선택되며 문제가 발생하지 않는 경우 리포지토리를 사용하여 추가됩니다. `ModelState.IsValid`를 확인하면 [모델 유효성 검사](../mvc/models/validation.md)를 수행하고, 사용자 입력을 허용하는 모든 API 메서드에서 수행되어야 합니다.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

이 샘플에서는 `enum` 모바일 클라이언트에 전달 되는 오류 코드를 포함 하는를 사용 합니다.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

요청의 본문에 JSON 형식의 새 개체를 제공하는 POST 동사를 선택하여 Postman을 사용하는 새 항목 추가를 테스트합니다. 또한 `application/json`의 `Content-Type`을 지정하는 요청 헤더를 추가해야 합니다.

![POST 및 응답을 보여 주는 Postman 콘솔](native-mobile-backend/_static/postman-post.png)

메서드는 응답에서 새로 만든 항목을 반환합니다.

### <a name="updating-items"></a>항목 업데이트

레코드 수정은 HTTP PUT 요청을 사용하여 수행됩니다. 이 변경 외에 `Edit` 메서드는 `Create`와 거의 동일합니다. 레코드를 찾을 수 없는 경우 `Edit` 작업은 `NotFound`(404) 응답을 반환합니다.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

Postman으로 테스트하려면 동사를 PUT으로 변경합니다. 요청의 본문에서 업데이트된 개체 데이터를 지정합니다.

![PUT 및 응답을 보여 주는 Postman 콘솔](native-mobile-backend/_static/postman-put.png)

이 메서드는 성공한 경우 기존 API와의 일관성을 위해 `NoContent`(204) 응답을 반환합니다.

### <a name="deleting-items"></a>항목 삭제

레코드 삭제는 서비스에 대해 DELETE 요청을 만들고, 삭제될 항목의 ID를 전달하여 수행됩니다. 업데이트를 사용하면 존재하지 않는 항목에 대한 요청은 `NotFound` 응답을 수신합니다. 그렇지 않으면 성공한 요청은 `NoContent`(204) 응답을 받습니다.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

삭제 기능을 테스트할 때 요청의 본문에 아무 것도 필요하지 않습니다.

![DELETE 및 응답을 보여 주는 Postman 콘솔](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a>과도한 게시 방지

현재 샘플 앱은 전체 `TodoItem` 개체를 공개합니다. 일반적으로 프로덕션 앱은 모델의 하위 집합을 사용하여 입력 및 반환되는 데이터를 제한합니다. 이 동작에는 여러 가지 이유가 있으며, 보안이 주요 이유 중 하나입니다. 일반적으로 모델의 하위 집합을 DTO(데이터 전송 개체), 입력 모델 또는 뷰 모델이라고 합니다. 이 문서에서는 **DTO** 를 사용합니다.

DTO는 다음과 같은 용도로 사용할 수 있습니다.

* 과도한 게시를 방지합니다.
* 클라이언트에 표시되지 않아야 하는 속성을 숨깁니다.
* 페이로드 크기를 줄이기 위해 일부 속성을 생략합니다.
* 중첩된 개체를 포함하는 개체 그래프를 평면화합니다. 클라이언트에는 평면화된 개체 그래프가 더 편리할 수 있습니다.

DTO 접근 방식을 설명 하려면 [초과 게시 방지](xref:tutorials/first-web-api#prevent-over-posting) 를 참조 하세요.

## <a name="common-web-api-conventions"></a>공통 Web API 규칙

앱에 대해 백 엔드 서비스를 개발하는 경우 교차 편집 문제 처리를 위해 일관적인 규칙의 집합 또는 정책을 찾을 수 있습니다. 예를 들어 위에 표시된 서비스에서 발견되지 않았던 특정 레코드에 대한 요청은 `BadRequest` 응답 대신 `NotFound` 응답을 받았습니다. 마찬가지로, 모델 바인딩 형식을 전달한 이 서비스에 대해 만든 명령은 항상 `ModelState.IsValid`를 확인했고 잘못된 모델 유형에 대해 `BadRequest`를 반환했습니다.

API에 대한 일반적인 정책을 식별했으면 [필터](../mvc/controllers/filters.md)에서 캡슐화할 수 있습니다. [ASP.NET Core MVC 애플리케이션에서 일반적인 API 정책을 캡슐화하는 방법](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters)에 대해 자세히 알아봅니다.

## <a name="additional-resources"></a>추가 자료

- [Xamarin.ios: 웹 서비스 인증](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin.ios: RESTful 웹 서비스 사용](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn: Xamarin 앱에서 REST 웹 서비스 사용](/learn/modules/consume-rest-services/)
- [Microsoft Learn: ASP.NET Core를 사용하여 웹 API 만들기](/learn/modules/build-web-api-aspnet-core/)
