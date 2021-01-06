---
title: ASP.NET Core의 Razor Pages 단위 테스트
author: rick-anderson
description: Razor Pages 앱용 단위 테스트를 만드는 방법을 알아봅니다.
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
uid: test/razor-pages-tests
ms.openlocfilehash: 2486eb8c9fd0fc33ea77b0fedd99795218d7f4ca
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058040"
---
# <a name="no-locrazor-pages-unit-tests-in-aspnet-core"></a>ASP.NET Core의 Razor Pages 단위 테스트

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core는 Razor Pages 앱의 단위 테스트를 지원합니다. DAL(데이터 액세스 계층) 및 페이지 모델을 테스트하여 다음을 확인할 수 있습니다.

* Razor Pages 앱의 부분은 앱 생성 중에 별도로, 하나의 단위로 함께 작동합니다.
* 클래스 및 메서드의 책임 범위는 제한되어 있습니다.
* 앱이 동작하는 방식에 대한 추가 설명서가 있습니다.
* 코드 업데이트로 인해 오류가 발생하게 되는 회귀는 자동화된 빌드 및 배포 중에 발견됩니다.

이 항목에서는 Razor Pages 앱 및 단위 테스트에 대해 기본적으로 이해하고 있다고 가정합니다. Razor Pages 앱 또는 테스트 개념에 익숙하지 않은 경우 다음 항목을 참조하세요.

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [dotnet 테스트 및 xUnit을 사용하여 .NET Core에서 C# 단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 프로젝트는 다음 두 앱으로 구성됩니다.

| 앱         | 프로젝트 폴더                     | 설명 |
| ----------- | ---------------------------------- | ----------- |
| 메시지 앱 | *src/RazorPagesTestSample*         | 사용자가 메시지를 추가하고, 메시지 하나를 삭제하고, 모든 메시지를 삭제하고, 메시지를 분석할 수 있도록 허용합니다(메시지당 평균 단어 수 확인). |
| 앱 테스트    | *tests/RazorPagesTestSample.Tests* | 메시지 앱의 DAL 및 인덱스 페이지 모델을 단위 테스트하는 데 사용됩니다. |

[Visual Studio](/visualstudio/test/unit-test-your-code) 또는 [Mac용 Visual Studio](/dotnet/core/tutorials/using-on-mac-vs-full-solution)와 같은 IDE의 기본 제공 테스트 기능을 사용하여 테스트를 실행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용하는 경우 *tests/RazorPagesTestSample.Tests* 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>메시지 앱 구성

메시지 앱은 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.

* 앱의 인덱스 페이지(*Pages/Index.cshtml* 및 *Pages/Index.cshtml.cs*)는 메시지의 추가, 삭제 및 분석을 제어하기 위한 UI 및 페이지 모델 메서드를 제공합니다(메시지당 평균 단어 수 확인).
* 메시지는 `Id`(키) 및 `Text`(메시지)의 두 가지 속성을 사용하여 `Message` 클래스(*Data/Message.cs*)에서 설명됩니다. `Text` 속성은 필수이며 200자로 제한됩니다.
* 메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)를 사용하여 저장됩니다.
* 앱은 데이터베이스 컨텍스트 클래스 `AppDbContext`(*Data/AppDbContext*)에 DAL을 포함합니다. DAL 메서드는 `virtual`로 표시되므로 테스트에서 메서드를 모의로 사용해볼 수 있습니다.
* 앱 시작 시 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화됩니다. 이러한 *시드된 메시지* 는 테스트에서도 사용됩니다.

