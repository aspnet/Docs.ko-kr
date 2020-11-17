---
title: ASP.NET Core에서 필터링
author: Rick-Anderson
description: 필터 작동 방법 및 ASP.NET Core에서 사용하는 방법을 자세히 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: d075faa951a34fb3856b54eb9e21593b6616b4f1
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673967"
---
# <a name="filters-in-aspnet-core"></a>ASP.NET Core에서 필터링

::: moniker range=">= aspnetcore-3.0"

작성자: [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)

ASP.NET Core에서 *필터* 를 사용하면 요청 처리 파이프라인의 특정 단계 전후에 코드를 실행할 수 있습니다.

기본 제공 필터는 다음과 같은 작업을 처리합니다.

* 권한 부여(사용자가 권한이 없는 리소스에 액세스 방지).
* 응답 캐싱(요청 파이프라인을 단락하면 캐시된 응답을 반환합니다).

사용자 지정 필터를 만들어 교차 편집 문제를 처리할 수 있습니다. 횡단 관심사의 사례로는 오류 처리, 캐싱, 구성, 권한 부여 및 로깅을 들 수 있습니다.  필터는 코드 중복을 방지합니다. 예를 들어 오류 처리 예외 필터는 오류 처리를 통합할 수 있습니다.

이 문서 Razor 는 뷰가 있는 페이지, API 컨트롤러 및 컨트롤러에 적용 됩니다. 필터는 [ Razor 구성 요소](xref:blazor/components/index)와 직접 작동 하지 않습니다. 필터는 다음과 같은 경우에만 간접적으로 구성 요소에 영향을 줄 수 있습니다.

* 구성 요소가 페이지 또는 보기에 포함되어 있는 경우
* 페이지 또는 컨트롤러/보기에서 필터를 사용하는 경우

