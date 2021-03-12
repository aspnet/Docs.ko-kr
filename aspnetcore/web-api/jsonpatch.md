---
title: ASP.NET Core Web API의 JsonPatch
author: rick-anderson
description: ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: 4ed44a0fca9e0834a78e433cdd48cbd153c58666
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587855"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>ASP.NET Core Web API의 JsonPatch

작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Kirk Larkin](https://github.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

이 문서에서는 ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 설명합니다.

## <a name="package-installation"></a>패키지 설치

앱에서 JSON 패치 지원을 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/)NuGet 패키지를 설치 합니다.
1. 프로젝트의 메서드를 업데이트 `Startup.ConfigureServices` 하 여를 호출 <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> 합니다. 예를 들면 다음과 같습니다.

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

`AddNewtonsoftJson`은 MVC 서비스 등록 메서드

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a>JSON 패치, AddNewtonsoftJson 및 System.Text.Js

`AddNewtonsoftJson``System.Text.Json` **모든** JSON 콘텐츠의 서식을 지정 하는 데 사용 되는 기반 입력 및 출력 포맷터를 바꿉니다. 를 사용 하 여 JSON 패치에 대 한 지원을 추가 하 고 `Newtonsoft.Json` 다른 포맷터는 변경 하지 않은 채 프로젝트의 메서드를 다음과 같이 업데이트 합니다 `Startup.ConfigureServices` .

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

위의 코드에는 `Microsoft.AspNetCore.Mvc.NewtonsoftJson` 패키지와 다음 문이 필요 합니다 `using` .

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a>PATCH HTTP 요청 메서드

