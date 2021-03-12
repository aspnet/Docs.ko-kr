---
title: ASP.NET Core에서 컨트롤러 논리 테스트
author: ardalis
description: ASP.NET Core에서 Moq 및 xUnit로 컨트롤러 논리를 테스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: mvc/controllers/testing
ms.openlocfilehash: 0f4fcceefdef946a5f29633c069cbb64ec345a3d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586958"
---
# <a name="unit-test-controller-logic-in-aspnet-core"></a>ASP.NET Core에서 컨트롤러 논리 단위 테스트

작성자: [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)는 인프라 및 종속성으로부터 격리된 상태에서 앱의 일부를 테스트하는 것입니다. 컨트롤러 논리를 유닛 테스트하는 경우 단일 작업의 콘텐츠만 테스트되고, 종속성 또는 프레임워크 자체의 동작은 테스트되지 않습니다.

## <a name="unit-testing-controllers"></a>컨트롤러 단위 테스트

컨트롤러의 동작에 초점을 맞춰 컨트롤러 동작의 단위 테스트를 설정합니다. 컨트롤러 단위 테스트는 [필터](xref:mvc/controllers/filters), [라우팅](xref:fundamentals/routing), [모델 바인딩](xref:mvc/models/model-binding) 같은 시나리오를 방지합니다. 전체적으로 요청에 응답하는 구성 요소 간 상호 작용을 포함하는 테스트는 ‘통합 테스트’에서 처리합니다. 통합 테스트에 대한 자세한 내용은 <xref:test/integration-tests>를 참조하세요.

사용자 지정 필터 및 경로를 작성할 때, 특정 컨트롤러 작업에 대한 테스트의 일부로서가 아니라, 별도로 단위 테스트를 수행하세요.

컨트롤러 단위 테스트를 시연하려면 예제 앱에서 다음 컨트롤러를 검토하세요. 

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

Home 컨트롤러는 브레인스토밍 세션 목록을 표시하고 POST 요청을 사용하여 새로운 브레인스토밍 세션을 만들 수 있습니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

이전 컨트롤러는 다음과 같습니다.