[예제 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="how-filters-work"></a>필터 작동 방법

필터는 ‘필터 파이프라인’이라고도 하는 ‘ASP.NET Core 동작 호출 파이프라인’내에서 실행됩니다. 필터 파이프라인은 ASP.NET Core가 실행할 작업을 선택한 후에 실행됩니다.

![다른 미들웨어, 라우팅 미들웨어, 작업 선택 영역 및 작업 호출 파이프라인을 통해 요청이 처리됩니다. 응답이 클라이언트에 전송되기 전에 작업 선택 영역, 라우팅 미들웨어 및 기타 다양한 미들웨어를 통해 요청 처리가 계속됩니다.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>필터 형식

각 필터 형식은 필터 파이프라인의 다른 단계에서 실행됩니다.

* [권한 부여 필터](#authorization-filters)는 가장 먼저 실행되고 사용자가 요청에 대한 권한이 있는지 여부를 결정하는 데 사용됩니다. 권한 부여 필터는 요청이 인증되지 않는 경우 파이프라인을 단락시킵니다.

* [리소스 필터](#resource-filters):

  * 권한 부여 후 실행됩니다.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>는 나머지 필터 파이프라인보다 먼저 코드를 실행합니다. 예를 들어 `OnResourceExecuting`는 모델 바인딩 전에 코드를 실행합니다.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>는 파이프라인의 나머지 부분이 완료된 후에 코드를 실행합니다.

* [작업 필터](#action-filters):

  * 작업 메서드가 호출되기 전후에 즉시 코드를 실행합니다.
  * 작업에 전달된 인수를 변경할 수 있습니다.
  * 작업에서 반환된 결과를 변경할 수 있습니다.
  * 는 페이지에서 지원 **되지 않습니다** Razor .

* [예외 필터](#exception-filters)는 응답 본문이 쓰여지기 전에 발생한 처리되지 않은 예외에 대한 전역 정책을 적용합니다.

* [결과 필터](#result-filters)는 작업 결과의 실행 전후에 즉시 코드를 실행합니다. 작업 메서드가 성공적으로 실행되는 경우에만 실행됩니다. 보기 또는 포맷터 실행을 둘러싸야 하는 논리에 유용합니다.

다음 다이어그램은 필터 형식이 필터 파이프라인에서 상호 작용하는 방법을 보여줍니다.

![요청은 권한 부여 필터, 리소스 필터, 모델 바인딩, 작업 필터, 작업 실행 및 작업 결과 변환, 예외 필터, 결과 필터 및 결과 실행을 통해 처리됩니다. 빠져나갈 때는 응답이 클라이언트에 전송되기 전에 결과 필터 및 리소스 필터에 의해서만 요청이 처리됩니다.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>구현

필터는 다른 인터페이스 정의를 통해 동기 및 비동기 구현을 모두 지원합니다.

동기 필터는 해당 파이프라인 단계 전후에 코드를 실행합니다. 예를 들어 <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*>는 작업 메서드가 호출되기 전에 호출됩니다. <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>는 작업 메서드가 반환된 후 호출됩니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

위의 코드에서 [Mydebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) 는 [샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs)의 유틸리티 함수입니다.

비동기 필터는 `On-Stage-ExecutionAsync` 메서드를 정의합니다. 예: <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

이전 코드에서 `SampleAsyncActionFilter`에는 작업 메서드를 실행하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>(`next`)가 포함되어 있습니다.

### <a name="multiple-filter-stages"></a>여러 필터 단계

단일 클래스에서 여러 필터 단계에 대한 인터페이스를 구현할 수 있습니다. 예를 들어 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 구현합니다.

* 동기: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>
* 비동기: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

필터 인터페이스의 동기 또는 비동기 버전을 모두 구현하지 **말고** 그 중 **한 가지** 만 구현하세요. 런타임은 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다. 그렇지 않으면 동기 인터페이스의 메서드를 호출합니다. 비동기 및 동기 인터페이스가 모두 하나의 클래스에 구현된 경우에는 비동기 메서드만 호출됩니다. 와 같은 추상 클래스를 사용 하는 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 각 필터 형식에 대해 동기 메서드 또는 비동기 메서드만 재정의 합니다.

### <a name="built-in-filter-attributes"></a>기본 제공 필터 특성

ASP.NET Core에는 서브클래싱 및 사용자 지정할 수 있는 기본 제공 특성 기반 필터가 포함되어 있습니다. 예를 들어 다음 결과 필터는 응답에 헤더를 추가합니다.

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

특성을 사용하면 이전 예제와 같이 필터에서 인수를 받을 수 있습니다. 컨트롤러나 작업 메서드에 `AddHeaderAttribute`를 적용하고 HTTP 헤더의 이름 및 값을 지정합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

[브라우저 개발자 도구](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) 와 같은 도구를 사용 하 여 헤더를 검사 합니다. **응답 헤더** 에 `author: Rick Anderson`이 표시됩니다.

다음 코드는 다음과 같은 작업을 수행하는 `ActionFilterAttribute`를 구현합니다.

* 구성 시스템에서 제목과 이름을 읽습니다. 앞의 샘플과 달리 다음 코드는 필터 매개 변수를 코드에 추가하지 않아도 됩니다.
* 응답 헤더에 제목과 이름을 추가합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

구성 옵션은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 [구성 시스템](xref:fundamentals/configuration/index)에서 제공됩니다. 예를 들어 파일에서 *appsettings.json* 다음을 수행 합니다.

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

`StartUp.ConfigureServices`에서

* `PositionOptions` 클래스는 `"Position"` 구성 영역을 사용하여 서비스 컨테이너에 추가됩니다.
* `MyActionFilterAttribute`는 서비스 컨테이너에 추가됩니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

다음 코드에서는 `PositionOptions` 클래스를 보여 줍니다.

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

다음 코드는 `MyActionFilterAttribute`를 `Index2` 메서드에 적용합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

**응답 헤더** 아래에는 `author: Rick Anderson` `Editor: Joe Smith` 끝점이 호출 될 때 및가 표시 됩니다 `Sample/Index2` .

다음 코드에서는 `MyActionFilterAttribute` 페이지에 및을 적용 합니다 `AddHeaderAttribute` Razor .

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

페이지 처리기 메서드에는 필터를 적용할 수 없습니다 Razor . 이러한 Razor 모델은 페이지 모델 또는 전역적으로 적용할 수 있습니다.

여러 필터 인터페이스에는 사용자 지정 구현에 대한 기본 클래스로 사용할 수 있는 해당 특성이 있습니다.

필터 특성:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>필터 범위 및 실행 순서

다음 세 가지 *범위* 중 하나에서 파이프라인에 필터를 추가할 수 있습니다.

* 컨트롤러 작업에서 특성 사용. 페이지 처리기 메서드에는 필터 특성을 적용할 수 없습니다 Razor .
* 컨트롤러 또는 페이지에서 특성을 사용 Razor 합니다.
* 모든 컨트롤러, 작업 및 페이지에 대해 전역적 Razor 으로 다음 코드와 같이 표시 됩니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a>기본 실행 순서

파이프라인의 특정 단계에 여러 개의 필터가 있는 경우 범위가 기본 필터 실행 순서를 결정합니다.  전역 필터는 메서드 필터를 둘러싼 클래스 필터를 둘러쌉니다.

필터 중첩의 결과로 필터의 *after* 코드는 *before* 코드의 역순으로 실행됩니다. 필터의 순서는 다음과 같습니다.

* 전역 필터의 *before* 코드.
  * 컨트롤러 및 페이지 필터의 *이전* 코드 Razor 입니다.
    * 작업 메서드 필터의 *before* 코드.
    * 작업 메서드 필터의 *after* 코드.
  * 컨트롤러 및 페이지 필터의 *이후* 코드 Razor 입니다.
* 전역 필터의 *after* 코드.
  
다음 예제는 필터 메서드가 동기 작업 필터에 대해 호출되는 순서를 보여줍니다.

| 시퀀스 | 필터 범위 | 필터 메서드 |
|:--------:|:------------:|:-------------:|
| 1 | 전역 | `OnActionExecuting` |
| 2 | 컨트롤러 또는 Razor 페이지| `OnActionExecuting` |
| 3 | 메서드 | `OnActionExecuting` |
| 4 | 메서드 | `OnActionExecuted` |
| 5 | 컨트롤러 또는 Razor 페이지 | `OnActionExecuted` |
| 6 | 전역 | `OnActionExecuted` |

### <a name="controller-level-filters"></a>컨트롤러 수준 필터

<xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스에서 상속되는 모든 컨트롤러에는 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 및 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 메서드가 포함됩니다. 이러한 메서드는: 다음 메서드는

* 지정된 작업을 위해 실행되는 필터를 래핑합니다.
* `OnActionExecuting`는 작업 필터 이전에 호출됩니다.
* `OnActionExecuted`는 모든 작업 필터 이후에 호출됩니다.
* `OnActionExecutionAsync`는 작업 필터 이전에 호출됩니다. 작업 메서드 이후 `next`가 실행된 후의 필터 코드.

예를 들어 다운로드 예제에서 `MySampleActionFilter`은 시작할 때 전역적으로 적용됩니다.

`TestController`:

* `SampleActionFilterAttribute`(`[SampleActionFilter]`)를 `FilterTest2` 작업에 적용합니다.
* `OnActionExecuting` 및 `OnActionExecuted`를 재정의합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

`https://localhost:5001/Test/FilterTest2`로 이동하면 다음 코드가 실행됩니다.

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

컨트롤러 수준 필터는 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 속성을 `int.MinValue`로 설정합니다. 컨트롤러 수준 필터는 메서드에 적용된 후에 실행되도록 설정할 수 **없습니다**. 순서는 다음 섹션에 설명되어 있습니다.

Razor페이지는 [ Razor 필터 메서드를 재정의 하 여 페이지 필터 구현](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)을 참조 하세요.

### <a name="overriding-the-default-order"></a>기본 순서 재정의

<xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>를 구현하여 실행의 기본 순서를 재정의할 수 있습니다. `IOrderedFilter`은 실행 순서를 결정하는 데 범위보다 우선 순위가 높은 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 속성을 노출합니다. 낮은 `Order` 값을 가진 필터는:

* 더 높은 `Order` 값을 가진 필터 이전에 *before* 코드를 실행합니다.
* 더 높은 `Order` 값을 가진 필터 이후에 *after* 코드를 실행합니다.

`Order` 속성은 생성자 매개 변수를 사용하여 설정됩니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

다음 컨트롤러의 두 작업 필터를 고려합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

전역 필터는 `StartUp.ConfigureServices`에 추가됩니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

3개의 필터는 다음 순서로 실행됩니다.

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

`Order` 속성은 필터가 실행되는 순서를 결정할 때 범위를 무시합니다. 필터가 순서에 따라 먼저 정렬된 다음, 범위는 연결을 끊는 데 사용됩니다. 모든 기본 제공 필터는 `IOrderedFilter`을 구현하고 기본 `Order` 값을 0으로 설정합니다. 앞서 언급했듯이 컨트롤러 수준 필터는 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 속성을 `int.MinValue`로 설정합니다. 기본 제공 필터의 경우 `Order`가 0이 아닌 값으로 설정되지 않은 한 범위가 순서를 결정합니다.

이전 코드에서 `MySampleActionFilter`는 전역 범위를 가지므로 컨트롤러 범위를 포함하는 `MyAction2FilterAttribute` 전에 실행됩니다. `MyAction2FilterAttribute`를 먼저 실행하려면 순서를 `int.MinValue`로 설정합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

전역 필터 `MySampleActionFilter`를 먼저 실행하려면 `Order`를 `int.MinValue`로 설정합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a>취소 및 단락

필터 메서드에 제공되는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 매개 변수의 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 속성을 설정하여 필터 파이프라인을 단락시킬 수 있습니다. 예를 들어 다음 리소스 필터는 파이프라인의 나머지 부분이 실행되지 않도록 막습니다.

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

다음 코드에서 `ShortCircuitingResourceFilter` 및 `AddHeader` 필터는 `SomeResource` 작업 메서드를 대상으로 지정합니다. `ShortCircuitingResourceFilter`:

* 리소스 필터이고 `AddHeader`이 작업 필터이기 때문에 먼저 실행합니다.
* 나머지 파이프라인을 단락시킵니다.

따라서 `AddHeader` 필터는 `SomeResource` 작업에 대해 절대 실행되지 않습니다. 이 동작은 `ShortCircuitingResourceFilter`가 먼저 실행되기 때문에 작업 메서드 수준에서 두 필터를 적용하더라도 동일합니다. 해당 필터 형식 또는 `Order` 속성의 명시적 사용으로 인해 `ShortCircuitingResourceFilter`이 실행됩니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a>종속성 주입

형식 또는 인스턴스별로 필터를 추가할 수 있습니다. 인스턴스가 추가되면 모든 요청에 해당 인스턴스가 사용됩니다. 형식이 추가되면 해당 필터는 형식으로 활성화됩니다. 형식으로 활성화된 필터는 다음을 의미합니다.

* 각 요청에 대해 인스턴스가 만들어집니다.
* 모든 생성자 종속성이 DI([종속성 주입](xref:fundamentals/dependency-injection))를 통해서 채워집니다.

특성으로 구현되고 컨트롤러 클래스 또는 작업 메서드에 직접 추가되는 필터에는 DI([종속성 주입](xref:fundamentals/dependency-injection))에서 제공하는 생성자 종속성이 없을 것입니다. DI는 다음과 같은 이유로 생성자 종속성을 제공할 수 없습니다.

* 특성이 적용될 때 해당 생성자 매개 변수가 제공되어야 하기 때문입니다. 
* 특성이 작동하는 방법의 제한 사항입니다.

다음 필터는 DI에서 제공하는 생성자 종속성을 지원합니다.

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* 특성에서 구현된 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.

위의 필터는 컨트롤러 또는 작업 메서드에 적용할 수 있습니다.

로거는 DI를 통해서 사용할 수 있습니다. 그러나 로깅 용도로만 필터를 만들거나 사용하지는 마세요. [기본 제공 프레임워크 로깅](xref:fundamentals/logging/index)은 일반적으로 로깅에 필요한 기능을 제공합니다. 필터에 추가된 로깅은:

* 비즈니스 도메인 문제 또는 필터 고유의 동작에 중점을 두어야 합니다.
* 작업 또는 다른 프레임워크 이벤트를 로깅해서는 **안 됩니다**. 기본 제공 필터는 작업 및 프레임워크 이벤트를 로깅합니다.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

서비스 필터 구현 형식은 `ConfigureServices`에 등록됩니다. <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 DI에서 필터의 인스턴스를 검색합니다.

다음 코드는 `AddHeaderResultServiceFilter`를 보여 줍니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

다음 코드에서 `AddHeaderResultServiceFilter`는 DI 컨테이너에 추가됩니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

다음 코드에서 `ServiceFilter` 특성은 DI에서 `AddHeaderResultServiceFilter` 필터의 인스턴스를 수신합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

`ServiceFilterAttribute`를 사용할 때 [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)을 설정하면:

* 필터 인스턴스가 원래 생성된 요청 범위 외부에서 재사용될 가능성이 *있음* 을 암시하는 것입니다. ASP.NET Core 런타임은 다음을 보장하지 않습니다.

  * 필터의 단일 인스턴스 생성.
  * 나중에 DI 컨테이너에서 필터가 다시 요청되지 않음.

* 싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다. `IFilterFactory`은 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다. `CreateInstance`는 DI에서 지정된 형식을 로드합니다.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>와 비슷하지만 해당 형식은 DI 컨테이너에서 직접 확인되지 않습니다. <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>를 사용하여 형식을 인스턴스화합니다.

`TypeFilterAttribute` 형식은 DI 컨테이너에서 직접 해결되지 않기 때문입니다.

* `TypeFilterAttribute`을 사용하여 참조되는 형식은 DI 컨테이너를 사용하여 등록할 필요가 없습니다.  DI 컨테이너에서 충족하는 종속성을 갖고 있습니다.
* `TypeFilterAttribute`는 형식에 대한 생성자 인수를 필요에 따라 허용할 수 있습니다.

`TypeFilterAttribute`를 사용할 때 [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)을 설정하면:
* 필터 인스턴스가 원래 생성된 요청 범위 밖에서 재사용될 가능성이 *있음* 을 암시하는 것입니다. ASP.NET Core 런타임은 단일 필터 인스턴스가 생성되도록 보장하지 않습니다.

* 싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.

다음 예제에서는 `TypeFilterAttribute`를 사용하여 인수를 형식에 전달하는 방법을 보여줍니다.

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>권한 부여 필터

권한 부여 필터는:

* 필터 파이프라인에서 첫 번째로 실행되는 필터입니다.
* 동작 메서드에 대한 액세스를 제어합니다.
* before 메서드는 있지만 after 메서드는 없습니다.

사용자 지정 권한 부여 필터는 사용자 지정 권한 부여 프레임워크를 필요로 합니다. 사용자 지정 필터를 작성하는 대신 권한 부여 정책을 구성하거나 사용자 지정 권한 부여 정책을 작성하는 것이 좋습니다. 기본 제공 권한 부여 필터는:

* 권한 부여 시스템을 호출합니다.
* 요청을 승인하지 않습니다.

권한 부여 필터 내에서 예외를 던지지 **마세요**.

* 해당 예외는 처리되지 않습니다.
* 예외 필터가 해당 예외를 처리하지 않습니다.

권한 부여 필터에서 예외가 발생할 경우 챌린지 발행을 고려하세요.

[권한 부여](xref:security/authorization/introduction)에 대해 자세히 알아봅니다.

## <a name="resource-filters"></a>리소스 필터

리소스 필터는:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 인터페이스 중 하나를 구현합니다.
* 실행이 필터 파이프라인 대부분을 래핑합니다.
* [권한 부여 필터](#authorization-filters) 는 리소스 필터 이전에만 실행 됩니다.

리소스 필터는 파이프라인의 대부분을 단락시켜야 하는 경우에 유용합니다. 예를 들어 캐싱 필터는 캐시 적중 시 파이프라인의 나머지 부분을 막을 수 있습니다.

리소스 필터 예제:

* 이전에 살펴본 [단락 리소스 필터](#short-circuiting-resource-filter).
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * 모델 바인딩이 양식 데이터에 액세스하는 것을 방지합니다.
  * 대용량 파일 업로드에서 양식 데이터를 메모리로 읽어들이는 것을 방지하기 위해서 사용됩니다.

## <a name="action-filters"></a>작업 필터

작업 필터는 페이지에 적용 **되지** 않습니다 Razor . Razor 페이지는 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 및를 지원 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> 합니다. 자세한 내용은 [Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.

작업 필터는:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 인터페이스 중 하나를 구현합니다.
* 해당 실행은 작업 메서드의 실행을 둘러쌉니다.

다음 코드는 예제 작업 필터를 보여 줍니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>는 다음과 같은 속성을 제공합니다.

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 작업 메서드에 대한 입력을 읽을 수 있습니다.
* <xref:Microsoft.AspNetCore.Mvc.Controller> - 컨트롤러 인스턴스를 조작할 수 있습니다.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` 설정은 작업 메서드 및 후속 작업 필터의 실행을 단락시킵니다.

작업 메서드에서 예외를 던지면:

* 후속 필터의 실행을 막습니다.
* `Result` 설정과 달리 성공적인 결과 대신 실패로 처리됩니다.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>는 다음과 같은 속성과 함께 `Controller` 및 `Result`를 제공합니다.

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 작업 실행이 다른 필터에 의해 단락된 경우 true입니다.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> - 작업 또는 이전에 실행된 작업 필터에서 예외가 던져진 경우 null이 아닙니다. 이 속성을 null로 설정하면:

  * 예외를 효과적으로 처리합니다.
  * `Result`는 작업 메서드에서 반환되는 것처럼 실행됩니다.

`IAsyncActionFilter`의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>에 대한 호출:

* 후속 작업 필터 및 작업 메서드를 실행합니다.
* `ActionExecutedContext`를 반환합니다.

단락시키려면 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName>을 결과 인스턴스에 할당하고 `next`(`ActionExecutionDelegate`)를 호출하지 않아야 합니다.

프레임워크는 서브클래싱 할 수 있는 추상 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 제공합니다.

`OnActionExecuting` 작업 필터는 다음에 사용할 수 있습니다.

* 모델 상태의 유효성을 검사합니다.
* 상태가 유효하지 않은 경우 오류를 반환합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> 특성으로 주석이 달린 컨트롤러 `[ApiController]` 는 자동으로 모델 상태의 유효성을 검사 하 고 400 응답을 반환 합니다. 자세한 정보는 [자동 HTTP 400 응답](xref:web-api/index#automatic-http-400-responses)을 참조하세요.

`OnActionExecuted` 메서드는 작업 메서드 이후에 실행됩니다.

* 그리고 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 속성을 통해 작업 결과를 확인하고 조작할 수 있습니다.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>는 작업 실행이 다른 필터에 의해 단락된 경우 true로 설정됩니다.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>은 작업 또는 후속 작업 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다. `Exception`을 Null로 설정:

  * 예외를 효과적으로 처리합니다.
  * `ActionExecutedContext.Result`은 작업 메서드에서 정상적으로 반환되는 것처럼 실행됩니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>예외 필터

예외 필터:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>를 구현합니다.
* 공통 오류 처리 정책을 구현하는 데 사용할 수 있습니다.

다음 예제 예외 필터는 사용자 지정 오류 보기를 사용하여 앱을 개발 중인 경우에 발생하는 예외에 대한 세부 정보를 표시합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

다음 코드에서는 예외 필터를 테스트합니다.

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

예외 필터:

* before 및 after 이벤트가 없습니다.
* <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>를 구현합니다.
* Razor페이지 또는 컨트롤러 생성, [모델 바인딩](xref:mvc/models/model-binding), 작업 필터 또는 작업 메서드에서 발생 하는 처리 되지 않은 예외를 처리 합니다.
* 리소스 필터, 결과 필터 또는 MVC 결과 실행에서 발생 하는 예외를 catch **하지** 마세요.

예외를 처리하려면 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 속성을 `true`로 설정하거나 응답을 작성합니다. 그러면 예외가 전파되지 않습니다. 예외 필터는 예외를 “성공”으로 변환할 수 없습니다. 이는 작업 필터에서만 가능합니다.

예외 필터:

* 작업 내에서 발생하는 예외를 잡는 데 좋습니다.
* 오류 처리 미들웨어만큼 유연하지 않습니다.

예외 처리의 경우 미들웨어를 선호합니다. 어떤 작업 메서드가 호출되는지에 따라 오류 처리 방식이 ‘다른’ 경우에만 예외 필터를 사용합니다. 예를 들어 앱에는 API 엔드포인트 및 보기/HTML 모두에 대한 작업 메서드가 있을 수 있습니다. API 엔드포인트는 JSON으로 오류 정보를 반환할 수 있습니다. 반면 보기 기반 작업은 HTML로 오류 페이지를 반환할 수 있습니다.

## <a name="result-filters"></a>결과 필터

결과 필터는:

* 인터페이스 구현:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* 해당 실행은 작업 결과의 실행을 둘러쌉니다.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter 및 IAsyncResultFilter

다음 코드는 HTTP 헤더를 추가하는 결과 필터를 보여 줍니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

실행되는 결과의 종류는 작업에 따라 다릅니다. 보기를 반환하는 작업에는 실행 중인 <xref:Microsoft.AspNetCore.Mvc.ViewResult>의 일부로 모든 Razor 프로세스가 포함됩니다. API 메서드는 실행 결과의 일부로 일부 serialization을 수행할 수 있습니다. [작업 결과](xref:mvc/controllers/actions)에 대해 자세히 알아보세요.

결과 필터는 작업 또는 작업 필터가 작업 결과를 생성하는 경우에만 실행됩니다. 다음 경우에는 결과 필터가 실행되지 않습니다.

* 권한 부여 필터 또는 리소스 필터가 파이프라인을 단락시킬 경우.
* 예외 필터는 작업 결과를 생성하여 예외를 처리합니다.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 메서드는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName>를 `true`로 설정하여 작업 결과 및 후속 결과 필터를 단락(short-circuit) 처리할 수 있습니다. 단락시킬 경우 빈 응답을 생성하지 않도록 응답 개체에 작성하세요. `IResultFilter.OnResultExecuting`에서 예외 throw:

* 작업 결과 및 후속 필터의 실행을 막습니다.
* 성공적인 결과 대신 실패로 처리됩니다.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 메서드가 실행될 때에는 응답이 이미 클라이언트에 전송되었을 수 있습니다. 이미 클라이언트에 전송된 응답은 변경할 수 없습니다.

`ResultExecutedContext.Canceled`는 작업 결과 실행이 다른 필터에 의해 단락(short-circuit) 처리된 경우 `true`로 설정됩니다.

`ResultExecutedContext.Exception`은 작업 결과 또는 후속 결과 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다. `Exception`을 효과적으로 null로 설정하면 예외를 '처리'하고 예외가 파이프라인의 뒷부분에서 다시 throw되지 않습니다. 결과 필터에서 예외를 처리하는 경우 데이터를 응답에 쓸 수 있는 신뢰할 수 있는 방법이 없습니다. 작업 결과가 예외를 던질 때 헤더가 클라이언트에 플러시된 경우 오류 코드를 전송하기 위한 신뢰할 수 있는 메커니즘이 없습니다.

<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate>의 `await next` 호출은 후속 결과 필터 및 작업 결과를 실행합니다. 단락시키려면 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel)을 `true`로 설정하고 `ResultExecutionDelegate`를 호출하지 마세요.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

프레임워크는 서브클래싱 할 수 있는 추상 `ResultFilterAttribute`를 제공합니다. 이전에 표시된 [AddHeaderAttribute](#add-header-attribute) 클래스는 결과 필터 특성의 예제입니다.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter 및 IAsyncAlwaysRunResultFilter

<xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 인터페이스는 모든 작업 결과에 대해 실행되는 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 구현을 선언합니다. 여기에는 다음에 의해 생성되는 작업 결과가 포함됩니다.

* 단락하는 권한 부여 필터 및 리소스 필터
* 예외 필터

예를 들어 다음 필터는 항상 작업 결과(콘텐츠 협상이 실패할 경우 *422 Unprocessable Entity* 상태 코드가 포함된 <xref:Microsoft.AspNetCore.Mvc.ObjectResult>)를 실행 및 설정합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>를 구현합니다. 따라서 `IFilterFactory` 인스턴스를 필터 파이프라인에서 `IFilterMetadata` 인스턴스로 사용할 수 있습니다. 런타임이 필터를 호출하려고 준비할 때 `IFilterFactory`로 캐스팅을 시도합니다. 해당 캐스팅에 성공하면 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 호출하여 호출되는 `IFilterMetadata` 인스턴스를 만듭니다. 앱이 시작될 때 정확한 필터 파이프라인을 명시적으로 설정할 필요가 없으므로 유연한 디자인을 제공합니다.

`IFilterFactory.IsReusable`:

* 팩터리에서 생성 된 요청 범위 외부에서 팩터리에서 만든 필터 인스턴스를 다시 사용할 수 있음을 팩터리에서 힌트 합니다.
* Singleton 이외의 수명으로 서비스에 의존 하는 필터와 함께 사용 **하면 안 됩니다**.

ASP.NET Core 런타임은 다음을 보장하지 않습니다.

_ 필터의 단일 인스턴스가 생성 됩니다.
* 나중에 DI 컨테이너에서 필터가 다시 요청되지 않음.

[!WARNING]`IFilterFactory.IsReusable` `true` 필터 원본이 모호 하 고, 필터가 상태 비저장 이며, 여러 HTTP 요청에서 사용할 수 있는 경우에만를 반환 하도록를 구성 합니다. 예를 들어,가를 반환 하는 경우 범위 지정 또는 임시로 등록 된 DI에서 필터를 반환 하지 않습니다. `IFilterFactory.IsReusable``true`

필터를 만드는 다른 방법으로 사용자 지정 특성 구현을 사용하여 `IFilterFactory`를 구현할 수 있습니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

필터는 다음 코드에서 적용됩니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

[샘플 다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)를 실행하여 이전 코드를 테스트할 수 있습니다.

* F12 개발자 도구를 호출합니다.
* `https://localhost:5001/Sample/HeaderWithFactory`로 이동합니다.

F12 개발자 도구는 샘플 코드에 의해 추가된 다음 응답 헤더를 표시합니다.

* **작성자:**`Rick Anderson`
* **globaladdheader:** `Result filter added to MvcOptions.Filters`
* **내부:**`My header`

이전 코드에서는 **internal:** `My header` 응답 헤더를 만듭니다.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>특성에서 구현된 IFilterFactory

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

`IFilterFactory`를 구현하는 필터는 다음과 같은 필터에 유용합니다.

* 매개 변수 전달을 필요로 하지 않는 필터.
* DI에 의해 채워져야 할 생성자 종속성이 있는 필터.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다. `IFilterFactory`은 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다. `CreateInstance`는 서비스 컨테이너(DI)에서 지정된 형식을 로드합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

다음 코드는 `[SampleActionFilter]`을 적용하는 세 가지 방법을 보여 줍니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

이전 코드에서 `[SampleActionFilter]`로 메서드를 장식하는 것이 `SampleActionFilter`을 적용하는 데 선호되는 방법입니다.

## <a name="using-middleware-in-the-filter-pipeline"></a>필터 파이프라인에서 미들웨어 사용

리소스 필터는 파이프라인의 뒷부분에 제공되는 모든 실행을 둘러싼다는 점에서 [미들웨어](xref:fundamentals/middleware/index)처럼 작동합니다. 하지만 필터는 런타임의 일부라는 점에서 미들웨어와 다릅니다. 즉, 컨텍스트 및 구문에 액세스할 수 있습니다.

미들웨어를 필터로 사용하려면 필터 파이프라인에 삽입할 미들웨어를 지정하는 `Configure` 메서드를 포함한 형식을 만듭니다. 다음 예제는 지역화 미들웨어를 사용하여 요청에 대한 현재 문화권을 설정합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>를 사용하여 미들웨어를 실행합니다.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

미들웨어 필터는 모델 바인딩 이전 및 나머지 파이프라인 이후에 리소스 필터와 동일한 필터 파이프라인 단계에서 실행됩니다.

## <a name="next-actions"></a>다음 작업

* [ Razor 페이지에 대 한 필터 메서드를](xref:razor-pages/filter)참조 하세요.
* 필터를 실험하려면 [GitHub 샘플을 다운로드하고, 테스트하고, 수정](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)

ASP.NET Core에서 *필터* 를 사용하면 요청 처리 파이프라인의 특정 단계 전후에 코드를 실행할 수 있습니다.

기본 제공 필터는 다음과 같은 작업을 처리합니다.

* 권한 부여(사용자가 권한이 없는 리소스에 액세스 방지).
* 응답 캐싱(요청 파이프라인을 단락하면 캐시된 응답을 반환합니다).

사용자 지정 필터를 만들어 교차 편집 문제를 처리할 수 있습니다. 횡단 관심사의 사례로는 오류 처리, 캐싱, 구성, 권한 부여 및 로깅을 들 수 있습니다.  필터는 코드 중복을 방지합니다. 예를 들어 오류 처리 예외 필터는 오류 처리를 통합할 수 있습니다.

이 문서 Razor 는 뷰가 있는 페이지, API 컨트롤러 및 컨트롤러에 적용 됩니다.

[예제 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="how-filters-work"></a>필터 작동 방법

필터는 ‘필터 파이프라인’이라고도 하는 ‘ASP.NET Core 동작 호출 파이프라인’내에서 실행됩니다.  필터 파이프라인은 ASP.NET Core가 실행할 작업을 선택한 후에 실행됩니다.

![요청은 기타 미들웨어, 라우팅 미들웨어, 작업 선택 및 ASP.NET Core 작업 호출 파이프라인을 통해서 처리됩니다. 응답이 클라이언트에 전송되기 전에 작업 선택 영역, 라우팅 미들웨어 및 기타 다양한 미들웨어를 통해 요청 처리가 계속됩니다.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>필터 형식

각 필터 형식은 필터 파이프라인의 다른 단계에서 실행됩니다.

* [권한 부여 필터](#authorization-filters)는 가장 먼저 실행되고 사용자가 요청에 대한 권한이 있는지 여부를 결정하는 데 사용됩니다. 권한 부여 필터는 요청이 인증되지 않는 경우 파이프라인을 단락시킵니다.

* [리소스 필터](#resource-filters):

  * 권한 부여 후 실행됩니다.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>는 나머지 필터 파이프라인보다 먼저 코드를 실행할 수 있습니다. 예를 들어 `OnResourceExecuting`는 모델 바인딩 전에 코드를 실행할 수 있습니다.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>는 파이프라인의 나머지 부분이 완료된 후에 코드를 실행할 수 있습니다.

* [작업 필터](#action-filters)는 개별 작업 메서드가 호출된 전후에 즉시 코드를 실행할 수 있습니다. 작업에 전달된 인수 및 작업에서 반환된 결과를 조작하는 데 사용할 수 있습니다. 작업 필터는 페이지에서 지원 **되지 않습니다** Razor .

* [예외 필터](#exception-filters)는 응답 본문에 무언가 쓰여지기 전에 발생한 처리되지 않은 예외에 전역 정책을 적용하는 데 사용됩니다.

* [결과 필터](#result-filters)는 개별 작업이 실행된 전후에 즉시 코드를 실행할 수 있습니다. 작업 메서드가 성공적으로 실행되는 경우에만 실행됩니다. 보기 또는 포맷터 실행을 둘러싸야 하는 논리에 유용합니다.

다음 다이어그램은 필터 형식이 필터 파이프라인에서 상호 작용하는 방법을 보여줍니다.

![요청은 권한 부여 필터, 리소스 필터, 모델 바인딩, 작업 필터, 작업 실행 및 작업 결과 변환, 예외 필터, 결과 필터 및 결과 실행을 통해 처리됩니다. 빠져나갈 때는 응답이 클라이언트에 전송되기 전에 결과 필터 및 리소스 필터에 의해서만 요청이 처리됩니다.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>구현

필터는 다른 인터페이스 정의를 통해 동기 및 비동기 구현을 모두 지원합니다.

동기 필터는 해당 파이프라인 단계 전(`On-Stage-Executing`)과 후(`On-Stage-Executed`)에 코드를 실행할 수 있습니다. 예를 들어 `OnActionExecuting`는 작업 메서드가 호출되기 전에 호출됩니다. `OnActionExecuted`는 작업 메서드가 반환된 후 호출됩니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

비동기 필터는 `On-Stage-ExecutionAsync` 메서드를 정의합니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

이전 코드에서 `SampleAsyncActionFilter`에는 작업 메서드를 실행하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>(`next`)가 포함되어 있습니다.  각 `On-Stage-ExecutionAsync` 메서드는 필터의 파이프라인 단계를 실행하는 `FilterType-ExecutionDelegate`를 받습니다.

### <a name="multiple-filter-stages"></a>여러 필터 단계

단일 클래스에서 여러 필터 단계에 대한 인터페이스를 구현할 수 있습니다. 예를 들어 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 클래스는 `IActionFilter`, `IResultFilter` 및 해당 비동기 값을 구현합니다.

필터 인터페이스의 동기 또는 비동기 버전을 모두 구현하지 **말고** 그 중 **한 가지** 만 구현하세요. 런타임은 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다. 그렇지 않으면 동기 인터페이스의 메서드를 호출합니다. 비동기 및 동기 인터페이스가 모두 하나의 클래스에 구현된 경우에는 비동기 메서드만 호출됩니다. <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 같은 추상 클래스를 사용하는 경우 각 필터 형식에 대한 동기 메서드 또는 비동기 메서드만 재정의합니다.

### <a name="built-in-filter-attributes"></a>기본 제공 필터 특성

ASP.NET Core에는 서브클래싱 및 사용자 지정할 수 있는 기본 제공 특성 기반 필터가 포함되어 있습니다. 예를 들어 다음 결과 필터는 응답에 헤더를 추가합니다.

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

특성을 사용하면 이전 예제와 같이 필터에서 인수를 받을 수 있습니다. 컨트롤러나 작업 메서드에 `AddHeaderAttribute`를 적용하고 HTTP 헤더의 이름 및 값을 지정합니다.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

여러 필터 인터페이스에는 사용자 지정 구현에 대한 기본 클래스로 사용할 수 있는 해당 특성이 있습니다.

필터 특성:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>필터 범위 및 실행 순서

다음 세 가지 *범위* 중 하나에서 파이프라인에 필터를 추가할 수 있습니다.

* 작업에서 특성 사용.
* 컨트롤러에서 특성 사용.
* 다음 코드와 같이 모든 컨트롤러와 작업에 전역으로 사용:

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

이전 코드는 [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) 컬렉션을 사용하여 3개의 필터를 전역적으로 추가합니다.

### <a name="default-order-of-execution"></a>기본 실행 순서

*동일한 유형* 의 필터가 여러 개 있는 경우 범위가 필터 실행의 기본 순서를 결정합니다.  전역 필터는 클래스 필터를 둘러쌉니다. 클래스 필터는 메서드 필터를 둘러쌉니다.

필터 중첩의 결과로 필터의 *after* 코드는 *before* 코드의 역순으로 실행됩니다. 필터의 순서는 다음과 같습니다.

* 전역 필터의 *before* 코드.
  * 컨트롤러 필터의 *before* 코드.
    * 작업 메서드 필터의 *before* 코드.
    * 작업 메서드 필터의 *after* 코드.
  * 컨트롤러 필터의 *after* 코드.
* 전역 필터의 *after* 코드.
  
다음 예제는 필터 메서드가 동기 작업 필터에 대해 호출되는 순서를 보여줍니다.

| 시퀀스 | 필터 범위 | 필터 메서드 |
|:--------:|:------------:|:-------------:|
| 1 | 전역 | `OnActionExecuting` |
| 2 | 컨트롤러 | `OnActionExecuting` |
| 3 | 메서드 | `OnActionExecuting` |
| 4 | 메서드 | `OnActionExecuted` |
| 5 | 컨트롤러 | `OnActionExecuted` |
| 6 | 전역 | `OnActionExecuted` |

이 시퀀스는 다음을 보여 줍니다.

* 메서드 필터는 컨트롤러 필터 내에서 중첩됩니다.
* 컨트롤러 필터는 전역 필터 내에서 중첩됩니다.

### <a name="controller-and-no-locrazor-page-level-filters"></a>컨트롤러 및 Razor 페이지 수준 필터

<xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스에서 상속되는 모든 컨트롤러에는 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 및 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 메서드가 포함됩니다. 이러한 메서드는: 다음 메서드는

* 지정된 작업을 위해 실행되는 필터를 래핑합니다.
* `OnActionExecuting`는 작업 필터 이전에 호출됩니다.
* `OnActionExecuted`는 모든 작업 필터 이후에 호출됩니다.
* `OnActionExecutionAsync`는 작업 필터 이전에 호출됩니다. 작업 메서드 이후 `next`가 실행된 후의 필터 코드.

예를 들어 다운로드 예제에서 `MySampleActionFilter`은 시작할 때 전역적으로 적용됩니다.

`TestController`:

* `SampleActionFilterAttribute`(`[SampleActionFilter]`)를 `FilterTest2` 작업에 적용합니다.
* `OnActionExecuting` 및 `OnActionExecuted`를 재정의합니다.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

`https://localhost:5001/Test/FilterTest2`로 이동하면 다음 코드가 실행됩니다.

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Razor페이지는 [ Razor 필터 메서드를 재정의 하 여 페이지 필터 구현](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)을 참조 하세요.

### <a name="overriding-the-default-order"></a>기본 순서 재정의

<xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>를 구현하여 실행의 기본 순서를 재정의할 수 있습니다. `IOrderedFilter`은 실행 순서를 결정하는 데 범위보다 우선 순위가 높은 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 속성을 노출합니다. 낮은 `Order` 값을 가진 필터는:

* 더 높은 `Order` 값을 가진 필터 이전에 *before* 코드를 실행합니다.
* 더 높은 `Order` 값을 가진 필터 이후에 *after* 코드를 실행합니다.

생성자 매개 변수로 `Order` 속성을 설정할 수 있습니다.

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

이전 예제와 동일한 3개의 작업 필터를 가정해보세요. 컨트롤러와 전역 필터의 `Order` 속성을 각각 1과 2로 설정하면 실행 순서가 반대가 됩니다.

| 시퀀스 | 필터 범위 | `Order` 속성 | 필터 메서드 |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | 메서드 | 0 | `OnActionExecuting` |
| 2 | 컨트롤러 | 1  | `OnActionExecuting` |
| 3 | 전역 | 2  | `OnActionExecuting` |
| 4 | 전역 | 2  | `OnActionExecuted` |
| 5 | 컨트롤러 | 1  | `OnActionExecuted` |
| 6 | 메서드 | 0  | `OnActionExecuted` |

`Order` 속성은 필터가 실행되는 순서를 결정할 때 범위를 무시합니다. 필터가 순서에 따라 먼저 정렬된 다음, 범위는 연결을 끊는 데 사용됩니다. 모든 기본 제공 필터는 `IOrderedFilter`을 구현하고 기본 `Order` 값을 0으로 설정합니다. 기본 제공 필터의 경우 `Order`를 0이 아닌 값으로 설정하지 않는 한 범위가 순서를 결정합니다.

## <a name="cancellation-and-short-circuiting"></a>취소 및 단락

필터 메서드에 제공되는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 매개 변수의 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 속성을 설정하여 필터 파이프라인을 단락시킬 수 있습니다. 예를 들어 다음 리소스 필터는 파이프라인의 나머지 부분이 실행되지 않도록 막습니다.

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

다음 코드에서 `ShortCircuitingResourceFilter` 및 `AddHeader` 필터는 `SomeResource` 작업 메서드를 대상으로 지정합니다. `ShortCircuitingResourceFilter`:

* 리소스 필터이고 `AddHeader`이 작업 필터이기 때문에 먼저 실행합니다.
* 나머지 파이프라인을 단락시킵니다.

따라서 `AddHeader` 필터는 `SomeResource` 작업에 대해 절대 실행되지 않습니다. 이 동작은 `ShortCircuitingResourceFilter`가 먼저 실행되기 때문에 작업 메서드 수준에서 두 필터를 적용하더라도 동일합니다. 해당 필터 형식 또는 `Order` 속성의 명시적 사용으로 인해 `ShortCircuitingResourceFilter`이 실행됩니다.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>종속성 주입

형식 또는 인스턴스별로 필터를 추가할 수 있습니다. 인스턴스가 추가되면 모든 요청에 해당 인스턴스가 사용됩니다. 형식이 추가되면 해당 필터는 형식으로 활성화됩니다. 형식으로 활성화된 필터는 다음을 의미합니다.

* 각 요청에 대해 인스턴스가 만들어집니다.
* 모든 생성자 종속성이 DI([종속성 주입](xref:fundamentals/dependency-injection))를 통해서 채워집니다.

특성으로 구현되고 컨트롤러 클래스 또는 작업 메서드에 직접 추가되는 필터에는 DI([종속성 주입](xref:fundamentals/dependency-injection))에서 제공하는 생성자 종속성이 없을 것입니다. DI는 다음과 같은 이유로 생성자 종속성을 제공할 수 없습니다.

* 특성이 적용될 때 해당 생성자 매개 변수가 제공되어야 하기 때문입니다. 
* 특성이 작동하는 방법의 제한 사항입니다.

다음 필터는 DI에서 제공하는 생성자 종속성을 지원합니다.

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* 특성에서 구현된 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.

위의 필터는 컨트롤러 또는 작업 메서드에 적용할 수 있습니다.

로거는 DI를 통해서 사용할 수 있습니다. 그러나 로깅 용도로만 필터를 만들거나 사용하지는 마세요. [기본 제공 프레임워크 로깅](xref:fundamentals/logging/index)은 일반적으로 로깅에 필요한 기능을 제공합니다. 필터에 추가된 로깅은:

* 비즈니스 도메인 문제 또는 필터 고유의 동작에 중점을 두어야 합니다.
* 작업 또는 다른 프레임워크 이벤트를 로깅해서는 **안 됩니다**. 기본 제공 필터는 작업 및 프레임워크 이벤트를 로깅합니다.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

서비스 필터 구현 형식은 `ConfigureServices`에 등록됩니다. <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 DI에서 필터의 인스턴스를 검색합니다.

다음 코드는 `AddHeaderResultServiceFilter`를 보여 줍니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

다음 코드에서 `AddHeaderResultServiceFilter`는 DI 컨테이너에 추가됩니다.

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

다음 코드에서 `ServiceFilter` 특성은 DI에서 `AddHeaderResultServiceFilter` 필터의 인스턴스를 수신합니다.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

`ServiceFilterAttribute`를 사용할 때 [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)을 설정하면:

* 필터 인스턴스가 원래 생성된 요청 범위 외부에서 재사용될 가능성이 *있음* 을 암시하는 것입니다. ASP.NET Core 런타임은 다음을 보장하지 않습니다.

  * 필터의 단일 인스턴스 생성.
  * 나중에 DI 컨테이너에서 필터가 다시 요청되지 않음.

* 싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다. `IFilterFactory`은 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다. `CreateInstance`는 DI에서 지정된 형식을 로드합니다.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>와 비슷하지만 해당 형식은 DI 컨테이너에서 직접 확인되지 않습니다. <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>를 사용하여 형식을 인스턴스화합니다.

`TypeFilterAttribute` 형식은 DI 컨테이너에서 직접 해결되지 않기 때문입니다.

* `TypeFilterAttribute`을 사용하여 참조되는 형식은 DI 컨테이너를 사용하여 등록할 필요가 없습니다.  DI 컨테이너에서 충족하는 종속성을 갖고 있습니다.
* `TypeFilterAttribute`는 형식에 대한 생성자 인수를 필요에 따라 허용할 수 있습니다.

`TypeFilterAttribute`를 사용할 때 [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)을 설정하면:
* 필터 인스턴스가 원래 생성된 요청 범위 밖에서 재사용될 가능성이 *있음* 을 암시하는 것입니다. ASP.NET Core 런타임은 단일 필터 인스턴스가 생성되도록 보장하지 않습니다.

* 싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.

다음 예제에서는 `TypeFilterAttribute`를 사용하여 인수를 형식에 전달하는 방법을 보여줍니다.

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>권한 부여 필터

권한 부여 필터는:

* 필터 파이프라인에서 첫 번째로 실행되는 필터입니다.
* 동작 메서드에 대한 액세스를 제어합니다.
* before 메서드는 있지만 after 메서드는 없습니다.

사용자 지정 권한 부여 필터는 사용자 지정 권한 부여 프레임워크를 필요로 합니다. 사용자 지정 필터를 작성하는 대신 권한 부여 정책을 구성하거나 사용자 지정 권한 부여 정책을 작성하는 것이 좋습니다. 기본 제공 권한 부여 필터는:

* 권한 부여 시스템을 호출합니다.
* 요청을 승인하지 않습니다.

권한 부여 필터 내에서 예외를 던지지 **마세요**.

* 해당 예외는 처리되지 않습니다.
* 예외 필터가 해당 예외를 처리하지 않습니다.

권한 부여 필터에서 예외가 발생할 경우 챌린지 발행을 고려하세요.

[권한 부여](xref:security/authorization/introduction)에 대해 자세히 알아봅니다.

## <a name="resource-filters"></a>리소스 필터

리소스 필터는:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 인터페이스 중 하나를 구현합니다.
* 실행이 필터 파이프라인 대부분을 래핑합니다.
* [권한 부여 필터](#authorization-filters) 는 리소스 필터 이전에만 실행 됩니다.

리소스 필터는 파이프라인의 대부분을 단락시켜야 하는 경우에 유용합니다. 예를 들어 캐싱 필터는 캐시 적중 시 파이프라인의 나머지 부분을 막을 수 있습니다.

리소스 필터 예제:

* 이전에 살펴본 [단락 리소스 필터](#short-circuiting-resource-filter).
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * 모델 바인딩이 양식 데이터에 액세스하는 것을 방지합니다.
  * 대용량 파일 업로드에서 양식 데이터를 메모리로 읽어들이는 것을 방지하기 위해서 사용됩니다.

## <a name="action-filters"></a>작업 필터

> [!IMPORTANT]
> 작업 필터는 페이지에 적용 **되지** 않습니다 Razor . Razor 페이지는 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 및를 지원 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> 합니다. 자세한 내용은 [Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.

작업 필터는:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 인터페이스 중 하나를 구현합니다.
* 해당 실행은 작업 메서드의 실행을 둘러쌉니다.

다음 코드는 예제 작업 필터를 보여 줍니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>는 다음과 같은 속성을 제공합니다.

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 작업 메서드에 대한 입력을 읽을 수 있습니다.
* <xref:Microsoft.AspNetCore.Mvc.Controller> - 컨트롤러 인스턴스를 조작할 수 있습니다.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` 설정은 작업 메서드 및 후속 작업 필터의 실행을 단락시킵니다.

작업 메서드에서 예외를 던지면:

* 후속 필터의 실행을 막습니다.
* `Result` 설정과 달리 성공적인 결과 대신 실패로 처리됩니다.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>는 다음과 같은 속성과 함께 `Controller` 및 `Result`를 제공합니다.

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 작업 실행이 다른 필터에 의해 단락된 경우 true입니다.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> - 작업 또는 이전에 실행된 작업 필터에서 예외가 던져진 경우 null이 아닙니다. 이 속성을 null로 설정하면:

  * 예외를 효과적으로 처리합니다.
  * `Result`는 작업 메서드에서 반환되는 것처럼 실행됩니다.

`IAsyncActionFilter`의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>에 대한 호출:

* 후속 작업 필터 및 작업 메서드를 실행합니다.
* `ActionExecutedContext`를 반환합니다.

단락시키려면 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName>을 결과 인스턴스에 할당하고 `next`(`ActionExecutionDelegate`)를 호출하지 않아야 합니다.

프레임워크는 서브클래싱 할 수 있는 추상 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 제공합니다.

`OnActionExecuting` 작업 필터는 다음에 사용할 수 있습니다.

* 모델 상태의 유효성을 검사합니다.
* 상태가 유효하지 않은 경우 오류를 반환합니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

`OnActionExecuted` 메서드는 작업 메서드 이후에 실행됩니다.

* 그리고 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 속성을 통해 작업 결과를 확인하고 조작할 수 있습니다.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>는 작업 실행이 다른 필터에 의해 단락된 경우 true로 설정됩니다.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>은 작업 또는 후속 작업 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다. `Exception`을 Null로 설정:

  * 예외를 효과적으로 처리합니다.
  * `ActionExecutedContext.Result`은 작업 메서드에서 정상적으로 반환되는 것처럼 실행됩니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>예외 필터

예외 필터:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>를 구현합니다. 
* 공통 오류 처리 정책을 구현하는 데 사용할 수 있습니다.

다음 예제 예외 필터는 사용자 지정 오류 보기를 사용하여 앱을 개발 중인 경우에 발생하는 예외에 대한 세부 정보를 표시합니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

예외 필터:

* before 및 after 이벤트가 없습니다.
* <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>를 구현합니다.
* Razor페이지 또는 컨트롤러 생성, [모델 바인딩](xref:mvc/models/model-binding), 작업 필터 또는 작업 메서드에서 발생 하는 처리 되지 않은 예외를 처리 합니다.
* 리소스 필터, 결과 필터 또는 MVC 결과 실행에서 발생 하는 예외를 catch **하지** 마세요.

예외를 처리하려면 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 속성을 `true`로 설정하거나 응답을 작성합니다. 그러면 예외가 전파되지 않습니다. 예외 필터는 예외를 “성공”으로 변환할 수 없습니다. 이는 작업 필터에서만 가능합니다.

예외 필터:

* 작업 내에서 발생하는 예외를 잡는 데 좋습니다.
* 오류 처리 미들웨어만큼 유연하지 않습니다.

예외 처리의 경우 미들웨어를 선호합니다. 어떤 작업 메서드가 호출되는지에 따라 오류 처리 방식이 ‘다른’ 경우에만 예외 필터를 사용합니다. 예를 들어 앱에는 API 엔드포인트 및 보기/HTML 모두에 대한 작업 메서드가 있을 수 있습니다. API 엔드포인트는 JSON으로 오류 정보를 반환할 수 있습니다. 반면 보기 기반 작업은 HTML로 오류 페이지를 반환할 수 있습니다.

## <a name="result-filters"></a>결과 필터

결과 필터는:

* 인터페이스 구현:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* 해당 실행은 작업 결과의 실행을 둘러쌉니다.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter 및 IAsyncResultFilter

다음 코드는 HTTP 헤더를 추가하는 결과 필터를 보여 줍니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

실행되는 결과의 종류는 작업에 따라 다릅니다. 보기를 반환하는 작업에는 실행 중인 <xref:Microsoft.AspNetCore.Mvc.ViewResult>의 일부로 모든 Razor 프로세스가 포함됩니다. API 메서드는 실행 결과의 일부로 일부 serialization을 수행할 수 있습니다. [작업 결과](xref:mvc/controllers/actions)에 대해 자세히 알아보세요.

결과 필터는 작업 또는 작업 필터가 작업 결과를 생성하는 경우에만 실행됩니다. 다음 경우에는 결과 필터가 실행되지 않습니다.

* 권한 부여 필터 또는 리소스 필터가 파이프라인을 단락시킬 경우.
* 예외 필터는 작업 결과를 생성하여 예외를 처리합니다.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 메서드는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName>를 `true`로 설정하여 작업 결과 및 후속 결과 필터를 단락(short-circuit) 처리할 수 있습니다. 단락시킬 경우 빈 응답을 생성하지 않도록 응답 개체에 작성하세요. `IResultFilter.OnResultExecuting`에서 예외를 던지면:

* 작업 결과 및 후속 필터의 실행을 방지합니다.
* 성공적인 결과 대신 실패로 처리됩니다.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 메서드가 실행될 때에는 응답이 이미 클라이언트에 전송되었을 수 있습니다. 이미 클라이언트에 전송된 응답은 더이상 변경할 수 없습니다.

`ResultExecutedContext.Canceled`는 작업 결과 실행이 다른 필터에 의해 단락(short-circuit) 처리된 경우 `true`로 설정됩니다.

`ResultExecutedContext.Exception`은 작업 결과 또는 후속 결과 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다. `Exception`을 null로 설정하면 효과적으로 예외를 처리하고 이후의 파이프라인에서 ASP.NET Core에 의해 예외가 다시 던져지지 않습니다. 결과 필터에서 예외를 처리하는 경우 데이터를 응답에 쓸 수 있는 신뢰할 수 있는 방법이 없습니다. 작업 결과가 예외를 던질 때 헤더가 클라이언트에 플러시된 경우 오류 코드를 전송하기 위한 신뢰할 수 있는 메커니즘이 없습니다.

<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate>의 `await next` 호출은 후속 결과 필터 및 작업 결과를 실행합니다. 단락시키려면 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel)을 `true`로 설정하고 `ResultExecutionDelegate`를 호출하지 마세요.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

프레임워크는 서브클래싱 할 수 있는 추상 `ResultFilterAttribute`를 제공합니다. 이전에 표시된 [AddHeaderAttribute](#add-header-attribute) 클래스는 결과 필터 특성의 예제입니다.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter 및 IAsyncAlwaysRunResultFilter

<xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 인터페이스는 모든 작업 결과에 대해 실행되는 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 구현을 선언합니다. 여기에는 다음에 의해 생성되는 작업 결과가 포함됩니다.

* 단락하는 권한 부여 필터 및 리소스 필터
* 예외 필터

예를 들어 다음 필터는 항상 작업 결과(콘텐츠 협상이 실패할 경우 *422 Unprocessable Entity* 상태 코드가 포함된 <xref:Microsoft.AspNetCore.Mvc.ObjectResult>)를 실행 및 설정합니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>를 구현합니다. 따라서 `IFilterFactory` 인스턴스를 필터 파이프라인에서 `IFilterMetadata` 인스턴스로 사용할 수 있습니다. 런타임이 필터를 호출하려고 준비할 때 `IFilterFactory`로 캐스팅을 시도합니다. 해당 캐스팅에 성공하면 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 호출하여 호출되는 `IFilterMetadata` 인스턴스를 만듭니다. 앱이 시작될 때 정확한 필터 파이프라인을 명시적으로 설정할 필요가 없으므로 유연한 디자인을 제공합니다.

필터를 만드는 다른 방법으로 사용자 지정 특성 구현을 사용하여 `IFilterFactory`를 구현할 수 있습니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

[다운로드 예제](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)를 실행하여 이전 코드를 테스트할 수 있습니다.

* F12 개발자 도구를 호출합니다.
* `https://localhost:5001/Sample/HeaderWithFactory`로 이동합니다.

F12 개발자 도구는 샘플 코드에 의해 추가된 다음 응답 헤더를 표시합니다.

* **작성자:**`Joe Smith`
* **globaladdheader:** `Result filter added to MvcOptions.Filters`
* **내부:**`My header`

이전 코드에서는 **internal:** `My header` 응답 헤더를 만듭니다.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>특성에서 구현된 IFilterFactory

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

`IFilterFactory`를 구현하는 필터는 다음과 같은 필터에 유용합니다.

* 매개 변수 전달을 필요로 하지 않는 필터.
* DI에 의해 채워져야 할 생성자 종속성이 있는 필터.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다. `IFilterFactory`은 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다. `CreateInstance`는 서비스 컨테이너(DI)에서 지정된 형식을 로드합니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

다음 코드는 `[SampleActionFilter]`을 적용하는 세 가지 방법을 보여 줍니다.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

이전 코드에서 `[SampleActionFilter]`로 메서드를 장식하는 것이 `SampleActionFilter`을 적용하는 데 선호되는 방법입니다.

## <a name="using-middleware-in-the-filter-pipeline"></a>필터 파이프라인에서 미들웨어 사용

리소스 필터는 파이프라인의 뒷부분에 제공되는 모든 실행을 둘러싼다는 점에서 [미들웨어](xref:fundamentals/middleware/index)처럼 작동합니다. 하지만 필터는 ASP.NET Core 런타임의 일부라는 점에서 미들웨어와 다릅니다. 즉, ASP.NET Core 컨텍스트 및 구문에 액세스할 수 있습니다.

미들웨어를 필터로 사용하려면 필터 파이프라인에 삽입할 미들웨어를 지정하는 `Configure` 메서드를 포함한 형식을 만듭니다. 다음 예제는 지역화 미들웨어를 사용하여 요청에 대한 현재 문화권을 설정합니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>를 사용하여 미들웨어를 실행합니다.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

미들웨어 필터는 모델 바인딩 이전 및 나머지 파이프라인 이후에 리소스 필터와 동일한 필터 파이프라인 단계에서 실행됩니다.

## <a name="next-actions"></a>다음 작업

* [ Razor 페이지에 대 한 필터 메서드를](xref:razor-pages/filter)참조 하세요.
* 필터를 실험하려면 [GitHub 샘플을 다운로드하고, 테스트하고, 수정](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)합니다.

::: moniker-end