PUT 및 [PATCH](https://tools.ietf.org/html/rfc5789) 메서드는 기존 리소스를 업데이트하는 데 사용됩니다. 두 메서드의 차이점은 PUT은 전체 리소스를 바꾸지만, PATCH는 변경 내용만 지정한다는 것입니다.

## <a name="json-patch"></a>JSON 패치

[JSON 패치](https://tools.ietf.org/html/rfc6902)는 리소스에 적용할 업데이트를 지정하기 위한 형식입니다. JSON 패치 문서에는 작업 배열이 포함됩니다. 각 작업은 특정 유형의 변경 내용을 식별 합니다. 이러한 변경의 예로는 배열 요소 추가 또는 속성 값 바꾸기가 있습니다.

예를 들어, 다음 JSON 문서는 리소스, 리소스에 대 한 JSON 패치 문서 및 패치 작업 적용 결과를 나타냅니다.

### <a name="resource-example"></a>리소스 예제

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>JSON 패치 예제

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

앞의 JSON에서:

* `op` 속성은 작업 형식을 나타냅니다.
* `path` 속성은 업데이트할 요소를 나타냅니다.
* `value` 속성은 새 값을 제공합니다.

### <a name="resource-after-patch"></a>패치 후 리소스

앞의 JSON 패치 문서를 적용한 후 리소스는 다음과 같습니다.

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

JSON 패치 문서를 리소스에 적용 하 여 변경한 내용은 원자성입니다. 목록에서 작업이 실패 하면 목록에 작업이 적용 되지 않습니다.

## <a name="path-syntax"></a>경로 구문

작업 개체의 [path](https://tools.ietf.org/html/rfc6901) 속성에서 수준 사이에는 슬래시가 있습니다. 예들 들어 `"/address/zipCode"`입니다.

0부터 시작하는 인덱스는 배열 요소를 지정하는 데 사용됩니다. `addresses` 배열의 첫 번째 요소는 `/addresses/0`에 있습니다. 배열의 끝에 대 한 `add` `-` 인덱스 번호 대신 하이픈 ()을 사용 `/addresses/-` 합니다.

### <a name="operations"></a>작업

다음 표에서는 [JSON 패치 사양](https://tools.ietf.org/html/rfc6902)에 정의된 지원되는 작업을 보여 줍니다.

|작업(Operation)  | 참고 |
|-----------|--------------------------------|
| `add`     | 속성 또는 배열 요소를 추가합니다. 기존 속성의 경우 값을 설정합니다.|
| `remove`  | 속성 또는 배열 요소를 제거합니다. |
| `replace` | 동일한 위치에서 `add`가 뒤에 오는 `remove`와 같습니다. |
| `move`    | 소스의 값을 사용하는 대상에 대한 `add`가 뒤에 오는 소스에서 `remove`와 같습니다. |
| `copy`    | 소스의 값을 사용하는 대상에 대한 `add`와 같습니다. |
| `test`    | `path`의 값이 제공된 `value`와 같은 경우 성공 상태 코드를 반환합니다.|

## <a name="json-patch-in-aspnet-core"></a>ASP.NET Core의 JSON 패치

JSON 패치의 ASP.NET Core 구현은 [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 패키지로 제공됩니다.

## <a name="action-method-code"></a>작업 메서드 코드

API 컨트롤러에서 JSON 패치의 작업 메서드는 다음과 같습니다.

* `HttpPatch` 특성을 사용하여 주석으로 처리됩니다.
* 일반적으로 `[FromBody]`를 사용하여 `JsonPatchDocument<T>`를 수락합니다.
* 패치 문서에서 `ApplyTo`를 호출하여 변경 내용을 적용합니다.

예를 들면 다음과 같습니다.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

샘플 앱의이 코드는 다음 모델과 함께 작동 합니다 `Customer` .

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

샘플 작업 메서드:

* `Customer`를 생성합니다.
* 패치를 적용합니다.
* 응답의 본문으로 결과를 반환합니다.

실제 앱에서 이 코드는 데이터베이스와 같은 저장소에서 데이터를 검색하고 패치를 적용한 후 데이터베이스를 업데이트합니다.

### <a name="model-state"></a>모델 상태

앞의 작업 메서드 예제에서는 모델 상태를 매개 변수 중 하나로 가져오는 `ApplyTo`의 오버로드를 호출합니다. 이 옵션을 사용하여 응답으로 오류 메시지를 가져올 수 있습니다. 다음 예제에서는 `test` 작업에 대한 400 잘못된 요청 응답의 본문을 보여 줍니다.

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>동적 개체

다음 동작 메서드 예에서는 동적 개체에 패치를 적용 하는 방법을 보여 줍니다.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>추가 작업

* `path`가 배열 요소를 가리키는 경우: `path`에 지정된 요소 앞에 새 요소를 삽입합니다.
* `path`가 속성을 가리키는 경우: 속성 값을 설정합니다.
* `path`가 존재하지 않는 위치를 가리키는 경우:
  * 패치할 리소스가 동적 개체인 경우: 속성을 추가합니다.
  * 패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.

다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Order` 개체를 `Orders` 배열 끝에 추가합니다.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>제거 작업

* `path`가 배열 요소를 가리키는 경우: 요소를 제거합니다.
* `path`가 속성을 가리키는 경우:
  * 패치할 리소스가 동적 개체인 경우: 속성을 제거합니다.
  * 패치할 리소스가 정적 개체인 경우:
    * 속성이 nullable인 경우: null로 설정합니다.
    * 속성이 nullable이 아닌 경우: `default<T>`로 설정합니다.

다음 샘플 패치 문서는 `CustomerName` 를 null로 설정 하 고 삭제 합니다 `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>바꾸기 작업

이 작업은 `add`가 뒤에 오는 `remove`와 기능적으로 동일합니다.

다음 샘플 패치 문서는의 값을 설정 `CustomerName` 하 고를 `Orders[0]` 새 `Order` 개체로 바꿉니다.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>이동 작업

* `path`가 배열 요소를 가리키는 경우: `from` 요소를 `path` 요소 위치에 복사한 후 `from` 요소에서 `remove` 작업을 실행합니다.
* `path`가 속성을 가리키는 경우: `from` 속성 값을 `path` 속성에 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.
* `path`가 존재하지 않는 속성을 가리키는 경우:
  * 패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.
  * 패치할 리소스가 동적 개체인 경우: `from` 속성을 `path`에 지정된 위치로 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.

다음 샘플 패치 문서는 다음을 수행합니다.

* `Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.
* `Orders[0].OrderName`을 null로 설정합니다.
* `Orders[1]`를 `Orders[0]` 앞으로 이동합니다.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>복사 작업

이 작업은 마지막 `remove` 단계 없이 `move` 작업과 기능적으로 동일합니다.

다음 샘플 패치 문서는 다음을 수행합니다.

* `Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.
* `Orders[1]` 복사본을 `Orders[0]` 앞에 삽입합니다.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>테스트 작업

`path`에 지정된 위치의 값이 `value`에 제공된 값과 다른 경우 요청이 실패합니다. 이 경우 패치 문서의 다른 모든 작업이 성공하더라도 전체 PATCH 요청이 실패합니다.

`test` 작업은 일반적으로 동시성 충돌이 발생하는 경우 업데이트를 방지하는 데 사용됩니다.

`CustomerName`의 초기 값이 “John”인 경우 테스트에 실패하므로 다음 샘플 패치 문서는 영향을 주지 않습니다.

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>코드 가져오기

[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples) ([다운로드하는 방법](xref:index#how-to-download-a-sample))

샘플을 테스트하려면 앱을 실행하고 다음 설정을 사용하여 HTTP 요청을 보냅니다.

* URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* HTTP 메서드: `PATCH`
* 헤더: `Content-Type: application/json-patch+json`
* Body: *json 프로젝트 폴더에서 json 패치* 문서 샘플 중 하나를 복사 하 여 붙여넣습니다.

## <a name="additional-resources"></a>추가 리소스

* [IETF RFC 5789 PATCH 메서드 사양](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 JSON 패치 사양](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON 패치 경로 형식 사양](https://tools.ietf.org/html/rfc6901)
* [JSON 패치 문서](https://jsonpatch.com/). JSON 패치 문서를 만들기 위한 리소스의 링크를 포함합니다.
* [ASP.NET Core JSON 패치 소스 코드](https://github.com/dotnet/AspNetCore/tree/main/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 문서에서는 ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 설명합니다.

## <a name="patch-http-request-method"></a>PATCH HTTP 요청 메서드

PUT 및 [PATCH](https://tools.ietf.org/html/rfc5789) 메서드는 기존 리소스를 업데이트하는 데 사용됩니다. 두 메서드의 차이점은 PUT은 전체 리소스를 바꾸지만, PATCH는 변경 내용만 지정한다는 것입니다.

## <a name="json-patch"></a>JSON 패치

[JSON 패치](https://tools.ietf.org/html/rfc6902)는 리소스에 적용할 업데이트를 지정하기 위한 형식입니다. JSON 패치 문서에는 작업 배열이 포함됩니다. 각 작업은 배열 요소 추가 또는 속성 값 바꾸기와 같은 특정 유형의 변경을 식별합니다.

예를 들어 다음 JSON 문서는 리소스, 리소스의 JSON 패치 문서 및 패치 작업을 적용한 결과를 나타냅니다.

### <a name="resource-example"></a>리소스 예제

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>JSON 패치 예제

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

앞의 JSON에서:

* `op` 속성은 작업 형식을 나타냅니다.
* `path` 속성은 업데이트할 요소를 나타냅니다.
* `value` 속성은 새 값을 제공합니다.

### <a name="resource-after-patch"></a>패치 후 리소스

앞의 JSON 패치 문서를 적용한 후 리소스는 다음과 같습니다.

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

JSON 패치 문서를 리소스에 적용하여 변경된 내용은 원자성입니다. 목록에 있는 작업이 하나라도 실패하면 목록의 작업이 적용되지 않습니다.

## <a name="path-syntax"></a>경로 구문

작업 개체의 [path](https://tools.ietf.org/html/rfc6901) 속성에서 수준 사이에는 슬래시가 있습니다. 예들 들어 `"/address/zipCode"`입니다.

0부터 시작하는 인덱스는 배열 요소를 지정하는 데 사용됩니다. `addresses` 배열의 첫 번째 요소는 `/addresses/0`에 있습니다. 배열 끝에 `add`(추가)하려면 인덱스 번호가 아닌 하이픈(-)을 사용합니다(`/addresses/-`).

### <a name="operations"></a>작업

다음 표에서는 [JSON 패치 사양](https://tools.ietf.org/html/rfc6902)에 정의된 지원되는 작업을 보여 줍니다.

|작업(Operation)  | 참고 |
|-----------|--------------------------------|
| `add`     | 속성 또는 배열 요소를 추가합니다. 기존 속성의 경우 값을 설정합니다.|
| `remove`  | 속성 또는 배열 요소를 제거합니다. |
| `replace` | 동일한 위치에서 `add`가 뒤에 오는 `remove`와 같습니다. |
| `move`    | 소스의 값을 사용하는 대상에 대한 `add`가 뒤에 오는 소스에서 `remove`와 같습니다. |
| `copy`    | 소스의 값을 사용하는 대상에 대한 `add`와 같습니다. |
| `test`    | `path`의 값이 제공된 `value`와 같은 경우 성공 상태 코드를 반환합니다.|

## <a name="jsonpatch-in-aspnet-core"></a>ASP.NET Core의 JsonPatch

JSON 패치의 ASP.NET Core 구현은 [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 패키지로 제공됩니다. 패키지는 [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) 메타패키지에 포함됩니다.

## <a name="action-method-code"></a>작업 메서드 코드

API 컨트롤러에서 JSON 패치의 작업 메서드는 다음과 같습니다.

* `HttpPatch` 특성을 사용하여 주석으로 처리됩니다.
* 일반적으로 `[FromBody]`를 사용하여 `JsonPatchDocument<T>`를 수락합니다.
* 패치 문서에서 `ApplyTo`를 호출하여 변경 내용을 적용합니다.

예를 들면 다음과 같습니다.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

샘플 앱의 이 코드는 다음 `Customer` 모델에서 작동합니다.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

샘플 작업 메서드:

* `Customer`를 생성합니다.
* 패치를 적용합니다.
* 응답의 본문으로 결과를 반환합니다.

 실제 앱에서 이 코드는 데이터베이스와 같은 저장소에서 데이터를 검색하고 패치를 적용한 후 데이터베이스를 업데이트합니다.

### <a name="model-state"></a>모델 상태

앞의 작업 메서드 예제에서는 모델 상태를 매개 변수 중 하나로 가져오는 `ApplyTo`의 오버로드를 호출합니다. 이 옵션을 사용하여 응답으로 오류 메시지를 가져올 수 있습니다. 다음 예제에서는 `test` 작업에 대한 400 잘못된 요청 응답의 본문을 보여 줍니다.

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>동적 개체

다음 작업 메서드 예제에서는 동적 개체에 패치를 적용하는 방법을 보여 줍니다.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>추가 작업

* `path`가 배열 요소를 가리키는 경우: `path`에 지정된 요소 앞에 새 요소를 삽입합니다.
* `path`가 속성을 가리키는 경우: 속성 값을 설정합니다.
* `path`가 존재하지 않는 위치를 가리키는 경우:
  * 패치할 리소스가 동적 개체인 경우: 속성을 추가합니다.
  * 패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.

다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Order` 개체를 `Orders` 배열 끝에 추가합니다.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>제거 작업

* `path`가 배열 요소를 가리키는 경우: 요소를 제거합니다.
* `path`가 속성을 가리키는 경우:
  * 패치할 리소스가 동적 개체인 경우: 속성을 제거합니다.
  * 패치할 리소스가 정적 개체인 경우:
    * 속성이 nullable인 경우: null로 설정합니다.
    * 속성이 nullable이 아닌 경우: `default<T>`로 설정합니다.

다음 샘플 패치 문서는 `CustomerName`을 null로 설정하고 `Orders[0]`를 삭제합니다.

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>바꾸기 작업

이 작업은 `add`가 뒤에 오는 `remove`와 기능적으로 동일합니다.

다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Orders[0]`를 새 `Order` 개체로 바꿉니다.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>이동 작업

* `path`가 배열 요소를 가리키는 경우: `from` 요소를 `path` 요소 위치에 복사한 후 `from` 요소에서 `remove` 작업을 실행합니다.
* `path`가 속성을 가리키는 경우: `from` 속성 값을 `path` 속성에 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.
* `path`가 존재하지 않는 속성을 가리키는 경우:
  * 패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.
  * 패치할 리소스가 동적 개체인 경우: `from` 속성을 `path`에 지정된 위치로 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.

다음 샘플 패치 문서는 다음을 수행합니다.

* `Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.
* `Orders[0].OrderName`을 null로 설정합니다.
* `Orders[1]`를 `Orders[0]` 앞으로 이동합니다.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>복사 작업

이 작업은 마지막 `remove` 단계 없이 `move` 작업과 기능적으로 동일합니다.

다음 샘플 패치 문서는 다음을 수행합니다.

* `Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.
* `Orders[1]` 복사본을 `Orders[0]` 앞에 삽입합니다.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>테스트 작업

`path`에 지정된 위치의 값이 `value`에 제공된 값과 다른 경우 요청이 실패합니다. 이 경우 패치 문서의 다른 모든 작업이 성공하더라도 전체 PATCH 요청이 실패합니다.

`test` 작업은 일반적으로 동시성 충돌이 발생하는 경우 업데이트를 방지하는 데 사용됩니다.

`CustomerName`의 초기 값이 “John”인 경우 테스트에 실패하므로 다음 샘플 패치 문서는 영향을 주지 않습니다.

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>코드 가져오기

[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples/2.2) ([다운로드하는 방법](xref:index#how-to-download-a-sample))

샘플을 테스트하려면 앱을 실행하고 다음 설정을 사용하여 HTTP 요청을 보냅니다.

* URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* HTTP 메서드: `PATCH`
* 헤더: `Content-Type: application/json-patch+json`
* Body: *json 프로젝트 폴더에서 json 패치* 문서 샘플 중 하나를 복사 하 여 붙여넣습니다.

## <a name="additional-resources"></a>추가 리소스

* [IETF RFC 5789 PATCH 메서드 사양](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 JSON 패치 사양](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON 패치 경로 형식 사양](https://tools.ietf.org/html/rfc6901)
* [JSON 패치 문서](https://jsonpatch.com/). JSON 패치 문서를 만들기 위한 리소스의 링크를 포함합니다.
* [ASP.NET Core JSON 패치 소스 코드](https://github.com/dotnet/AspNetCore/tree/main/src/Features/JsonPatch/src)

::: moniker-end