* [명시적 종속성 원칙](/dotnet/architecture/modern-web-apps-azure/architectural-principles#explicit-dependencies)을 따릅니다.
* [DI(종속성 주입)](xref:fundamentals/dependency-injection)에서 `IBrainstormSessionRepository`의 인스턴스를 제공할 것으로 기대합니다.
* [Moq](https://www.nuget.org/packages/Moq/)와 같은 모의 개체 프레임워크를 사용하여 모의 `IBrainstormSessionRepository` 서비스로 테스트할 수 있습니다. ‘모의 개체’는 테스트에 사용되는 사전 결정된 속성 및 메서드 동작 집합이 있는 제작된 개체입니다. 자세한 내용은 [통합 테스트 소개](xref:test/integration-tests#introduction-to-integration-tests)를 참조하세요.

`HTTP GET Index` 메서드는 반복 또는 분기가 없으며 한 가지 메서드만 호출합니다. 이 동작의 단위 테스트는 다음을 수행합니다.

* `GetTestSessions` 메서드를 사용하여 `IBrainstormSessionRepository` 서비스를 모방합니다. `GetTestSessions`는 날짜 및 세션 이름이 있는 두 개의 모의 브레인스토밍 세션을 작성합니다.
* `Index` 메서드를 실행합니다.
* 메서드가 반환한 결과에 대한 어설션을 작성합니다.
  * <xref:Microsoft.AspNetCore.Mvc.ViewResult>가 반환됩니다.
  * [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*)은 `StormSessionViewModel`입니다.
  * `ViewDataDictionary.Model`에 저장된 두 개의 브레인스토밍 세션이 있습니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Home 컨트롤러의 `HTTP POST Index` 메서드는 다음을 확인합니다.

* [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*)가 `false` 인 경우 작업 메서드가 적절한 데이터와 함께 *400 잘못된 요청* <xref:Microsoft.AspNetCore.Mvc.ViewResult>를 반환합니다.
* `ModelState.IsValid`가 `true`인 경우:
  * 리포지토리의 `Add` 메서드를 호출합니다.
  * <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult>가 올바른 인수와 함께 반환됩니다.

아래의 첫 번째 테스트처럼 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>로 오류를 추가하여 잘못된 모델 상태를 테스트할 수 있습니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)가 유효하지 않으면 GET 요청의 경우와 동일한 `ViewResult`가 반환됩니다. 이 테스트는 잘못된 모델을 전달하려고 시도하지 않습니다. 모델 바인딩이 실행되지 않기 때문에([통합 테스트](xref:test/integration-tests)는 모델 바인딩을 사용하는 반면), 잘못된 모델을 전달하는 것은 유효한 접근 방식이 아닙니다. 이번 경우에는 모델 바인딩을 테스트하지 않습니다. 이러한 단위 테스트는 작업 메서드의 코드만 테스트합니다.

두 번째 테스트는 `ModelState`가 유효한 시기를 확인합니다.

* 새 `BrainstormSession`이 (리포지토리를 통해) 추가됨.
* 메서드가 예상 속성과 함께 `RedirectToActionResult`를 반환함.

호출되지 않은 모의 호출은 일반적으로 무시되지만, 설정 호출의 끝부분에서 `Verifiable`을 호출하면 테스트에서 모의 유효성 검사가 가능합니다. 이는 `mockRepo.Verify` 호출을 통해 수행되며, 예상된 메서드가 호출되지 않았으면 테스트가 실패합니다.

> [!NOTE]
> 이 샘플에 사용된 Moq 라이브러리를 사용하면 확인 가능한 또는 “엄격한” 모의 개체를 확인 불가능한 모의 개체(“느슨한” 모의 개체 또는 스텁이라고도 함)와 혼합할 수 있습니다. [Moq를 사용하여 모의 동작 사용자 지정](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)에 대해 자세히 알아보세요.

샘플 앱의 [SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs)는 특정 브레인스토밍 세션과 관련된 정보를 표시합니다. 컨트롤러에는 잘못된 `id` 값(다음 예에는 이러한 시나리오를 다루는 두 개의 `return` 시나리오가 있음)을 처리하는 논리가 포함되어 있습니다. 마지막 `return` 문은 새 `StormSessionViewModel`을 보기 (*Controllers/SessionController.cs*)로 반환합니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

단위 테스트에는 세션 컨트롤러 `Index` 작업에 각 `return` 시나리오에 대한 하나의 테스트가 포함되어 있습니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Ideas 컨트롤러로 이동해보면 앱은 `api/ideas` 경로에서 기능을 웹 API로 노출합니다.

* 브레인스토밍 세션과 연관된 아이디어(`IdeaDTO`)의 목록이 `ForSession` 메서드에 의해 반환됩니다.
* `Create` 메서드는 세션에 새 아이디어를 추가합니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

API 호출을 통해 직접 비즈니스 도메인 엔터티를 반환하지 마세요. 도메인 엔터티는 다음과 같습니다.

* 종종 클라이언트에 필요한 것보다 많은 데이터를 포함합니다.
* 공개적으로 노출된 API와 앱의 내부 도메인 모델을 불필요하게 결합합니다.

도메인 엔터티와 클라이언트에 반환되는 형식 간의 매핑을 수행할 수 있습니다.

* 예제 앱에서 사용하는 것처럼 LINQ `Select`를 사용하여 수동으로 수행합니다. 자세한 내용은 [LINQ(Language-Integrated Query)](/dotnet/standard/using-linq)를 참조하세요.
* [AutoMapper](https://github.com/AutoMapper/AutoMapper)와 같은 라이브러리를 사용하여 자동으로 수행합니다.

다음으로 예제 앱은 Ideas 컨트롤러의 `Create` 및 `ForSession` API 메서드에 대한 단위 테스트를 보여줍니다.

예제 앱에는 두 개의 `ForSession` 테스트가 포함되어 있습니다. 첫 번째 테스트는 `ForSession`이 잘못된 세션에 대해 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>(HTTP를 찾을 수 없음)를 반환하는지 여부를 판별합니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

두 번째 `ForSession` 테스트는 `ForSession`에서 유효한 세션에 대한 세션 아이디어(`<List<IdeaDTO>>`) 목록을 반환하는지 여부를 판별합니다. 또한 이 검사는 첫 번째 아이디어를 검사하여 `Name` 속성이 올바른지도 확인합니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

`ModelState`가 유효하지 않을 때 `Create` 메서드의 동작을 테스트하기 위해 예제 앱은 테스트의 일부로 컨트롤러에 모델 오류를 추가합니다. 단위 테스트에서 모델 유효성 검사 또는 모델 바인딩을 테스트하지 말고, &mdash;잘못된 `ModelState`에 직면했을 때의 작업 메서드 동작만 테스트하세요.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

두 번째 `Create` 테스트는 `null`을 반환하는 리포지토리에 의존하므로 모의 리포지토리가 `null`을 반환하도록 구성됩니다. 테스트 데이터베이스를 만들고(메모리 내부에 또는 다른 위치에) 이 결과를 반환하는 쿼리를 작성할 필요가 없습니다. 예제 코드에서 볼 수 있는 것처럼 단일 명령문으로 테스트할 수 있습니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

세 번째 `Create` 테스트인 `Create_ReturnsNewlyCreatedIdeaForSession`에서는 리포지토리의 `UpdateAsync` 메서드가 호출되는지 확인합니다. `Verifiable`을 통해 모의 개체가 호출된 후 확인 가능한 메서드가 실행되었는지 확인하기 위해 모의 리포지토리의 `Verify` 메서드가 호출됩니다. `UpdateAsync` 메서드가 데이터를 저장하도록 보장하는 것은 단위 테스트의 책임이 아니며 통합 테스트로 수행할 수 있습니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>테스트 ActionResult\<T>

ASP.NET Core 2.1 이상에서 [ \<T> actionresult](xref:web-api/action-return-types#actionresultt-type) ()를 <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> 사용 하면에서 파생 된 형식을 반환 `ActionResult` 하거나 특정 형식을 반환할 수 있습니다.

샘플 앱에는 지정된 세션 `id`에 대한 `List<IdeaDTO>`를 반환하는 메서드가 포함되어 있습니다. 세션 `id`가 없으면 컨트롤러는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>를 반환합니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

`ForSessionActionResult` 컨트롤러에 대한 두 테스트는 `ApiIdeasControllerTests`에 포함되어 있습니다.

첫 번째 테스트는 컨트롤러가 `ActionResult`를 반환하지만 존재하지 않는 세션 `id`에 대한 존재하지 않는 아이디어 목록을 반환하는지 확인합니다.

* `ActionResult` 형식이 `ActionResult<List<IdeaDTO>>`입니다.
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*>는 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>임.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

유효한 세션 `id`에 대한 두 번째 테스트는 메서드가 다음을 반환하는지 확인합니다.

* `List<IdeaDTO>` 유형의 `ActionResult`.
* [Actionresult \<T> 입니다. 값](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) 은 `List<IdeaDTO>` 형식입니다.
* 목록의 첫 번째 항목은 모의 세션(`GetTestSession` 호출로 얻음)에 저장된 아이디어와 일치하는 유효한 아이디어임.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

예제 앱에는 지정된 세션에 대한 새 `Idea`를 작성하는 메서드도 포함되어 있습니다. 컨트롤러는 다음을 반환합니다.

* 잘못된 모델에 대한 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>(세션이 없는 경우).
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>(세션이 새 아이디어로 수정될 때).

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

세 개의 `CreateActionResult` 테스트는 `ApiIdeasControllerTests`에 포함되어 있습니다.

첫 번째 테스트는 잘못된 모델에 대해 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>가 반환되는지 확인합니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

두 번째 테스트는 세션이 존재하지 않는 경우 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>가 반환되는지 확인합니다.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

유효한 세션 `id`에 대한, 마지막 테스트는 다음을 확인합니다.

* 메서드가 `BrainstormSession` 유형의 `ActionResult`를 반환함.
* [Actionresult \<T> 입니다. 결과](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) 는 <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> 입니다. `CreatedAtActionResult`가 `Location` 헤더가 있는 *201 생성됨* 응답과 유사함.
* [Actionresult \<T> 입니다. 값](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) 은 `BrainstormSession` 형식입니다.
* 세션을 업데이트하기 위한 모의 호출 `UpdateAsync(testSession)`가 실행됨. `Verifiable` 메서드 호출은 어설션에서 `mockRepo.Verify()`를 실행하여 확인됩니다.
* 세션에 대해 두 개의 `Idea` 개체가 반환됩니다.
* 마지막 항목(`UpdateAsync`에 대한 모의 호출에 의해 추가된 `Idea`)이 테스트의 세션에 추가된 `newIdea`와 일치함.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[컨트롤러](xref:mvc/controllers/actions)는 임의의 ASP.NET Core MVC 앱에서 중심적인 역할을 수행합니다. 따라서 컨트롤러가 의도한 대로 동작한다고 확신할 수 있어야 합니다. 자동화된 테스트는 앱이 프로덕션 환경에 배포되기 전에 오류를 발견할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>컨트롤러 논리의 단위 테스트

[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)는 인프라 및 종속성으로부터 격리된 상태에서 앱의 일부를 테스트하는 것입니다. 컨트롤러 논리를 유닛 테스트하는 경우 단일 작업의 콘텐츠만 테스트되고, 종속성 또는 프레임워크 자체의 동작은 테스트되지 않습니다.

컨트롤러의 동작에 초점을 맞춰 컨트롤러 동작의 단위 테스트를 설정합니다. 컨트롤러 단위 테스트는 [필터](xref:mvc/controllers/filters), [라우팅](xref:fundamentals/routing), [모델 바인딩](xref:mvc/models/model-binding) 같은 시나리오를 방지합니다. 전체적으로 요청에 응답하는 구성 요소 간 상호 작용을 포함하는 테스트는 ‘통합 테스트’에서 처리합니다. 통합 테스트에 대한 자세한 내용은 <xref:test/integration-tests>를 참조하세요.

사용자 지정 필터 및 경로를 작성할 때, 특정 컨트롤러 작업에 대한 테스트의 일부로서가 아니라, 별도로 단위 테스트를 수행하세요.

컨트롤러 단위 테스트를 시연하려면 예제 앱에서 다음 컨트롤러를 검토하세요. Home 컨트롤러는 브레인스토밍 세션 목록을 표시하고 POST 요청을 사용하여 새로운 브레인스토밍 세션을 만들 수 있습니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

이전 컨트롤러는 다음과 같습니다.

* [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 따릅니다.
* [DI(종속성 주입)](xref:fundamentals/dependency-injection)에서 `IBrainstormSessionRepository`의 인스턴스를 제공할 것으로 기대합니다.
* [Moq](https://www.nuget.org/packages/Moq/)와 같은 모의 개체 프레임워크를 사용하여 모의 `IBrainstormSessionRepository` 서비스로 테스트할 수 있습니다. ‘모의 개체’는 테스트에 사용되는 사전 결정된 속성 및 메서드 동작 집합이 있는 제작된 개체입니다. 자세한 내용은 [통합 테스트 소개](xref:test/integration-tests#introduction-to-integration-tests)를 참조하세요.

`HTTP GET Index` 메서드는 반복 또는 분기가 없으며 한 가지 메서드만 호출합니다. 이 동작의 단위 테스트는 다음을 수행합니다.

* `GetTestSessions` 메서드를 사용하여 `IBrainstormSessionRepository` 서비스를 모방합니다. `GetTestSessions`는 날짜 및 세션 이름이 있는 두 개의 모의 브레인스토밍 세션을 작성합니다.
* `Index` 메서드를 실행합니다.
* 메서드가 반환한 결과에 대한 어설션을 작성합니다.
  * <xref:Microsoft.AspNetCore.Mvc.ViewResult>가 반환됩니다.
  * [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*)은 `StormSessionViewModel`입니다.
  * `ViewDataDictionary.Model`에 저장된 두 개의 브레인스토밍 세션이 있습니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Home 컨트롤러의 `HTTP POST Index` 메서드는 다음을 확인합니다.

* [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*)가 `false` 인 경우 작업 메서드가 적절한 데이터와 함께 *400 잘못된 요청* <xref:Microsoft.AspNetCore.Mvc.ViewResult>를 반환합니다.
* `ModelState.IsValid`가 `true`인 경우:
  * 리포지토리의 `Add` 메서드를 호출합니다.
  * <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult>가 올바른 인수와 함께 반환됩니다.

아래의 첫 번째 테스트처럼 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>로 오류를 추가하여 잘못된 모델 상태를 테스트할 수 있습니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)가 유효하지 않으면 GET 요청의 경우와 동일한 `ViewResult`가 반환됩니다. 이 테스트는 잘못된 모델을 전달하려고 시도하지 않습니다. 모델 바인딩이 실행되지 않기 때문에([통합 테스트](xref:test/integration-tests)는 모델 바인딩을 사용하는 반면), 잘못된 모델을 전달하는 것은 유효한 접근 방식이 아닙니다. 이번 경우에는 모델 바인딩을 테스트하지 않습니다. 이러한 단위 테스트는 작업 메서드의 코드만 테스트합니다.

두 번째 테스트는 `ModelState`가 유효한 시기를 확인합니다.

* 새 `BrainstormSession`이 (리포지토리를 통해) 추가됨.
* 메서드가 예상 속성과 함께 `RedirectToActionResult`를 반환함.

호출되지 않은 모의 호출은 일반적으로 무시되지만, 설정 호출의 끝부분에서 `Verifiable`을 호출하면 테스트에서 모의 유효성 검사가 가능합니다. 이는 `mockRepo.Verify` 호출을 통해 수행되며, 예상된 메서드가 호출되지 않았으면 테스트가 실패합니다.

> [!NOTE]
> 이 샘플에 사용된 Moq 라이브러리를 사용하면 확인 가능한 또는 “엄격한” 모의 개체를 확인 불가능한 모의 개체(“느슨한” 모의 개체 또는 스텁이라고도 함)와 혼합할 수 있습니다. [Moq를 사용하여 모의 동작 사용자 지정](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)에 대해 자세히 알아보세요.

샘플 앱의 [SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs)는 특정 브레인스토밍 세션과 관련된 정보를 표시합니다. 컨트롤러에는 잘못된 `id` 값(다음 예에는 이러한 시나리오를 다루는 두 개의 `return` 시나리오가 있음)을 처리하는 논리가 포함되어 있습니다. 마지막 `return` 문은 새 `StormSessionViewModel`을 보기 (*Controllers/SessionController.cs*)로 반환합니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

단위 테스트에는 세션 컨트롤러 `Index` 작업에 각 `return` 시나리오에 대한 하나의 테스트가 포함되어 있습니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Ideas 컨트롤러로 이동해보면 앱은 `api/ideas` 경로에서 기능을 웹 API로 노출합니다.

* 브레인스토밍 세션과 연관된 아이디어(`IdeaDTO`)의 목록이 `ForSession` 메서드에 의해 반환됩니다.
* `Create` 메서드는 세션에 새 아이디어를 추가합니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

API 호출을 통해 직접 비즈니스 도메인 엔터티를 반환하지 마세요. 도메인 엔터티는 다음과 같습니다.

* 종종 클라이언트에 필요한 것보다 많은 데이터를 포함합니다.
* 공개적으로 노출된 API와 앱의 내부 도메인 모델을 불필요하게 결합합니다.

도메인 엔터티와 클라이언트에 반환되는 형식 간의 매핑을 수행할 수 있습니다.

* 예제 앱에서 사용하는 것처럼 LINQ `Select`를 사용하여 수동으로 수행합니다. 자세한 내용은 [LINQ(Language-Integrated Query)](/dotnet/standard/using-linq)를 참조하세요.
* [AutoMapper](https://github.com/AutoMapper/AutoMapper)와 같은 라이브러리를 사용하여 자동으로 수행합니다.

다음으로 예제 앱은 Ideas 컨트롤러의 `Create` 및 `ForSession` API 메서드에 대한 단위 테스트를 보여줍니다.

예제 앱에는 두 개의 `ForSession` 테스트가 포함되어 있습니다. 첫 번째 테스트는 `ForSession`이 잘못된 세션에 대해 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>(HTTP를 찾을 수 없음)를 반환하는지 여부를 판별합니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

두 번째 `ForSession` 테스트는 `ForSession`에서 유효한 세션에 대한 세션 아이디어(`<List<IdeaDTO>>`) 목록을 반환하는지 여부를 판별합니다. 또한 이 검사는 첫 번째 아이디어를 검사하여 `Name` 속성이 올바른지도 확인합니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

`ModelState`가 유효하지 않을 때 `Create` 메서드의 동작을 테스트하기 위해 예제 앱은 테스트의 일부로 컨트롤러에 모델 오류를 추가합니다. 단위 테스트에서 모델 유효성 검사 또는 모델 바인딩을 테스트하지 말고, &mdash;잘못된 `ModelState`에 직면했을 때의 작업 메서드 동작만 테스트하세요.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

두 번째 `Create` 테스트는 `null`을 반환하는 리포지토리에 의존하므로 모의 리포지토리가 `null`을 반환하도록 구성됩니다. 테스트 데이터베이스를 만들고(메모리 내부에 또는 다른 위치에) 이 결과를 반환하는 쿼리를 작성할 필요가 없습니다. 예제 코드에서 볼 수 있는 것처럼 단일 명령문으로 테스트할 수 있습니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

세 번째 `Create` 테스트인 `Create_ReturnsNewlyCreatedIdeaForSession`에서는 리포지토리의 `UpdateAsync` 메서드가 호출되는지 확인합니다. `Verifiable`을 통해 모의 개체가 호출된 후 확인 가능한 메서드가 실행되었는지 확인하기 위해 모의 리포지토리의 `Verify` 메서드가 호출됩니다. `UpdateAsync` 메서드가 데이터를 저장하도록 보장하는 것은 단위 테스트의 책임이 아니며 통합 테스트로 수행할 수 있습니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>테스트 ActionResult\<T>

ASP.NET Core 2.1 이상에서 [ \<T> actionresult](xref:web-api/action-return-types#actionresultt-type) ()를 <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> 사용 하면에서 파생 된 형식을 반환 `ActionResult` 하거나 특정 형식을 반환할 수 있습니다.

샘플 앱에는 지정된 세션 `id`에 대한 `List<IdeaDTO>`를 반환하는 메서드가 포함되어 있습니다. 세션 `id`가 없으면 컨트롤러는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>를 반환합니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

`ForSessionActionResult` 컨트롤러에 대한 두 테스트는 `ApiIdeasControllerTests`에 포함되어 있습니다.

첫 번째 테스트는 컨트롤러가 `ActionResult`를 반환하지만 존재하지 않는 세션 `id`에 대한 존재하지 않는 아이디어 목록을 반환하는지 확인합니다.

* `ActionResult` 형식이 `ActionResult<List<IdeaDTO>>`입니다.
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*>는 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>임.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

유효한 세션 `id`에 대한 두 번째 테스트는 메서드가 다음을 반환하는지 확인합니다.

* `List<IdeaDTO>` 유형의 `ActionResult`.
* [Actionresult \<T> 입니다. 값](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) 은 `List<IdeaDTO>` 형식입니다.
* 목록의 첫 번째 항목은 모의 세션(`GetTestSession` 호출로 얻음)에 저장된 아이디어와 일치하는 유효한 아이디어임.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

예제 앱에는 지정된 세션에 대한 새 `Idea`를 작성하는 메서드도 포함되어 있습니다. 컨트롤러는 다음을 반환합니다.

* 잘못된 모델에 대한 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>(세션이 없는 경우).
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>(세션이 새 아이디어로 수정될 때).

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

세 개의 `CreateActionResult` 테스트는 `ApiIdeasControllerTests`에 포함되어 있습니다.

첫 번째 테스트는 잘못된 모델에 대해 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>가 반환되는지 확인합니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

두 번째 테스트는 세션이 존재하지 않는 경우 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>가 반환되는지 확인합니다.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

유효한 세션 `id`에 대한, 마지막 테스트는 다음을 확인합니다.

* 메서드가 `BrainstormSession` 유형의 `ActionResult`를 반환함.
* [Actionresult \<T> 입니다. 결과](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) 는 <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> 입니다. `CreatedAtActionResult`가 `Location` 헤더가 있는 *201 생성됨* 응답과 유사함.
* [Actionresult \<T> 입니다. 값](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) 은 `BrainstormSession` 형식입니다.
* 세션을 업데이트하기 위한 모의 호출 `UpdateAsync(testSession)`가 실행됨. `Verifiable` 메서드 호출은 어설션에서 `mockRepo.Verify()`를 실행하여 확인됩니다.
* 세션에 대해 두 개의 `Idea` 개체가 반환됩니다.
* 마지막 항목(`UpdateAsync`에 대한 모의 호출에 의해 추가된 `Idea`)이 테스트의 세션에 추가된 `newIdea`와 일치함.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* <xref:test/integration-tests>
* [Visual Studio를 사용 하 여 단위 테스트 만들기 및 실행](/visualstudio/test/unit-test-your-code)
* [AspNetCore-ASP.NET CORE mvc에 대 한 흐름 테스트 라이브러리](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): 강력한 형식의 단위 테스트 라이브러리로, mvc 및 web API 앱 테스트를 위한 흐름 인터페이스를 제공 합니다. (*Microsoft에서 유지 관리하거나 지원하지 않습니다.* )
* [JustMockLite](https://github.com/telerik/JustMockLite): .NET 개발자를 위한 모의 프레임워크입니다. (*Microsoft에서 유지 관리하거나 지원하지 않습니다.* )

