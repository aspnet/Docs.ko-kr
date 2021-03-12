---
title: ASP.NET Core 웹 API에서 컨트롤러 작업 반환 형식
author: scottaddie
description: ASP.NET Core 웹 API에서 다양한 컨트롤러 작업 메서드 반환 형식을 사용하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
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
uid: web-api/action-return-types
ms.openlocfilehash: b377025a843f82a57fd0aac4961ddcedc67ff3b9
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589727"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>ASP.NET Core 웹 API에서 컨트롤러 작업 반환 형식

작성자: [Scott Addie](https://github.com/scottaddie)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/action-return-types/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

ASP.NET Core에서는 웹 API 컨트롤러 작업 반환 형식에 다음 옵션을 제공합니다.

::: moniker range=">= aspnetcore-2.1"

* [특정 형식](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [특정 형식](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

이 문서에서는 각 반환 형식을 사용하는 것이 가장 적합한 경우를 설명합니다.

## <a name="specific-type"></a>특정 형식

가장 간단한 작업은 기본 또는 복합 데이터 형식을 반환합니다(예: `string` 또는 사용자 지정 개체 형식). 다음 작업을 사용하여 사용자 지정 `Product` 개체의 컬렉션을 반환합니다.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

작업 실행 중에 보호할 알려진 조건 없이 특정 형식을 반환하는 것으로 충분합니다. 매개 변수 제약 조건 유효성 검사가 필요하지 않으므로 위의 작업은 매개 변수를 허용하지 않습니다.

여러 반환 형식을 사용할 수 있는 경우 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 반환 형식을 기본 또는 복합 반환 형식과 혼합 하는 것이 일반적입니다. 이 동작 유형을 수용 하려면 [iactionresult](#iactionresult-type) 또는 [ \<T> actionresult](#actionresultt-type) 가 필요 합니다. 이 문서에서는 여러 반환 형식에 대 한 몇 가지 샘플을 제공 합니다.

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>IEnumerable \<T> 또는 IAsyncEnumerable을 반환 합니다.\<T>

ASP.NET Core 2.2 이하 버전에서 작업에서 <xref:System.Collections.Generic.IEnumerable%601>를 반환하면 Serializer에 의한 동기 컬렉션 반복이 발생합니다. 그 결과는 호출 차단이며 스레드 풀 고갈의 가능성도 있습니다. 예를 들어 웹 API의 데이터 액세스 요구 사항에 대해 EF(Entity Framework) Core를 사용하고 있다고 가정합니다. 직렬화하는 동안 다음 작업의 반환 형식이 동기적으로 열거됩니다.

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

ASP.NET Core 2.2 이하 버전에서 데이터베이스에 대한 동기 열거 및 차단 대기를 방지하려면 `ToListAsync`를 호출합니다.

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

ASP.NET Core 3.0 이상에서 작업이 `IAsyncEnumerable<T>`를 반환할 경우:

* 더 이상 동기 반복이 발생하지 않습니다.
* <xref:System.Collections.Generic.IEnumerable%601>를 반환하는 것만큼 효율적입니다.

ASP.NET Core 3.0 이상에서는 Serializer에 제공하기 전에 다음 작업의 결과를 버퍼링합니다.

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

비동기 반복을 보장하기 위해 작업 서명의 반환 형식을 `IAsyncEnumerable<T>`로 선언합니다. 궁극적으로 반복 모드는 반환되는 기본 구체적 형식을 기반으로 합니다. MVC는 `IAsyncEnumerable<T>`를 구현하는 모든 구체적 형식을 자동으로 버퍼링합니다.

다음과 같은 판매 가격이 책정된 제품 레코드를 `IEnumerable<Product>`로 반환하는 작업을 살펴보겠습니다.

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.31/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

다음은 위 작업의 `IAsyncEnumerable<Product>`에 해당합니다.

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.31/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

위의 두 작업은 모두 ASP.NET Core 3.0 기준으로 비차단입니다.

## <a name="iactionresult-type"></a>IActionResult 형식

한 작업에 여러 `ActionResult` 반환 형식을 사용할 수 있는 경우 <xref:Microsoft.AspNetCore.Mvc.IActionResult> 반환 형식이 적절합니다. `ActionResult` 형식은 다양한 HTTP 상태 코드를 나타냅니다. `ActionResult`에서 파생되는 비추상 클래스는 유효한 반환 형식입니다. 이 범주에서 일반적인 반환 형식은 <xref:Microsoft.AspNetCore.Mvc.BadRequestResult>(400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult>(404) 및 <xref:Microsoft.AspNetCore.Mvc.OkObjectResult>(200)입니다. 또는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 클래스의 편의 메서드를 사용하여 작업에서 `ActionResult` 형식을 반환할 수 있습니다. 예를 들어 `return BadRequest();`는 `return new BadRequestResult();`의 약식 형태입니다.

이러한 유형의 작업에는 여러 반환 형식 및 경로가 있으므로 특성을 자유롭게 사용 [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) 해야 합니다. 이 특성은 [Swagger](xref:tutorials/web-api-help-pages-using-swagger)와 같은 도구에서 생성한 웹 API 도움말 페이지에 대한 설명이 포함된 응답 세부 정보를 생성합니다. `[ProducesResponseType]`은 작업에서 반환한 알려진 형식 및 HTTP 상태 코드을 나타냅니다.

### <a name="synchronous-action"></a>동기화 작업

두 가지 가능한 반환 형식이 포함된 다음과 같은 동기 작업을 사용합니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

위 작업에서:

* `id`에서 표시하는 제품이 내부 데이터 저장소에 없는 경우 404 상태 코드가 반환됩니다. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> 편의 메서드는 `return new NotFoundResult();`의 축약형으로 호출됩니다.
* 제품이 존재하는 경우 `Product` 개체와 함께 200 상태 코드가 반환됩니다. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> 편의 메서드는 `return new OkObjectResult(product);`의 축약형으로 호출됩니다.

### <a name="asynchronous-action"></a>비동기 작업

두 가지 가능한 반환 형식이 포함된 다음과 같은 비동기 작업을 사용합니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

위 작업에서:

* 제품 설명에 "XYZ 위젯"이 포함되어 있는 경우 400 상태 코드가 반환됩니다. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> 편의 메서드는 `return new BadRequestResult();`의 축약형으로 호출됩니다.
* 제품이 만들어지면 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 편의 메서드가 201 상태 코드를 생성합니다. `CreatedAtAction`을 호출하는 대신 `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`를 호출할 수 있습니다. 이 코드 경로에서는 `Product` 개체가 응답 본문에 제공됩니다. 새로 만든 제품의 URL을 포함하는 `Location` 응답 헤더가 제공됩니다.

예를 들어 다음 모델은 요청이 `Name` 및 `Description` 속성을 포함해야 함을 나타냅니다. 요청에서 `Name` 및 `Description`을 제공하지 못하면 모델 유효성 검사에 실패합니다.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)ASP.NET Core 2.1 이상의 특성이 적용 되는 경우 모델 유효성 검사 오류는 400 상태 코드를 생성 합니다. 자세한 정보는 [자동 HTTP 400 응답](xref:web-api/index#automatic-http-400-responses)을 참조하세요.

## <a name="actionresultt-type"></a>ActionResult \<T> 형식

ASP.NET Core 2.1에는 web API 컨트롤러 작업에 대 한 [Actionresult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) 반환 형식이 도입 되었습니다. 이를 통해 <xref:Microsoft.AspNetCore.Mvc.ActionResult>에서 파생된 형식을 반환하거나 [특정 형식](#specific-type)을 반환할 수 있습니다. `ActionResult<T>`는 [IActionResult 형식](#iactionresult-type)을 통해 다음과 같은 혜택을 제공합니다.

* [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute)특성의 `Type` 속성을 제외할 수 있습니다. 예를 들어 `[ProducesResponseType(200, Type = typeof(Product))]`은 `[ProducesResponseType(200)]`으로 단순화됩니다. 작업의 예상 반환 형식은 대신 `ActionResult<T>`의 `T`에서 유추됩니다.
* [암시적 캐스트 연산자](/dotnet/csharp/language-reference/keywords/implicit)는 `T` 및 `ActionResult` 모두를 `ActionResult<T>`로 변환하도록 지원합니다. `T`는 <xref:Microsoft.AspNetCore.Mvc.ObjectResult>로 변환합니다. 즉, `return new ObjectResult(T);`는 `return T;`로 간소화됩니다.

C#은 인터페이스에서 암시적 캐스트 연산자를 지원하지 않습니다. 따라서 인터페이스를 구체적인 형식으로 전환하려면 `ActionResult<T>`를 사용해야 합니다. 예를 들어 다음 예제에서 `IEnumerable`을 사용하면 작동하지 않습니다.

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

이전 코드를 수정하는 한 가지 옵션으로 `_repository.GetProducts().ToList();`를 반환해 볼 수 있습니다.

대부분의 작업에는 특정 반환 형식이 있습니다. 작업을 실행하는 동안 예기치 않은 조건이 발생할 수 있습니다. 이 경우에 특정 형식이 반환되지 않습니다. 예를 들어 작업의 입력 매개 변수는 모델 유효성 검사에 실패할 수 있습니다. 이러한 경우에 일반적으로 특정 형식이 아닌 적절한 `ActionResult` 형식을 반환합니다.

### <a name="synchronous-action"></a>동기화 작업

두 가지 가능한 반환 형식이 포함된 동기 작업을 사용합니다.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

위 작업에서:

* 제품이 데이터베이스에 존재하지 않는 경우 404 상태 코드가 반환됩니다.
* 제품이 존재하는 경우 해당 `Product` 개체와 함께 200 상태 코드가 반환됩니다. ASP.NET Core 2.1 이전에는 `return product;` 줄이 `return Ok(product);`이어야 했습니다.

### <a name="asynchronous-action"></a>비동기 작업

두 가지 가능한 반환 형식이 포함된 비동기 작업을 사용합니다.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

위 작업에서:

* 다음과 같은 경우 ASP.NET Core 런타임에서 400 상태 코드(<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>)가 반환됩니다.
  * [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)특성이 적용 되었으며 모델 유효성 검사가 실패 합니다.
  * 제품 설명에 “XYZ 위젯”이 포함됩니다.
* 제품이 만들어지면 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드가 201 상태 코드를 생성합니다. 이 코드 경로에서는 `Product` 개체가 응답 본문에 제공됩니다. 새로 만든 제품의 URL을 포함하는 `Location` 응답 헤더가 제공됩니다.

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
