---
title: ASP.NET Core에서 구성 요소 테스트Blazor
author: guardrex
description: Blazor 앱에서 구성 요소를 테스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: cd4aee66fd6df6cc0ce520d8ca66e0a2cf130eff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054868"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a>ASP.NET Core에서 구성 요소 테스트Blazor

[Egil Hansen](https://egilhansen.com/)

테스트는 안정적이고 유지 관리 가능한 소프트웨어를 구축하는 데 있어 중요한 측면입니다.

Blazor 구성 요소를 테스트하기 위해 CUT(‘테스트 중인 구성 요소’)는 다음과 같습니다.

* 테스트에 관련된 입력으로 렌더링됩니다.
* 수행되는 테스트 형식에 따라 상호 작용 또는 수정이 적용될 수 있습니다. 예를 들어 단추에 대한 `onclick` 이벤트와 같은 이벤트 처리기를 트리거할 수 있습니다.
* 예상 값이 검사되었습니다.

## <a name="test-approaches"></a>테스트 접근 방식

Blazor 구성 요소를 테스트하는 두 가지 일반적인 접근 방식은 E2E(엔드투엔드) 테스트와 유닛 테스트입니다.

* **유닛 테스트** : [단위 테스트](/dotnet/core/testing/)는 다음을 제공하는 유닛 테스트 라이브러리를 사용하여 작성됩니다.
  * 구성 요소 렌더링
  * 구성 요소 출력 및 상태 검사
  * 이벤트 처리기 및 수명 주기 메서드 트리거
  * 구성 요소 동작이 올바른 어설션

  [bUnit](https://github.com/egil/bUnit)은 Razor 구성 요소 유닛 테스트를 사용하도록 지정하는 라이브러리의 예입니다.

* **E2E 테스트** : Test Runner는 CUT를 포함하는 Blazor 앱을 실행하고 브라우저 인스턴스를 자동화합니다. 테스트 도구는 브라우저를 통해 CUT를 검사하고 상호 작용합니다. [Selenium](https://github.com/SeleniumHQ/selenium)은 Blazor 앱에서 사용할 수 있는 E2E 테스트 프레임워크의 예입니다.

유닛 테스트에는 Blazor 구성 요소(Razor/C#)만 포함됩니다. 서비스 및 JS interop과 같은 외부 종속성은 모의되어야 합니다. E2E 테스트에서 Blazor 구성 요소와 모든 보조 인프라는 CSS, JS, DOM, 브라우저 API를 비롯한 테스트의 일부입니다.

‘테스트 범위’에서는 테스트의 범위를 설명합니다. 테스트 범위는 일반적으로 테스트의 속도에 영향을 줍니다. 단위 테스트는 앱 하위 시스템의 하위 집합에서 실행되며 일반적으로 몇 밀리초 내에 실행됩니다. 광범위한 앱 하위 시스템 그룹을 테스트하는 E2E 테스트는 완료하는 데 몇 초 정도 걸릴 수 있습니다. 

또한 유닛 테스트를 사용하면 CUT의 인스턴스에 액세스하여 구성 요소의 내부 상태를 검사 및 확인할 수 있습니다. 일반적으로 E2E 테스트에서는 이렇게 할 수 없습니다.

구성 요소의 환경과 관련하여 E2E 테스트에서는 확인이 시작되기 전에 필요한 환경 상태에 도달했는지 확인해야 합니다. 그렇지 않으면 결과를 예측할 수 없습니다. 유닛 테스트에서는 CUT 렌더링과 테스트의 수명 주기가 통합되어 테스트 안정성이 향상됩니다.

E2E 테스트는 여러 프로세스, 네트워크 및 디스크 I/O, 기타 하위 시스템 작업을 포함하여 테스트 안정성이 저하되는 경우가 있습니다. 단위 테스트는 일반적으로 이러한 종류의 문제로부터 보호됩니다.

다음 표에서는 두 테스트 접근 방식의 차이점을 요약하여 보여 줍니다.

| 기능                       | 단위 테스트                     | E2E 테스트                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| 테스트 범위                       | Blazor 구성 요소(Razor/C#)만 | Blazor 구성 요소(Razor/C#) 및 CSS/JS |
| 테스트 실행 시간              | 밀리초                     | 초                                 |
| 구성 요소 인스턴스에 대한 액세스 | 예                              | 아니요                                      |
| 환경에 민감     | 아니요                               | 예                                     |
| 안정성                      | 안정성 향상                    | 안정성 감소                           |

## <a name="choose-the-most-appropriate-test-approach"></a>가장 적절한 테스트 접근 방식 선택

수행할 테스트 형식을 선택할 때 시나리오를 고려하세요. 다음 표에서는 몇 가지 고려 사항을 설명합니다.

| 시나리오 | 제안된 방법 | 설명 |
| -------- | ------------------ | ------- |
| JS interop 논리가 없는 구성 요소 | 단위 테스트 | Blazor 구성 요소에서 JS interop에 대한 종속성이 없는 경우 JS 또는 DOM API에 액세스하지 않고 구성 요소를 테스트할 수 있습니다. 이 시나리오에서는 유닛 테스트를 선택하는 것이 좋습니다. |
| 간단한 JS interop 논리를 포함하는 구성 요소 | 단위 테스트 | 구성 요소에서 DOM을 쿼리하거나 JS interop을 통해 애니메이션을 트리거하는 것이 일반적입니다. <xref:Microsoft.JSInterop.IJSRuntime> 인터페이스를 통해 JS를 모의하는 것이 간단하므로 이 시나리오에서는 일반적으로 유닛 테스트를 사용하는 것이 좋습니다. |
| 복잡한 JS 코드에 종속되는 구성 요소 | 유닛 테스트 및 개별 JS 테스트 | 구성 요소에서 JS interop을 사용하여 크거나 복잡한 JS 라이브러리를 호출하지만 Blazor 구성 요소와 JS 라이브러리 간의 상호 작용이 간단한 경우, 구성 요소와 JS 라이브러리 또는 코드를 두 개별 부분으로 처리하고 각 부분을 개별적으로 테스트하는 것이 가장 좋은 접근 방식입니다. 유닛 테스트 라이브러리를 사용하여 Blazor 구성 요소를 테스트하고 JS 테스트 라이브러리를 사용하여 JS를 테스트합니다. |
| 브라우저 DOM의 JS 조작에 종속되는 논리를 포함하는 구성 요소 | E2E 테스트 | 구성 요소의 기능이 JS와 해당 DOM 조작에 종속되는 경우 E2E 테스트에서 JS와 Blazor 코드를 함께 확인합니다. 이 방법은 Blazor 프레임워크 개발자가 긴밀하게 결합된 C# 및 JS 코드를 포함하는 Blazor의 브라우저 렌더링 논리를 사용하여 수행하는 방법입니다. C# 및 JS 코드는 함께 작동하여 브라우저에서 Blazor 구성 요소를 올바르게 렌더링해야 합니다.
| 모의하기 어려운 종속 항목을 포함하며 타사 구성 요소 라이브러리에 종속되는 구성 요소 | E2E 테스트 | 구성 요소의 기능이 JS interop과 같은 모의하기 어려운 종속 항목을 포함하는 타사 구성 요소 라이브러리에 종속되는 경우 E2E 테스트는 구성 요소를 테스트하는 유일한 옵션일 수 있습니다. |

## <a name="test-components-with-bunit"></a>bUnit을 사용하여 구성 요소 테스트

Blazor에 대한 공식적인 Microsoft 테스트 프레임워크는 없지만 커뮤니티 기반 프로젝트 [bUnit](https://github.com/egil/bUnit)은 Blazor 구성 요소를 단위 테스트하는 편리한 방법을 제공합니다.

> [!NOTE]
> bUnit은 타사 테스트 라이브러리이며 Microsoft에서 지원하거나 유지 관리하지 않습니다.

bUnit은 [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), [xUnit](https://xunit.github.io/)과 같은 범용 테스트 프레임워크에서 작동합니다. 이러한 테스트 프레임워크는 bUnit 테스트가 일반적인 단위 테스트처럼 보이고 느껴지도록 합니다. 범용 테스트 프레임워크와 통합된 bUnit 테스트는 일반적으로 다음을 사용하여 실행됩니다.

* [Visual Studio 테스트 탐색기](/visualstudio/test/run-unit-tests-with-test-explorer)
* 명령 셸의 [`dotnet test`](/dotnet/core/tools/dotnet-test) CLI 명령
* 자동화된 DevOps 테스트 파이프라인

> [!NOTE]
> 여러 테스트 프레임워크의 테스트 개념 및 테스트 구현은 비슷하지만 동일하지는 않습니다. 지침은 테스트 프레임워크 설명서를 참조하세요.

다음은 Blazor 프로젝트 템플릿을 기반으로 하는 앱의 `Counter` 구성 요소에 대한 bUnit 테스트 구조를 보여 줍니다. `Counter` 구성 요소는 페이지에서 단추를 선택하는 사용자를 기반으로 카운터를 표시하고 증가시킵니다.

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

다음 bUnit 테스트에서는 단추를 선택할 때 CUT의 카운터가 올바르게 증가하는지 확인합니다.

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

다음은 테스트의 각 단계에서 수행되는 작업입니다.

* ‘정렬’: `Counter` 구성 요소는 bUnit의 `TestContext`를 사용하여 렌더링됩니다. CUT의 단락 요소(`<p>`)를 찾아서 `paraElm`에 할당합니다.

* ‘실행’: 단추의 요소(`<button>`)를 찾은 다음 `Click`을 호출하여 선택합니다. 그러면 카운터가 증가하고 단락 태그(`<p>`)의 내용이 업데이트됩니다. 단락 요소 텍스트 콘텐츠는 `TextContent`를 호출하여 가져옵니다.

* ‘어설션’: 텍스트 내용에 대해 `MarkupMatches`를 호출하여 예상 문자열(`Current count: 1`)과 일치하는지 확인합니다.

> [!NOTE]
> `MarkupMatches` 어설션 메서드는 일반 문자열 비교 어설션(예: `Assert.Equal("Current count: 1", paraElmText);`)과 다르며 `MarkupMatches`는 입력 및 예상 HTML 태그에 대한 의미 체계를 비교합니다. 의미 체계 비교에서는 HTML 의미 체계를 인식하므로 중요하지 않은 공백과 같은 항목은 무시됩니다. 따라서 더 안정적으로 테스트할 수 있습니다. 자세한 내용은 [의미 체계 HTML 비교 사용자 지정](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison)을 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [bUnit 시작하기](https://bunit.egilhansen.com/docs/getting-started/): bUnit 지침에는 테스트 프로젝트를 만들고, 프레임워크 패키지를 참조하고, 테스트를 빌드 및 실행하는 방법에 대한 지침이 포함되어 있습니다.