EF 항목 [InMemory로 테스트](/ef/core/miscellaneous/testing/in-memory)에서는 MSTest를 사용하여 테스트에 메모리 내 데이터베이스를 사용하는 방법을 설명합니다. 이 항목에서는 [xUnit](https://xunit.github.io/) 테스트 프레임워크를 사용합니다. 여러 테스트 프레임워크의 테스트 개념 및 테스트 구현은 비슷하지만 동일하지는 않습니다.

샘플 앱은 리포지토리 패턴을 사용하지 않고 [UoW(작업 단위) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예가 아니지만 Razor Pages는 이 개발 패턴을 지원합니다. 자세한 내용은 [인프라 지속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 <xref:mvc/controllers/testing>(샘플에서 리포지토리 패턴 구현)을 참조하세요.

## <a name="test-app-organization"></a>테스트 앱 구성

테스트 앱은 *tests/RazorPagesTestSample.Tests* 폴더에 있는 콘솔 앱입니다.

| 테스트 앱 폴더 | 설명 |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* 에는 DAL에 대한 단위 테스트가 포함되어 있습니다.</li><li>*IndexPageTests.cs* 에는 인덱스 페이지 모델에 대한 단위 테스트가 포함되어 있습니다.</li></ul> |
| *유틸리티*     | 데이터베이스가 각 테스트에 대한 기준 조건으로 다시 설정되도록 하기 위해 각 DAL 단위 테스트에 대한 새 데이터베이스 컨텍스트 옵션을 만드는 데 사용되는 `TestDbContextOptions` 메서드가 포함되어 있습니다. |

테스트 프레임워크는 [xUnit](https://xunit.github.io/)입니다. 개체 모의 프레임워크는 [Moq](https://github.com/moq/moq4)입니다.

## <a name="unit-tests-of-the-data-access-layer-dal"></a>DAL(데이터 액세스 계층)의 단위 테스트

메시지 앱의 `AppDbContext` 클래스(*src/RazorPagesTestSample/Data/AppDbContext.cs*)에는 4가지 메서드를 포함하는 DAL이 있습니다. 테스트 앱의 각 메서드에는 하나 또는 두 개의 단위 테스트가 있습니다.

| DAL 메서드               | 기능                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | `Text` 속성을 기준으로 정렬된 데이터베이스의 `List<Message>`를 가져옵니다. |
| `AddMessageAsync`        | 데이터베이스에 `Message`를 추가합니다.                                          |
| `DeleteAllMessagesAsync` | 데이터베이스에서 모든 `Message` 항목을 삭제합니다.                           |
| `DeleteMessageAsync`     | 데이터베이스에서 `Id`별로 단일 `Message`를 삭제합니다.                      |

각 테스트에 대해 새 `AppDbContext`를 만들 때 DAL의 단위 테스트에는 <xref:Microsoft.EntityFrameworkCore.DbContextOptions>가 필요합니다. 각 테스트에 대해 `DbContextOptions`를 만드는 한 가지 방법은 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>를 사용하는 것입니다.

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

이 방법의 문제는 이전 테스트가 어떤 상태이든 관계없이 각 테스트가 데이터베이스를 수신한다는 것입니다. 서로 방해하지 않는 원자 단위 테스트를 작성하려고 할 때 문제가 될 수 있습니다. `AppDbContext`에서 각 테스트에 대해 강제로 새 데이터베이스 컨텍스트를 사용하도록 하려면 새 서비스 공급자를 기준으로 하는 `DbContextOptions` 인스턴스를 제공합니다. 테스트 앱은 해당 `Utilities` 클래스 메서드 `TestDbContextOptions`(*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*)를 사용하여 이 작업을 수행하는 방법을 보여 줍니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

DAL 단위 테스트에서 `DbContextOptions`를 사용하면 각 테스트는 새 데이터베이스 인스턴스에서 원자 단위로 실행할 수 있습니다.

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

`DataAccessLayerTest` 클래스(*UnitTests/DataAccessLayerTest.cs*)의 각 테스트 메서드는 비슷한 정렬-실행-어설션 패턴을 따릅니다.

1. 정렬: 데이터베이스는 테스트를 위해 구성되고 예상된 결과가 정의됩니다.
1. 실행: 테스트가 실행됩니다.
1. 어설션: 테스트 결과가 성공인지 여부를 확인하기 위해 어설션이 수행됩니다.

예를 들어, `DeleteMessageAsync` 메서드는 해당 `Id`(*src/RazorPagesTestSample/Data/AppDbContext.cs*)로 식별되는 단일 메시지를 제거하는 일을 담당합니다.

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

이 메서드에 대한 2가지 테스트가 있습니다. 한 가지 테스트는 데이터베이스에 메시지가 있는 경우 메서드가 메시지를 삭제하는지 확인합니다. 다른 메서드는 삭제를 위해 메시지 `Id`가 존재하지 않는 경우 데이터베이스가 변경되지 않는지 테스트합니다. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 메서드는 다음과 같습니다.

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

첫째, 이 메서드는 실행 단계가 준비되는 정렬 단계를 수행합니다. 시드 메시지를 가져온 후 `seedMessages`에 보관합니다. 시드 메시지는 데이터베이스에 저장됩니다. `Id`가 `1`인 메시지는 삭제용으로 설정됩니다. `DeleteMessageAsync` 메서드가 실행될 때 예상되는 메시지에는 `Id`가 `1`인 경우를 제외한 모든 메시지가 있어야 합니다. `expectedMessages` 변수는 이 예상 결과를 나타냅니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

이 메서드는 다음과 같이 동작합니다. `DeleteMessageAsync` 메서드는 `recId` `1`를 전달하여 실행됩니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

마지막으로, 이 메서드는 컨텍스트에서 `Messages`를 가져온 다음, `expectedMessages`와 비교하여 두 항목이 같다고 어설션합니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

두 `List<Message>`가 동일한지 비교하기 위해 다음이 수행됩니다.

* 메시지는 `Id`별로 정렬됩니다.
* 메시지 쌍의 `Text` 속성이 비교됩니다.

유사한 테스트 메서드인 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`는 존재하지 않는 메시지를 삭제하려고 시도한 결과를 확인합니다. 이 경우 데이터베이스의 예상 메시지는 `DeleteMessageAsync` 메서드가 실행된 후의 실제 메시지와 같아야 합니다. 데이터베이스의 내용은 다음과 같이 변경되지 않아야 합니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>페이지 모델 메서드의 단위 테스트

다른 단위 테스트 세트는 페이지 모델 메서드의 테스트를 담당합니다. 메시지 앱에서 인덱스 페이지 모델은 *src/RazorPagesTestSample/Pages/Index.cshtml.cs* 의 `IndexModel` 클래스에 있습니다.

| 페이지 모델 메서드 | 기능 |
| ----------------- | -------- |
| `OnGetAsync` | `GetMessagesAsync` 메서드를 사용하여 UI에 대한 DAL에서 메시지를 가져옵니다. |
| `OnPostAddMessageAsync` | [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)가 유효한 경우 `AddMessageAsync`를 호출하여 데이터베이스에 메시지를 추가합니다. |
| `OnPostDeleteAllMessagesAsync` | `DeleteAllMessagesAsync`를 호출하여 데이터베이스의 모든 메시지를 삭제합니다. |
| `OnPostDeleteMessageAsync` | `DeleteMessageAsync`를 실행하여 지정된 `Id`의 메시지를 삭제합니다. |
| `OnPostAnalyzeMessagesAsync` | 하나 이상의 메시지가 데이터베이스에 있으면 메시지당 평균 단어 수를 계산합니다. |

페이지 모델 메서드는 `IndexPageTests` 클래스(*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*)의 7가지 테스트를 사용하여 테스트됩니다. 테스트는 친숙한 정렬-어셜선-실행 패턴을 사용합니다. 이러한 테스트는 다음에 중점을 둡니다.

* [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)가 잘못된 경우 메서드가 올바른 동작을 따르는지 확인합니다.
* 메서드를 확인하면 올바른 <xref:Microsoft.AspNetCore.Mvc.IActionResult>가 생성됩니다.
* 해당 속성 값 할당에 대한 확인이 올바르게 수행됩니다.

이 테스트 그룹은 종종 DAL 메서드의 모의 버전을 만들어 페이지 모델 메서드가 실행되는 실행 단계를 위한 예상 데이터를 생성합니다. 예를 들어, `AppDbContext`의 `GetMessagesAsync` 메서드의 모의 메서드를 만들어 출력을 생성합니다. 페이지 모델 메서드에서 이 메서드를 실행하면 모의 버전이 결과를 반환합니다. 이 데이터는 데이터베이스에서 제공되지 않습니다. 이렇게 하면 페이지 모델 테스트에서 DAL을 사용하기 위한 예측 가능하고 신뢰할 수 있는 테스트 조건이 생성됩니다.

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 테스트는 페이지 모델에 대한 `GetMessagesAsync` 메서드의 모의 버전을 생성하는 방법을 보여 줍니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

실행 단계에서 `OnGetAsync` 메서드를 수행하면 페이지 모델의 `GetMessagesAsync` 메서드가 호출됩니다.

단위 테스트 실행 단계(*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` 페이지 모델의 `OnGetAsync` 메서드(*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL의 `GetMessagesAsync` 메서드는 이 메서드 호출에 대한 결과를 반환하지 않습니다. 이 메서드의 모의 버전이 결과를 반환합니다.

`Assert` 단계에서 실제 메시지(`actualMessages`)가 페이지 모델의 `Messages` 속성에서 할당됩니다. 메시지가 할당될 때 형식 검사도 수행됩니다. 예상 및 실제 메시지는 해당 `Text` 속성과 비교됩니다. 이 테스트는 두 개의 `List<Message>` 인스턴스에 동일한 메시지가 포함되어 있음을 어설션합니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

이 그룹의 다른 테스트에서는 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, <xref:Microsoft.AspNetCore.Mvc.ActionContext>를 포함하는 페이지 모델 개체를 만들어 `PageContext`, `ViewDataDictionary` 및 `PageContext`를 설정합니다. 이러한 기능은 테스트를 수행하는 데 유용합니다. 예를 들어, 메시지 앱은 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>로 `ModelState` 오류를 설정하여 `OnPostAddMessageAsync`가 실행될 때 유효한 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>가 반환되는지 확인합니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>추가 자료

* [dotnet 테스트 및 xUnit을 사용하여 .NET Core에서 C# 단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [코드 단위 테스트](/visualstudio/test/unit-test-your-code)(Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Visual Studio for Mac을 사용하여 macOS에서 완전한 .NET Core 솔루션 빌드](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [xUnit.net 시작: .NET SDK 명령줄에서 .NET Core 사용](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq 빠른 시작](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core는 Razor Pages 앱의 단위 테스트를 지원합니다. DAL(데이터 액세스 계층) 및 페이지 모델을 테스트하여 다음을 확인할 수 있습니다.

* Razor Pages 앱의 부분은 앱 생성 중에 별도로, 하나의 단위로 함께 작동합니다.
* 클래스 및 메서드의 책임 범위는 제한되어 있습니다.
* 앱이 동작하는 방식에 대한 추가 설명서가 있습니다.
* 코드 업데이트로 인해 오류가 발생하게 되는 회귀는 자동화된 빌드 및 배포 중에 발견됩니다.

이 항목에서는 Razor Pages 앱 및 단위 테스트에 대해 기본적으로 이해하고 있다고 가정합니다. Razor Pages 앱 또는 테스트 개념에 익숙하지 않은 경우 다음 항목을 참조하세요.

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [dotnet 테스트 및 xUnit을 사용하여 .NET Core에서 C# 단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 프로젝트는 다음 두 앱으로 구성됩니다.

| 앱         | 프로젝트 폴더                     | 설명 |
| ----------- | ---------------------------------- | ----------- |
| 메시지 앱 | *src/RazorPagesTestSample*         | 사용자가 메시지를 추가하고, 메시지 하나를 삭제하고, 모든 메시지를 삭제하고, 메시지를 분석할 수 있도록 허용합니다(메시지당 평균 단어 수 확인). |
| 앱 테스트    | *tests/RazorPagesTestSample.Tests* | 메시지 앱의 DAL 및 인덱스 페이지 모델을 단위 테스트하는 데 사용됩니다. |

[Visual Studio](/visualstudio/test/unit-test-your-code) 또는 [Mac용 Visual Studio](/dotnet/core/tutorials/using-on-mac-vs-full-solution)와 같은 IDE의 기본 제공 테스트 기능을 사용하여 테스트를 실행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용하는 경우 *tests/RazorPagesTestSample.Tests* 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>메시지 앱 구성

메시지 앱은 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.

* 앱의 인덱스 페이지(*Pages/Index.cshtml* 및 *Pages/Index.cshtml.cs*)는 메시지의 추가, 삭제 및 분석을 제어하기 위한 UI 및 페이지 모델 메서드를 제공합니다(메시지당 평균 단어 수 확인).
* 메시지는 `Id`(키) 및 `Text`(메시지)의 두 가지 속성을 사용하여 `Message` 클래스(*Data/Message.cs*)에서 설명됩니다. `Text` 속성은 필수이며 200자로 제한됩니다.
* 메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)를 사용하여 저장됩니다.
* 앱은 데이터베이스 컨텍스트 클래스 `AppDbContext`(*Data/AppDbContext*)에 DAL을 포함합니다. DAL 메서드는 `virtual`로 표시되므로 테스트에서 메서드를 모의로 사용해볼 수 있습니다.
* 앱 시작 시 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화됩니다. 이러한 *시드된 메시지* 는 테스트에서도 사용됩니다.

EF 항목 [InMemory로 테스트](/ef/core/miscellaneous/testing/in-memory)에서는 MSTest를 사용하여 테스트에 메모리 내 데이터베이스를 사용하는 방법을 설명합니다. 이 항목에서는 [xUnit](https://xunit.github.io/) 테스트 프레임워크를 사용합니다. 여러 테스트 프레임워크의 테스트 개념 및 테스트 구현은 비슷하지만 동일하지는 않습니다.

샘플 앱은 리포지토리 패턴을 사용하지 않고 [UoW(작업 단위) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예가 아니지만 Razor Pages는 이 개발 패턴을 지원합니다. 자세한 내용은 [인프라 지속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 <xref:mvc/controllers/testing>(샘플에서 리포지토리 패턴 구현)을 참조하세요.

## <a name="test-app-organization"></a>테스트 앱 구성

테스트 앱은 *tests/RazorPagesTestSample.Tests* 폴더에 있는 콘솔 앱입니다.

| 테스트 앱 폴더 | 설명 |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* 에는 DAL에 대한 단위 테스트가 포함되어 있습니다.</li><li>*IndexPageTests.cs* 에는 인덱스 페이지 모델에 대한 단위 테스트가 포함되어 있습니다.</li></ul> |
| *유틸리티*     | 데이터베이스가 각 테스트에 대한 기준 조건으로 다시 설정되도록 하기 위해 각 DAL 단위 테스트에 대한 새 데이터베이스 컨텍스트 옵션을 만드는 데 사용되는 `TestDbContextOptions` 메서드가 포함되어 있습니다. |

테스트 프레임워크는 [xUnit](https://xunit.github.io/)입니다. 개체 모의 프레임워크는 [Moq](https://github.com/moq/moq4)입니다.

## <a name="unit-tests-of-the-data-access-layer-dal"></a>DAL(데이터 액세스 계층)의 단위 테스트

메시지 앱의 `AppDbContext` 클래스(*src/RazorPagesTestSample/Data/AppDbContext.cs*)에는 4가지 메서드를 포함하는 DAL이 있습니다. 테스트 앱의 각 메서드에는 하나 또는 두 개의 단위 테스트가 있습니다.

| DAL 메서드               | 기능                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | `Text` 속성을 기준으로 정렬된 데이터베이스의 `List<Message>`를 가져옵니다. |
| `AddMessageAsync`        | 데이터베이스에 `Message`를 추가합니다.                                          |
| `DeleteAllMessagesAsync` | 데이터베이스에서 모든 `Message` 항목을 삭제합니다.                           |
| `DeleteMessageAsync`     | 데이터베이스에서 `Id`별로 단일 `Message`를 삭제합니다.                      |

각 테스트에 대해 새 `AppDbContext`를 만들 때 DAL의 단위 테스트에는 <xref:Microsoft.EntityFrameworkCore.DbContextOptions>가 필요합니다. 각 테스트에 대해 `DbContextOptions`를 만드는 한 가지 방법은 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>를 사용하는 것입니다.

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

이 방법의 문제는 이전 테스트가 어떤 상태이든 관계없이 각 테스트가 데이터베이스를 수신한다는 것입니다. 서로 방해하지 않는 원자 단위 테스트를 작성하려고 할 때 문제가 될 수 있습니다. `AppDbContext`에서 각 테스트에 대해 강제로 새 데이터베이스 컨텍스트를 사용하도록 하려면 새 서비스 공급자를 기준으로 하는 `DbContextOptions` 인스턴스를 제공합니다. 테스트 앱은 해당 `Utilities` 클래스 메서드 `TestDbContextOptions`(*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*)를 사용하여 이 작업을 수행하는 방법을 보여 줍니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

DAL 단위 테스트에서 `DbContextOptions`를 사용하면 각 테스트는 새 데이터베이스 인스턴스에서 원자 단위로 실행할 수 있습니다.

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

`DataAccessLayerTest` 클래스(*UnitTests/DataAccessLayerTest.cs*)의 각 테스트 메서드는 비슷한 정렬-실행-어설션 패턴을 따릅니다.

1. 정렬: 데이터베이스는 테스트를 위해 구성되고 예상된 결과가 정의됩니다.
1. 실행: 테스트가 실행됩니다.
1. 어설션: 테스트 결과가 성공인지 여부를 확인하기 위해 어설션이 수행됩니다.

예를 들어, `DeleteMessageAsync` 메서드는 해당 `Id`(*src/RazorPagesTestSample/Data/AppDbContext.cs*)로 식별되는 단일 메시지를 제거하는 일을 담당합니다.

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

이 메서드에 대한 2가지 테스트가 있습니다. 한 가지 테스트는 데이터베이스에 메시지가 있는 경우 메서드가 메시지를 삭제하는지 확인합니다. 다른 메서드는 삭제를 위해 메시지 `Id`가 존재하지 않는 경우 데이터베이스가 변경되지 않는지 테스트합니다. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 메서드는 다음과 같습니다.

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

첫째, 이 메서드는 실행 단계가 준비되는 정렬 단계를 수행합니다. 시드 메시지를 가져온 후 `seedMessages`에 보관합니다. 시드 메시지는 데이터베이스에 저장됩니다. `Id`가 `1`인 메시지는 삭제용으로 설정됩니다. `DeleteMessageAsync` 메서드가 실행될 때 예상되는 메시지에는 `Id`가 `1`인 경우를 제외한 모든 메시지가 있어야 합니다. `expectedMessages` 변수는 이 예상 결과를 나타냅니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

이 메서드는 다음과 같이 동작합니다. `DeleteMessageAsync` 메서드는 `recId` `1`를 전달하여 실행됩니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

마지막으로, 이 메서드는 컨텍스트에서 `Messages`를 가져온 다음, `expectedMessages`와 비교하여 두 항목이 같다고 어설션합니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

두 `List<Message>`가 동일한지 비교하기 위해 다음이 수행됩니다.

* 메시지는 `Id`별로 정렬됩니다.
* 메시지 쌍의 `Text` 속성이 비교됩니다.

유사한 테스트 메서드인 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`는 존재하지 않는 메시지를 삭제하려고 시도한 결과를 확인합니다. 이 경우 데이터베이스의 예상 메시지는 `DeleteMessageAsync` 메서드가 실행된 후의 실제 메시지와 같아야 합니다. 데이터베이스의 내용은 다음과 같이 변경되지 않아야 합니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>페이지 모델 메서드의 단위 테스트

다른 단위 테스트 세트는 페이지 모델 메서드의 테스트를 담당합니다. 메시지 앱에서 인덱스 페이지 모델은 *src/RazorPagesTestSample/Pages/Index.cshtml.cs* 의 `IndexModel` 클래스에 있습니다.

| 페이지 모델 메서드 | 기능 |
| ----------------- | -------- |
| `OnGetAsync` | `GetMessagesAsync` 메서드를 사용하여 UI에 대한 DAL에서 메시지를 가져옵니다. |
| `OnPostAddMessageAsync` | [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)가 유효한 경우 `AddMessageAsync`를 호출하여 데이터베이스에 메시지를 추가합니다. |
| `OnPostDeleteAllMessagesAsync` | `DeleteAllMessagesAsync`를 호출하여 데이터베이스의 모든 메시지를 삭제합니다. |
| `OnPostDeleteMessageAsync` | `DeleteMessageAsync`를 실행하여 지정된 `Id`의 메시지를 삭제합니다. |
| `OnPostAnalyzeMessagesAsync` | 하나 이상의 메시지가 데이터베이스에 있으면 메시지당 평균 단어 수를 계산합니다. |

페이지 모델 메서드는 `IndexPageTests` 클래스(*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*)의 7가지 테스트를 사용하여 테스트됩니다. 테스트는 친숙한 정렬-어셜선-실행 패턴을 사용합니다. 이러한 테스트는 다음에 중점을 둡니다.

* [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)가 잘못된 경우 메서드가 올바른 동작을 따르는지 확인합니다.
* 메서드를 확인하면 올바른 <xref:Microsoft.AspNetCore.Mvc.IActionResult>가 생성됩니다.
* 해당 속성 값 할당에 대한 확인이 올바르게 수행됩니다.

이 테스트 그룹은 종종 DAL 메서드의 모의 버전을 만들어 페이지 모델 메서드가 실행되는 실행 단계를 위한 예상 데이터를 생성합니다. 예를 들어, `AppDbContext`의 `GetMessagesAsync` 메서드의 모의 메서드를 만들어 출력을 생성합니다. 페이지 모델 메서드에서 이 메서드를 실행하면 모의 버전이 결과를 반환합니다. 이 데이터는 데이터베이스에서 제공되지 않습니다. 이렇게 하면 페이지 모델 테스트에서 DAL을 사용하기 위한 예측 가능하고 신뢰할 수 있는 테스트 조건이 생성됩니다.

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 테스트는 페이지 모델에 대한 `GetMessagesAsync` 메서드의 모의 버전을 생성하는 방법을 보여 줍니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

실행 단계에서 `OnGetAsync` 메서드를 수행하면 페이지 모델의 `GetMessagesAsync` 메서드가 호출됩니다.

단위 테스트 실행 단계(*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` 페이지 모델의 `OnGetAsync` 메서드(*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL의 `GetMessagesAsync` 메서드는 이 메서드 호출에 대한 결과를 반환하지 않습니다. 이 메서드의 모의 버전이 결과를 반환합니다.

`Assert` 단계에서 실제 메시지(`actualMessages`)가 페이지 모델의 `Messages` 속성에서 할당됩니다. 메시지가 할당될 때 형식 검사도 수행됩니다. 예상 및 실제 메시지는 해당 `Text` 속성과 비교됩니다. 이 테스트는 두 개의 `List<Message>` 인스턴스에 동일한 메시지가 포함되어 있음을 어설션합니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

이 그룹의 다른 테스트에서는 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, <xref:Microsoft.AspNetCore.Mvc.ActionContext>를 포함하는 페이지 모델 개체를 만들어 `PageContext`, `ViewDataDictionary` 및 `PageContext`를 설정합니다. 이러한 기능은 테스트를 수행하는 데 유용합니다. 예를 들어, 메시지 앱은 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>로 `ModelState` 오류를 설정하여 `OnPostAddMessageAsync`가 실행될 때 유효한 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>가 반환되는지 확인합니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>추가 자료

* [dotnet 테스트 및 xUnit을 사용하여 .NET Core에서 C# 단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [코드 단위 테스트](/visualstudio/test/unit-test-your-code)(Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Visual Studio for Mac을 사용하여 macOS에서 완전한 .NET Core 솔루션 빌드](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [xUnit.net 시작: .NET SDK 명령줄에서 .NET Core 사용](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq 빠른 시작](https://github.com/Moq/moq4/wiki/Quickstart)
* [JustMockLite](https://github.com/telerik/JustMockLite): .NET 개발자를 위한 모의 프레임워크입니다. (*Microsoft에서 유지 관리하거나 지원하지 않습니다.* )

::: moniker-end
