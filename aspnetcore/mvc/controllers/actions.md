---
title: ASP.NET Core MVC에서 컨트롤러를 사용한 요청 처리
author: ardalis
description: ''
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
uid: mvc/controllers/actions
ms.openlocfilehash: a9319e74d0213b178c2a71be69a0332270d9446c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061459"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a>ASP.NET Core MVC에서 컨트롤러를 사용한 요청 처리

작성자: [Steve Smith](https://ardalis.com/) 및 [Scott Addie](https://github.com/scottaddie)

컨트롤러, 작업 및 작업 결과는 개발자가 ASP.NET Core MVC를 사용하여 앱을 구축하는 방법의 기본적인 부분입니다.

## <a name="what-is-a-controller"></a>컨트롤러란?

컨트롤러는 일련의 작업을 정의하고 그룹화하는 데 사용됩니다. 작업(또는 *작업 메서드* )은 요청을 처리하는 컨트롤러의 메서드입니다. 컨트롤러는 논리적으로 유사한 작업을 함께 그룹화합니다. 이 작업의 집합체를 통해 라우팅, 캐싱 및 권한 부여와 같은 일반적인 규칙 모음을 일괄적으로 적용할 수 있습니다. 요청은 [라우팅](xref:mvc/controllers/routing)을 통해 작업에 매핑됩니다.

규칙에 따라 컨트롤러 클래스는:

* 프로젝트의 루트 수준 *Controllers* 폴더에 위치합니다.
* `Microsoft.AspNetCore.Mvc.Controller`에서 상속합니다.

컨트롤러는 다음 조건 중 하나 이상이 참인 인스턴스화할 수 있는 클래스입니다.

* 클래스 이름에 접미사 `Controller`가 붙습니다.
* 클래스가 이름에 접미사 `Controller`가 붙는 클래스를 상속합니다.
* `[Controller]` 특성이 클래스에 적용됩니다.

컨트롤러 클래스에는 연결된 `[NonController]` 특성이 없어야만 합니다.

컨트롤러는 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 따라야 합니다. 이 원칙을 구현하는 방법에는 몇 가지가 있습니다. 여러 컨트롤러 작업에서 동일한 서비스가 필요한 경우 해당 종속성을 요청하는 데 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 사용하는 것을 고려합니다. 서비스가 단일 작업 메서드에서만 필요한 경우에는 종속성을 요청하는 데 [작업 주입](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)의 사용을 고려하세요.

**M** odel- **V** iew- **C** ontroller(모델-뷰-컨트롤러) 패턴에서 컨트롤러는 초기 요청 처리 및 모델의 인스턴스화를 담당합니다. 일반적으로 비즈니스 의사 결정은 모델 내에서 수행되어야 합니다.

컨트롤러는 모델의 처리 결과(있는 경우)를 사용하고, 적절한 보기와 관련된 보기 데이터 또는 API 호출의 결과를 반환합니다. [ASP.NET Core MVC 개요](xref:mvc/overview) 및 [ASP.NET Core MVC 및 Visual Studio 시작](xref:tutorials/first-mvc-app/start-mvc)에서 자세히 알아봅니다.

컨트롤러는 *UI 수준* 추상화입니다. 컨트롤러의 책임은 요청 데이터가 올바른지 확인하고 어떤 보기(또는 API에 대한 결과)를 반환해야 하는지 선택하는 것입니다. 잘 구성된 앱에서는 데이터 액세스 또는 비즈니스 논리를 컨트롤러에 직접 포함하지 않습니다. 대신, 컨트롤러는 이러한 책임을 처리하는 서비스에 위임합니다.

## <a name="defining-actions"></a>작업 정의

컨트롤러의 공용 메서드는 `[NonAction]` 특성이 있는 경우 이외에는 작업입니다. 작업의 매개 변수는 요청 데이터에 바인딩되며 [모델 바인딩](xref:mvc/models/model-binding)을 사용하여 유효성 검사가 수행됩니다. 모델 유효성 검사는 모델 바인딩되는 모든 작업에 대해 발생합니다. `ModelState.IsValid` 속성 값은 모델 바인딩 및 유효성 검사의 성공 여부를 나타냅니다.

작업 메서드는 비즈니스 문제에 요청을 매핑하기 위한 논리를 포함해야 합니다. 비즈니스 문제는 일반적으로 컨트롤러가 [종속성 주입](xref:mvc/controllers/dependency-injection)을 통해 액세스하는 서비스로 표현되어야 합니다. 그런 다음 작업은 비즈니스 작업의 결과를 애플리케이션 상태에 매핑합니다.

작업은 무엇이든 반환할 수 있지만, 흔히 응답을 생성하는 `IActionResult`(또는 비동기 메서드에 대한 `Task<IActionResult>`)의 인스턴스를 반환합니다. 작업 메서드는 *응답의 종류* 를 선택해야 합니다. 작업 결과는 *응답을 수행* 합니다.

### <a name="controller-helper-methods"></a>컨트롤러 도우미 메서드

컨트롤러는 일반적으로 [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller)에서 상속합니다. 단, 이는 필수가 아닙니다. `Controller`에서의 파생은 도우미 메서드의 세 범주에 대한 액세스를 제공합니다.

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. 메서드는 빈 응답 본문을 생성 합니다.

응답 본문에 설명할 콘텐츠가 없으므로 `Content-Type` HTTP 응답 헤더가 포함되지 않습니다.

이 범주 내에는 리디렉션 및 HTTP 상태 코드의 두 가지 결과 형식이 있습니다.

* **HTTP 상태 코드**

    이 형식은 HTTP 상태 코드를 반환합니다. 이러한 형식의 몇 가지 도우미 메서드는 `BadRequest`, `NotFound` 및 `Ok`입니다. 예를 들어 `return BadRequest();`가 실행되면 400 상태 코드를 생성합니다. `BadRequest`, `NotFound` 및 `Ok`와 같은 메서드가 오버로드되는 경우 콘텐츠 협상이 수행되므로 더 이상 HTTP 상태 코드 응답자로서의 자격이 없습니다.

* **리디렉션**

    이 형식은 작업 또는 대상에 리디렉션을 반환합니다(`Redirect`, `LocalRedirect`, `RedirectToAction` 또는 `RedirectToRoute` 를 사용하여). 예를 들어 `return RedirectToAction("Complete", new {id = 123});`은 `Complete`로 리디렉션하고 익명 개체를 전달합니다.

    리디렉션 결과 형식은 주로 `Location` HTTP 응답 헤더와 더불어 HTTP 상태 코드 형식에 따라 다릅니다.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. 메서드를 사용 하면 미리 정의 된 콘텐츠 형식을 사용 하는 비어 있지 않은 응답 본문이 생성 됩니다.

이 범주의 도우미 메서드 대부분은 `ContentType` 속성을 포함하므로 응답 본문을 설명하도록 `Content-Type` 응답 헤더를 설정할 수 있습니다.

이 범주 내의 결과 형식은 [보기](xref:mvc/views/overview) 및 [서식 있는 응답](xref:web-api/advanced/formatting)의 두 가지가 있습니다.

* **보기**

    이 형식은 HTML을 렌더링하는 모델을 사용하는 보기를 반환합니다. 예를 들어 `return View(customer);`는 보기에 데이터 바인딩을 위한 모델을 전달합니다.

* **서식화된 응답**

    이 형식은 JSON 또는 비슷한 데이터 교환 형식을 반환하여 개체를 특정 방식으로 표시합니다. 예를 들어 `return Json(customer);`은 JSON 형식에 제공된 개체를 직렬화합니다.
    
    이 형식의 다른 일반적인 메서드에는 `File` 및 `PhysicalFile`이 포함됩니다. 예를 들어 `return PhysicalFile(customerFilePath, "text/xml");`은 [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult)를 반환합니다.

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. 메서드는 클라이언트와 협상 된 콘텐츠 형식으로 비어 있지 않은 응답 본문의 형식을 지정 합니다.

이 범주는 **콘텐츠 협상** 으로 더 잘 알려져 있습니다. [콘텐츠 협상](xref:web-api/advanced/formatting#content-negotiation)은 작업이 [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) 형식 또는 [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) 구현 이외의 형식을 반환할 때마다 적용됩니다. 비 `IActionResult` 구현을 반환하는 작업(예: `object`)도 서식화된 응답을 반환합니다.

이 형식의 몇 가지 도우미 메서드에는 `BadRequest`, `CreatedAtRoute` 및 `Ok`가 포함됩니다. 이러한 메서드의 예로는 각각 `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);` 및 `return Ok(value);`가 있습니다. `BadRequest` 및 `Ok`는 값이 전달될 때만 콘텐츠 협상을 수행합니다. 값을 전달하지 않으면 대신 HTTP 상태 코드 결과 형식을 제공합니다. 반면, `CreatedAtRoute` 메서드는 해당 오버로드에서 모두 값이 전달되어야 하므로 항상 콘텐츠 협상을 수행합니다.

### <a name="cross-cutting-concerns"></a>횡단 관심사

응용 프로그램은 일반적으로 해당 워크플로의 일부를 공유합니다. 예를 들어 쇼핑 카트 액세스를 위해 인증이 필요한 앱이나 일부 페이지에서 데이터를 캐시하는 앱이 있습니다. 작업 메서드 전후로 논리를 수행하려면 *필터* 를 사용합니다. 횡단 관심사에서 [필터](xref:mvc/controllers/filters)를 사용하면 중복을 줄일 수 있습니다.

`[Authorize]`와 같은 대부분의 필터 특성은 원하는 세분성 수준에 따라 컨트롤러 또는 작업 수준에 적용할 수 있습니다.

오류 처리 및 응답 캐시는 종종 교차 편집 문제입니다.
* [오류 처리](xref:mvc/controllers/filters#exception-filters)
* [응답 캐싱](xref:performance/caching/response)

많은 교차 편집 문제는 필터 또는 사용자 지정 [미들웨어](xref:fundamentals/middleware/index)를 사용하여 처리할 수 있습니다.
