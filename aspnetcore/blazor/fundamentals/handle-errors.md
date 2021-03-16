---
title: ASP.NET Core Blazor 앱에서 오류 처리
author: guardrex
description: 이 문서에서는 ASP.NET Core Blazor가 처리되지 않은 예외를 관리하는 방법과 오류를 감지 및 처리하는 앱을 개발하는 방법을 설명합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2021
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
uid: blazor/fundamentals/handle-errors
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: d4c8054afc3818d58bc2a047a0aa74613ae6047b
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395099"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>ASP.NET Core Blazor 앱에서 오류 처리

이 문서에서는 Blazor가 처리되지 않은 예외를 관리하는 방법과 오류를 감지 및 처리하는 앱을 개발하는 방법을 설명합니다

::: zone pivot="webassembly"

## <a name="detailed-errors-during-development"></a>개발 중에 발생한 자세한 오류

Blazor 앱이 개발 중에 올바르게 작동하지 않는 경우 앱에서 자세한 오류 정보를 수신하면 문제를 해결하고 수정하는 데 도움이 됩니다. 오류가 발생하면 Blazor 앱의 화면 아래쪽에 연한 노란색 막대가 표시됩니다.

* 개발 중에 이 막대를 누르면 예외를 볼 수 있는 브라우저 콘솔로 연결됩니다.
* 프로덕션에서 이 막대는 오류가 발생했음을 알려 주고 브라우저를 새로 고치도록 권장합니다.

이 오류 처리 환경의 UI는 [Blazor 프로젝트 템플릿](xref:blazor/project-structure)의 일부입니다.

Blazor WebAssembly 앱에서 `wwwroot/index.html` 파일의 환경을 사용자 지정합니다.

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

앱의 스타일시트(`wwwroot/css/app.css`)에 `blazor-error-ui` CSS 클래스의 `display: none` 스타일이 있기 때문에 `blazor-error-ui` 요소는 일반적으로 숨겨집니다. 오류가 발생하면 프레임워크가 `display: block`을 요소에 적용합니다.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>개발자 코드에서 처리되지 않은 예외 관리

오류가 발생한 후에도 앱을 계속하려면 앱에 오류 처리 논리가 있어야 합니다. 이 문서의 나중 섹션에서는 처리되지 않은 예외의 잠재적 원인을 설명합니다.

프로덕션 환경에서는 UI에서 프레임워크 예외 메시지 또는 스택 추적을 렌더링하지 않습니다. 예외 메시지 또는 스택 추적을 렌더링하면 다음이 수행될 수 있습니다.

* 최종 사용자에게 중요한 정보를 공개합니다.
* 악의적인 사용자가 앱, 서버 또는 네트워크의 보안을 손상시킬 수 있는 앱의 약점을 찾아내도록 합니다.

## <a name="global-exception-handling"></a>전역 예외 처리

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

## <a name="log-errors-with-a-persistent-provider"></a>영구 공급자가 있는 오류 로깅

처리되지 않은 예외가 발생하는 경우 예외가 서비스 컨테이너에 구성된 <xref:Microsoft.Extensions.Logging.ILogger> 인스턴스에 로깅됩니다. 기본적으로 Blazor 앱은 콘솔 로깅 공급자의 콘솔 출력에 로깅합니다. 로그 크기 관리 및 로그 회전과 함께 로깅 공급자를 사용하는 백 엔드 웹 API에 오류 정보를 보냄으로써 서버의 좀 더 영구적인 위치에 로깅하는 것이 좋습니다. 또는 백 엔드 웹 API 앱은 [Azure Application Insights(Azure Monitor)&dagger;](/azure/azure-monitor/app/app-insights-overview) 같은 APM(애플리케이션 성능 관리) 서비스를 사용하여 클라이언트에서 수신하는 오류 정보를 로깅할 수 있습니다.

로깅할 인시던트 및 로깅된 인시던트의 심각도 수준을 결정해야 합니다. 악의적인 사용자가 의도적으로 오류를 트리거할 수 있습니다. 예를 들어, 제품 정보를 표시하는 구성 요소의 URL에서 알 수 없는 `ProductId`가 제공되는 오류의 경우 인시던트를 로깅하지 않도록 합니다. 모든 오류가 로깅이 필요한 인시던트로 취급되는 것은 아닙니다.

자세한 내용은 다음 문서를 참조하세요.

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&Dagger;
* <xref:web-api/index>

&dagger;Blazor WebAssembly 앱을 지원하기 위한 네이티브 [Application Insights](/azure/azure-monitor/app/app-insights-overview) 기능과 [Google 애널리틱스](https://analytics.google.com/analytics/web/)에 대한 네이티브 Blazor 프레임워크 지원은 이러한 기술의 향후 릴리스에서 제공될 수 있습니다. 자세한 내용은 [Blazor WASM 클라이언트 쪽 App Insights 지원(microsoft/ApplicationInsights-dotnet #2143)](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143)과 [웹 분석 및 진단(커뮤니티 구현에 대한 링크 포함)(dotnet/aspnetcore #5461)](https://github.com/dotnet/aspnetcore/issues/5461)을 참조하세요. 한편 클라이언트 쪽 Blazor WebAssembly 앱은 [JS interop](xref:blazor/call-javascript-from-dotnet)과 함께 [Application Insights JavaScript SDK](/azure/azure-monitor/app/javascript)를 사용하여 클라이언트 쪽 앱에서 Application Insights에 직접 오류를 로깅할 수 있습니다.

&Dagger;Blazor 앱용 웹 API 백 엔드 앱인 서버 쪽 ASP.NET Core 앱에 적용됩니다. 클라이언트 쪽 앱은 오류 정보를 트랩하고 웹 API로 보냅니다. 웹 API는 이 정보를 영구 로깅 공급자에 로깅합니다.

## <a name="places-where-errors-may-occur"></a>오류가 발생할 수 있는 위치

프레임워크 및 앱 코드는 다음 위치 중 하나에서 처리되지 않은 예외를 트리거할 수 있습니다. 자세한 내용은 이 문서의 아래 섹션을 참조하세요.

* [구성 요소 인스턴스화](#component-instantiation-webassembly)
* [수명 주기 메서드](#lifecycle-methods-webassembly)
* [렌더링 논리](#rendering-logic-webassembly)
* [이벤트 처리기](#event-handlers-webassembly)
* [구성 요소 삭제](#component-disposal-webassembly)
* [JavaScript interop](#javascript-interop-webassembly)

<h3 id="component-instantiation-webassembly">구성 요소 인스턴스화</h3>

Blazor가 구성 요소의 인스턴스를 만들 경우

* 구성 요소의 생성자가 호출됩니다.
* [`@inject`](xref:mvc/views/razor#inject) 지시문 또는 [`[Inject]` 특성](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component)을 통해 구성 요소 생성자에 제공된 singleton이 아닌 DI 서비스의 생성자가 호출됩니다.

실행된 생성자 또는 `[Inject]` 속성의 setter에 오류가 발생하면 처리되지 않은 예외가 발생하고 프레임워크가 구성 요소를 인스턴스화하는 것이 중단됩니다. 생성자 논리에서 예외를 throw할 수 있는 경우 앱은 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩해야 합니다.

<h3 id="lifecycle-methods-webassembly">수명 주기 메서드</h3>

구성 요소의 수명 주기 동안 Blazor는 [수명 주기 메서드](xref:blazor/components/lifecycle#lifecycle-methods)를 호출합니다. 구성 요소가 수명 주기 메서드의 오류를 처리하려면 오류 처리 논리를 추가합니다.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>가 제품을 가져오는 메서드를 호출하는 다음 예제에서는 다음 작업이 수행됩니다.

* `ProductRepository.GetProductByIdAsync` 메서드에서 throw된 예외는 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 의해 처리됩니다.
* `catch` 블록이 실행될 때 다음이 수행됩니다.
  * `loadFailed`가 사용자에게 오류 메시지를 표시하는 데 사용되는 `true`로 설정됩니다.
  * 오류가 로깅됩니다.

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-webassembly">렌더링 논리</h3>

Razor 구성 요소 파일(`.razor`)의 선언적 태그는 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>라는 C# 메서드로 컴파일됩니다. 구성 요소가 렌더링되면 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>가 실행되고, 렌더링된 구성 요소의 요소, 텍스트 및 자식 구성 요소를 설명하는 데이터 구조를 구축합니다.

렌더링 논리는 예외를 throw할 수 있습니다. 이 시나리오의 예는 `@someObject.PropertyName`이 평가되지만 `@someObject`가 `null`인 경우에 발생합니다.

렌더링 논리에서 <xref:System.NullReferenceException>이 발생하지 않도록 하려면 해당 멤버에 액세스하기 전에 `null` 개체를 확인합니다. 다음 예제에서 `person.Address`가 `null`인 경우 `person.Address` 속성에 액세스되지 않습니다.

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

위의 코드는 `person`이 `null`이 아니라고 가정합니다. 일반적으로 이 코드의 구조에 따르면 구성 요소가 렌더링될 때 개체가 확실히 존재하게 됩니다. 이러한 경우에는 렌더링 논리에서 `null`이 있는지 확인하지 않아도 됩니다. 이전 예제에서는 구성 요소가 인스턴스화될 때 `person`이 만들어지기 때문에 `person`이 존재한다고 보장할 수 있습니다. 다음 예제에서 이를 보여 줍니다.

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-webassembly">이벤트 처리기</h3>

클라이언트 쪽 코드는 다음을 사용하여 이벤트 처리기를 만들 때 C# 코드의 호출을 트리거합니다.

* `@onclick`
* `@onchange`
* 기타 `@on...` 특성
* `@bind`

이벤트 처리기 코드는 이러한 시나리오에서 처리되지 않은 예외를 throw할 수 있습니다.

앱에서 외부 이유로 인해 실패할 수 있는 코드를 호출하는 경우 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩합니다.

사용자 코드에서 예외를 트랩 및 처리하지 않는 경우 프레임워크는 예외를 로깅합니다.

<h3 id="component-disposal-webassembly">구성 요소 삭제</h3>

예를 들어, 사용자가 다른 페이지로 이동했으므로 UI에서 구성 요소가 제거될 수 있습니다. <xref:System.IDisposable?displayProperty=fullName>을 구현하는 구성 요소가 UI에서 제거되면 프레임워크는 구성 요소의 <xref:System.IDisposable.Dispose%2A> 메서드를 호출합니다.

삭제 논리에서 예외를 throw할 수 있는 경우 앱은 오류 처리 및 로깅이 포함된 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩해야 합니다.

구성 요소 삭제에 대한 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>을 참조하세요.

<h3 id="javascript-interop-webassembly">JavaScript interop</h3>

<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>는 .NET 코드가 사용자의 브라우저에서 JavaScript 런타임에 대한 비동기 호출을 수행할 수 있도록 합니다.

다음 조건은 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>의 오류 처리에 적용됩니다.

* <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 동기적으로 실패하면 .NET 예외가 발생합니다. 예를 들어, 제공된 인수를 직렬화 할 수 없기 때문에 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 실패할 수 있습니다. 개발자 코드는 예외를 catch해야 합니다.
* <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 비동기적으로 실패하면 .NET <xref:System.Threading.Tasks.Task>는 실패합니다. 예를 들어, JavaScript 쪽 코드에서 예외를 throw하거나 완료된 `Promise`를 `rejected`로 반환하기 때문에 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 실패할 수 있습니다. 개발자 코드는 예외를 catch해야 합니다. [`await`](/dotnet/csharp/language-reference/keywords/await) 연산자를 사용하는 경우 오류 처리 및 로깅을 사용하여 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 메서드 호출을 래핑하는 것이 좋습니다.
* 기본적으로 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 호출은 특정 기간 내에 완료되어야 합니다. 그렇지 않으면 호출 시간이 초과됩니다. 기본 제한 시간은 1분입니다. 제한 시간은 네트워크 연결이 끊어진 코드 또는 완료 메시지를 다시 전송하지 않는 JavaScript 코드를 보호합니다. 호출 시간이 초과되면 결과 <xref:System.Threading.Tasks>는 <xref:System.OperationCanceledException>을 나타내며 실패합니다. 로깅을 사용하여 예외를 트랩하고 처리합니다.

마찬가지로 JavaScript 코드는 [`[JSInvokable]` 특성](xref:blazor/call-dotnet-from-javascript)으로 표시되는 .NET 메서드에 대한 호출을 시작할 수 있습니다. 이러한 .NET 메서드가 처리되지 않은 예외를 throw하면 JavaScript 쪽 `Promise`가 거부됩니다.

메서드 호출의 .NET 쪽 또는 JavaScript 쪽에서 오류 처리 코드를 사용하는 옵션이 있습니다.

자세한 내용은 다음 항목을 참조하세요.

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="advanced-scenarios"></a>고급 시나리오

### <a name="recursive-rendering"></a>재귀 렌더링

구성 요소는 재귀적으로 중첩될 수 있습니다. 이것은 재귀적 데이터 구조를 나타내는 데 유용합니다. 예를 들어, `TreeNode` 구성 요소는 각 노드의 자식에 대해 더 많은 `TreeNode` 구성 요소를 렌더링할 수 있습니다.

반복적으로 렌더링하는 경우 무한 재귀가 발생하는 코딩 패턴을 사용하지 않습니다.

* 주기가 포함된 데이터 구조는 재귀적으로 렌더링하지 않도록 합니다. 예를 들어, 자식에 자기 자신이 포함된 트리 노드를 렌더링하지 않도록 합니다.
* 주기가 포함된 레이아웃 체인을 만들지 않도록 합니다. 예를 들어, 레이아웃만 있는 레이아웃은 만들지 않도록 합니다.
* 최종 사용자가 악의적인 데이터 입력 또는 JavaScript interop 호출을 통해 재귀 고정 항목(규칙)을 위반할 수 없도록 합니다.

렌더링 중 무한 루프:

* 렌더링 프로세스가 계속 지속되도록 합니다.
* 종료되지 않는 루프를 만드는 것과 같습니다.

이 시나리오에서 스레드는 일반적으로 다음을 시도합니다.

* 운영 체제에서 허용하는 만큼의 CPU 시간을 무제한으로 사용합니다.
* 클라이언트 메모리를 무제한으로 사용합니다. 무제한 메모리를 사용하는 것은 종료되지 않는 루프가 반복될 때마다 컬렉션에 항목을 추가하는 시나리오와 동일합니다.

무한 재귀 패턴을 방지하려면 재귀 렌더링 코드에 적절한 중지 조건이 포함되어 있는지 확인합니다.

### <a name="custom-render-tree-logic"></a>사용자 지정 렌더링 트리 논리

대부분의 Blazor 구성 요소는 Razor 구성 요소 파일(`.razor`)로 구현되며 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>에서 작동하여 출력을 렌더링하는 논리를 생성하도록 프레임워크에 의해 컴파일됩니다. 그러나 개발자는 프로시저 C# 코드를 사용하여 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 논리를 수동으로 구현할 수 있습니다. 자세한 내용은 <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>를 참조하세요.

> [!WARNING]
> 수동 렌더링 트리 작성기 논리를 사용하는 것은 일반적인 구성 요소 개발에는 권장되지 않는 안전하지 않은 고급 시나리오로 간주됩니다.

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 코드를 작성하는 경우 개발자는 코드의 정확성을 보장해야 합니다. 예를 들어, 개발자는 다음을 확인해야 합니다.

* <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 및 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A>에 대한 호출은 올바르게 균형이 조정됩니다.
* 특성은 올바른 위치에만 추가됩니다.

잘못된 수동 렌더링 트리 작성기 논리가 있으면 크래시, 앱 중단, 보안 취약성을 포함하는 정의되지 않은 임의 동작이 발생할 수 있습니다.

어셈블리 코드 또는 [MSIL(Microsoft Intermediate Language)](/dotnet/standard/managed-code) 명령을 직접 작성하는 것과 동일한 수준의 복잡성과 동일한 수준의 ‘위험’에서 수동 렌더링 트리 작성기 논리를 고려하세요.

## <a name="additional-resources"></a>추가 리소스

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;
* <xref:web-api/index>

&dagger;클라이언트 쪽 Blazor WebAssembly 앱이 로깅에 사용하는 백 엔드 ASP.NET Core 웹 API 앱에 적용됩니다.

::: zone-end

::: zone pivot="server"

## <a name="detailed-errors-during-development"></a>개발 중에 발생한 자세한 오류

Blazor 앱이 개발 중에 올바르게 작동하지 않는 경우 앱에서 자세한 오류 정보를 수신하면 문제를 해결하고 수정하는 데 도움이 됩니다. 오류가 발생하면 Blazor 앱의 화면 아래쪽에 연한 노란색 막대가 표시됩니다.

* 개발 중에 이 막대를 누르면 예외를 볼 수 있는 브라우저 콘솔로 연결됩니다.
* 프로덕션에서 이 막대는 오류가 발생했음을 알려 주고 브라우저를 새로 고치도록 권장합니다.

이 오류 처리 환경의 UI는 [Blazor 프로젝트 템플릿](xref:blazor/project-structure)의 일부입니다.

Blazor Server 앱에서 `Pages/_Host.cshtml` 파일의 환경을 사용자 지정합니다.

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

사이트의 스타일시트(`wwwroot/css/site.css`)에 `blazor-error-ui` CSS 클래스의 `display: none` 스타일이 있기 때문에 `blazor-error-ui` 요소는 일반적으로 숨겨집니다. 오류가 발생하면 프레임워크가 `display: block`을 요소에 적용합니다.

## <a name="blazor-server-detailed-circuit-errors"></a>Blazor Server 자세한 회로 오류

클라이언트 쪽 오류는 호출 스택을 포함하지 않으며 오류의 원인에 대한 세부 정보를 제공하지 않지만 서버 로그에는 이러한 정보가 포함되어 있습니다. 개발 단계에서는 자세한 오류를 사용하도록 설정하여 클라이언트에서 중요한 회로 오류 정보를 볼 수 있도록 설정할 수 있습니다.

<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>를 `true`로 설정합니다. 자세한 내용과 예제는 <xref:blazor/fundamentals/signalr#circuit-handler-options>를 참조하세요.

<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> 설정에 대한 대안은 앱의 개발 환경 설정 파일(`appsettings.Development.json`)에서 `DetailedErrors` 구성 키를 `true`로 설정하는 것입니다.  또한 자세한 SignalR 로깅을 위해 [SignalR 서버 쪽 로깅](xref:signalr/diagnostics#server-side-logging)(`Microsoft.AspNetCore.SignalR`)을 [디버그](xref:Microsoft.Extensions.Logging.LogLevel) 또는 [추적](xref:Microsoft.Extensions.Logging.LogLevel)으로 설정할 수 있습니다.

`appsettings.Development.json`:

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

개발/스테이징 환경 서버 또는 로컬 시스템에서 값이 `true`인 `ASPNETCORE_DETAILEDERRORS` 환경 변수를 사용하여 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> 구성 키를 `true`로 설정할 수도 있습니다.

> [!WARNING]
> 인터넷의 클라이언트에 오류 정보를 노출하는 것을 항상 피하세요. 보안상 위험할 수 있습니다.

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a>Blazor Server 앱이 처리되지 않은 예외에 반응하는 방법

Blazor Server는 상태 저장 프레임워크입니다. 사용자가 앱과 상호 작용하는 동안 *회로* 라는 서버에 대한 연결이 유지됩니다. 회로는 활성 구성 요소 인스턴스와 다음과 같은 상태의 여러 다양한 측면을 포함합니다.

* 구성 요소의 가장 최근에 렌더링된 출력
* 클라이언트 쪽 이벤트에 의해 트리거될 수 있는 현재 이벤트 처리 대리자 세트

사용자가 여러 브라우저 탭에서 앱을 여는 경우 여러 개의 독립적인 회로를 만들게 됩니다.

Blazor는 발생하는 대부분의 처리되지 않은 예외를 회로에 치명적인 것으로 취급합니다. 처리되지 않은 예외로 인해 회로가 종료되는 경우 사용자는 페이지를 다시 로드하여 새 회로를 만드는 방식으로 앱과 계속 상호 작용할 수 있습니다. 다른 사용자 또는 다른 브라우저 탭을 위한 회로이면서 종료된 회로의 외부에 있는 회로는 영향을 받지 않습니다. 이 시나리오는 충돌하는 데스크톱 앱과 유사합니다. 작동을 중단한 앱을 다시 시작해야 하지만 다른 앱은 영향을 받지 않습니다.

다음과 같은 이유로 인해 처리되지 않은 예외가 발생하면 프레임워크가 회로를 종료합니다.

* 처리되지 않은 예외는 종종 회로를 정의되지 않은 상태로 둡니다.
* 처리되지 않은 예외가 발생한 후에는 앱의 일반 작업을 보장할 수 없습니다.
* 회로가 정의되지 않은 상태로 계속되는 경우 앱에 보안 취약성이 나타날 수 있습니다.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>개발자 코드에서 처리되지 않은 예외 관리

오류가 발생한 후에도 앱을 계속하려면 앱에 오류 처리 논리가 있어야 합니다. 이 문서의 나중 섹션에서는 처리되지 않은 예외의 잠재적 원인을 설명합니다.

프로덕션 환경에서는 UI에서 프레임워크 예외 메시지 또는 스택 추적을 렌더링하지 않습니다. 예외 메시지 또는 스택 추적을 렌더링하면 다음이 수행될 수 있습니다.

* 최종 사용자에게 중요한 정보를 공개합니다.
* 악의적인 사용자가 앱, 서버 또는 네트워크의 보안을 손상시킬 수 있는 앱의 약점을 찾아내도록 합니다.

## <a name="global-exception-handling"></a>전역 예외 처리

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

이 섹션의 접근 방식은 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 오류를 처리하기 때문에 오류가 발생하고 회로가 활성 상태를 유지할 경우 클라이언트와 서버 간의 SignalR 연결이 끊어지지 않습니다. 처리되지 않은 예외는 회로에 치명적입니다. 자세한 내용은 [Blazor Server 앱이 처리되지 않은 예외에 반응하는 방법](#how-a-blazor-server-app-reacts-to-unhandled-exceptions)에 대한 이전 섹션을 참조하세요.

## <a name="log-errors-with-a-persistent-provider"></a>영구 공급자가 있는 오류 로깅

처리되지 않은 예외가 발생하는 경우 예외가 서비스 컨테이너에 구성된 <xref:Microsoft.Extensions.Logging.ILogger> 인스턴스에 로깅됩니다. 기본적으로 Blazor 앱은 콘솔 로깅 공급자의 콘솔 출력에 로깅합니다. 로그 크기와 로그 순환을 관리하는 공급자와 함께 서버의 좀 더 영구적인 위치에 로깅하는 것이 좋습니다. 또는 앱이 [Azure Application Insights(Azure Monitor)](/azure/azure-monitor/app/app-insights-overview) 같은 APM(애플리케이션 성능 관리) 서비스를 사용할 수 있습니다.

개발하는 동안 Blazor Server 앱은 일반적으로 디버깅에 도움을 주기 위해 브라우저 콘솔에 예외의 전체 세부 정보를 보냅니다. 프로덕션 환경에서는 자세한 오류가 클라이언트에 전송되지 않지만, 예외의 전체 세부 정보가 서버에 로깅됩니다.

로깅할 인시던트 및 로깅된 인시던트의 심각도 수준을 결정해야 합니다. 악의적인 사용자가 의도적으로 오류를 트리거할 수 있습니다. 예를 들어, 제품 정보를 표시하는 구성 요소의 URL에서 알 수 없는 `ProductId`가 제공되는 오류의 경우 인시던트를 로깅하지 않도록 합니다. 모든 오류가 로깅이 필요한 인시던트로 취급되는 것은 아닙니다.

자세한 내용은 다음 문서를 참조하세요.

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

&dagger;Blazor 앱용 웹 API 백 엔드 앱인 서버 쪽 ASP.NET Core 앱에 적용됩니다.

## <a name="places-where-errors-may-occur"></a>오류가 발생할 수 있는 위치

프레임워크 및 앱 코드는 다음 위치 중 하나에서 처리되지 않은 예외를 트리거할 수 있습니다. 자세한 내용은 이 문서의 아래 섹션을 참조하세요.

* [구성 요소 인스턴스화](#component-instantiation-server)
* [수명 주기 메서드](#lifecycle-methods-server)
* [렌더링 논리](#rendering-logic-server)
* [이벤트 처리기](#event-handlers-server)
* [구성 요소 삭제](#component-disposal-server)
* [JavaScript interop](#javascript-interop-server)
* [Blazor Server 다시 렌더링](#blazor-server-prerendering-server)

<h3 id="component-instantiation-server">구성 요소 인스턴스화</h3>

Blazor가 구성 요소의 인스턴스를 만들 경우

* 구성 요소의 생성자가 호출됩니다.
* [`@inject`](xref:mvc/views/razor#inject) 지시문 또는 [`[Inject]` 특성](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component)을 통해 구성 요소 생성자에 제공된 singleton이 아닌 DI 서비스의 생성자가 호출됩니다.

모든 `[Inject]` 속성에 대해 실행된 생성자 또는 setter가 처리되지 않은 예외를 throw하는 경우 Blazor Server 회로가 실패합니다. 이 예외는 프레임워크에서 구성 요소를 인스턴스화할 수 없기 때문에 치명적입니다. 생성자 논리에서 예외를 throw할 수 있는 경우 앱은 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩해야 합니다.

<h3 id="lifecycle-methods-server">수명 주기 메서드</h3>

구성 요소의 수명 주기 동안 Blazor는 [수명 주기 메서드](xref:blazor/components/lifecycle#lifecycle-methods)를 호출합니다. 수명 주기 메서드가 동기적 또는 비동기적으로 예외를 throw하는 경우 이 예외는 Blazor Server 회로에 치명적입니다. 구성 요소가 수명 주기 메서드의 오류를 처리하려면 오류 처리 논리를 추가합니다.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>가 제품을 가져오는 메서드를 호출하는 다음 예제에서는 다음 작업이 수행됩니다.

* `ProductRepository.GetProductByIdAsync` 메서드에서 throw된 예외는 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 의해 처리됩니다.
* `catch` 블록이 실행될 때 다음이 수행됩니다.
  * `loadFailed`가 사용자에게 오류 메시지를 표시하는 데 사용되는 `true`로 설정됩니다.
  * 오류가 로깅됩니다.

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-server">렌더링 논리</h3>

Razor 구성 요소 파일(`.razor`)의 선언적 태그는 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>라는 C# 메서드로 컴파일됩니다. 구성 요소가 렌더링되면 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>가 실행되고, 렌더링된 구성 요소의 요소, 텍스트 및 자식 구성 요소를 설명하는 데이터 구조를 구축합니다.

렌더링 논리는 예외를 throw할 수 있습니다. 이 시나리오의 예는 `@someObject.PropertyName`이 평가되지만 `@someObject`가 `null`인 경우에 발생합니다. 렌더링 논리에 따라 throw된 처리되지 않은 예외는 Blazor Server 회로에 치명적입니다.

렌더링 논리에서 <xref:System.NullReferenceException>이 발생하지 않도록 하려면 해당 멤버에 액세스하기 전에 `null` 개체를 확인합니다. 다음 예제에서 `person.Address`가 `null`인 경우 `person.Address` 속성에 액세스되지 않습니다.

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

위의 코드는 `person`이 `null`이 아니라고 가정합니다. 일반적으로 이 코드의 구조에 따르면 구성 요소가 렌더링될 때 개체가 확실히 존재하게 됩니다. 이러한 경우에는 렌더링 논리에서 `null`이 있는지 확인하지 않아도 됩니다. 이전 예제에서는 구성 요소가 인스턴스화될 때 `person`이 만들어지기 때문에 `person`이 존재한다고 보장할 수 있습니다. 다음 예제에서 이를 보여 줍니다.

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-server">이벤트 처리기</h3>

클라이언트 쪽 코드는 다음을 사용하여 이벤트 처리기를 만들 때 C# 코드의 호출을 트리거합니다.

* `@onclick`
* `@onchange`
* 기타 `@on...` 특성
* `@bind`

이벤트 처리기 코드는 이러한 시나리오에서 처리되지 않은 예외를 throw할 수 있습니다.

이벤트 처리기가 처리되지 않은 예외(예: 데이터베이스 쿼리 실패)를 throw하는 경우 예외는 Blazor Server 회로에 치명적입니다. 앱에서 외부 이유로 인해 실패할 수 있는 코드를 호출하는 경우 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩합니다.

사용자 코드에서 예외를 트랩 및 처리하지 않는 경우 프레임워크는 예외를 로깅하고 회로를 종료합니다.

<h3 id="component-disposal-server">구성 요소 삭제</h3>

예를 들어, 사용자가 다른 페이지로 이동했으므로 UI에서 구성 요소가 제거될 수 있습니다. <xref:System.IDisposable?displayProperty=fullName>을 구현하는 구성 요소가 UI에서 제거되면 프레임워크는 구성 요소의 <xref:System.IDisposable.Dispose%2A> 메서드를 호출합니다.

구성 요소의 `Dispose` 메서드가 처리되지 않은 예외를 throw하는 경우 예외는 Blazor Server 회로에 치명적입니다. 삭제 논리에서 예외를 throw할 수 있는 경우 앱은 오류 처리 및 로깅이 포함된 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩해야 합니다.

구성 요소 삭제에 대한 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>을 참조하세요.

<h3 id="javascript-interop-server">JavaScript interop</h3>

<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>는 .NET 코드가 사용자의 브라우저에서 JavaScript 런타임에 대한 비동기 호출을 수행할 수 있도록 합니다.

다음 조건은 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>의 오류 처리에 적용됩니다.

* <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 동기적으로 실패하면 .NET 예외가 발생합니다. 예를 들어, 제공된 인수를 직렬화 할 수 없기 때문에 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 실패할 수 있습니다. 개발자 코드는 예외를 catch해야 합니다. 이벤트 처리기 또는 구성 요소 수명 주기 메서드의 앱 코드가 예외를 처리하지 않는 경우 결과 예외는 Blazor Server 회로에 치명적입니다.
* <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 비동기적으로 실패하면 .NET <xref:System.Threading.Tasks.Task>는 실패합니다. 예를 들어, JavaScript 쪽 코드에서 예외를 throw하거나 완료된 `Promise`를 `rejected`로 반환하기 때문에 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 실패할 수 있습니다. 개발자 코드는 예외를 catch해야 합니다. [`await`](/dotnet/csharp/language-reference/keywords/await) 연산자를 사용하는 경우 오류 처리 및 로깅을 사용하여 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 메서드 호출을 래핑하는 것이 좋습니다. 이 연산자를 사용하지 않는 경우 오류 코드는 Blazor Server 회로에 치명적인 처리되지 않은 예외를 발생시킵니다.
* 기본적으로 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 호출은 특정 기간 내에 완료되어야 합니다. 그렇지 않으면 호출 시간이 초과됩니다. 기본 제한 시간은 1분입니다. 제한 시간은 네트워크 연결이 끊어진 코드 또는 완료 메시지를 다시 전송하지 않는 JavaScript 코드를 보호합니다. 호출 시간이 초과되면 결과 <xref:System.Threading.Tasks>는 <xref:System.OperationCanceledException>을 나타내며 실패합니다. 로깅을 사용하여 예외를 트랩하고 처리합니다.

마찬가지로 JavaScript 코드는 [`[JSInvokable]` 특성](xref:blazor/call-dotnet-from-javascript)으로 표시되는 .NET 메서드에 대한 호출을 시작할 수 있습니다. 이러한 .NET 메서드에서 처리되지 않은 예외를 throw하는 경우 다음이 수행됩니다.

* 이 예외는 Blazor Server 회로에 치명적으로 처리되지 않습니다.
* JavaScript 쪽 `Promise`는 거부됩니다.

메서드 호출의 .NET 쪽 또는 JavaScript 쪽에서 오류 처리 코드를 사용하는 옵션이 있습니다.

자세한 내용은 다음 항목을 참조하세요.

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

<h3 id="blazor-server-prerendering-server">Blazor Server 미리 렌더링</h3>

렌더링된 HTML 태그가 사용자의 초기 HTTP 요청 일부로 반환되도록 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용하여 Blazor 구성 요소를 미리 렌더링할 수 있습니다. 이 작업은 다음을 통해 진행됩니다.

* 동일한 페이지의 일부인 미리 렌더링된 모든 구성 요소에 대해 새 회로 생성
* 초기 HTML 생성
* 사용자의 브라우저가 동일한 서버에 대해 SignalR 연결을 다시 설정할 때까지 회로를 `disconnected`로 처리. 연결이 설정되면 회로의 상호 작용이 다시 시작되고 구성 요소의 HTML 태그가 업데이트됩니다.

사전 렌더링 동안 구성 요소가 처리되지 않은 예외를 throw하는 경우(예: 수명 주기 방법 동안 또는 렌더링 논리에서)

* 이 예외는 회로에 치명적입니다.
* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 태그 도우미의 호출 스택에 대해 예외가 throw됩니다. 따라서 개발자 코드에서 예외를 명시적으로 catch하지 않으면 전체 HTTP 요청이 실패합니다.

일반적인 경우에는 사전 렌더링에 실패하면 작업 구성 요소를 렌더링할 수 없기 때문에 구성 요소를 계속 빌드 및 렌더링하는 것은 적합하지 않습니다.

렌더링 중에 발생할 수 있는 오류를 허용하려면 예외를 throw할 수 있는 오류 처리 논리를 구성 요소 내부에 배치해야 합니다. 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용합니다. [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 태그 도우미를 래핑하는 대신, <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 태그 도우미에 의해 렌더링되는 구성 요소에 오류 처리 논리를 배치합니다.

## <a name="advanced-scenarios"></a>고급 시나리오

### <a name="recursive-rendering"></a>재귀 렌더링

구성 요소는 재귀적으로 중첩될 수 있습니다. 이것은 재귀적 데이터 구조를 나타내는 데 유용합니다. 예를 들어, `TreeNode` 구성 요소는 각 노드의 자식에 대해 더 많은 `TreeNode` 구성 요소를 렌더링할 수 있습니다.

반복적으로 렌더링하는 경우 무한 재귀가 발생하는 코딩 패턴을 사용하지 않습니다.

* 주기가 포함된 데이터 구조는 재귀적으로 렌더링하지 않도록 합니다. 예를 들어, 자식에 자기 자신이 포함된 트리 노드를 렌더링하지 않도록 합니다.
* 주기가 포함된 레이아웃 체인을 만들지 않도록 합니다. 예를 들어, 레이아웃만 있는 레이아웃은 만들지 않도록 합니다.
* 최종 사용자가 악의적인 데이터 입력 또는 JavaScript interop 호출을 통해 재귀 고정 항목(규칙)을 위반할 수 없도록 합니다.

렌더링 중 무한 루프:

* 렌더링 프로세스가 계속 지속되도록 합니다.
* 종료되지 않는 루프를 만드는 것과 같습니다.

이 시나리오에서 영향을 받는 Blazor Server 회로는 실패하고 스레드는 일반적으로 다음을 시도합니다.

* 운영 체제에서 허용하는 만큼의 CPU 시간을 무제한으로 사용합니다.
* 서버 메모리를 무제한으로 사용합니다. 무제한 메모리를 사용하는 것은 종료되지 않는 루프가 반복될 때마다 컬렉션에 항목을 추가하는 시나리오와 동일합니다.

무한 재귀 패턴을 방지하려면 재귀 렌더링 코드에 적절한 중지 조건이 포함되어 있는지 확인합니다.

### <a name="custom-render-tree-logic"></a>사용자 지정 렌더링 트리 논리

대부분의 Blazor 구성 요소는 Razor 구성 요소 파일(`.razor`)로 구현되며 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>에서 작동하여 출력을 렌더링하는 논리를 생성하도록 프레임워크에 의해 컴파일됩니다. 그러나 개발자는 프로시저 C# 코드를 사용하여 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 논리를 수동으로 구현할 수 있습니다. 자세한 내용은 <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>를 참조하세요.

> [!WARNING]
> 수동 렌더링 트리 작성기 논리를 사용하는 것은 일반적인 구성 요소 개발에는 권장되지 않는 안전하지 않은 고급 시나리오로 간주됩니다.

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 코드를 작성하는 경우 개발자는 코드의 정확성을 보장해야 합니다. 예를 들어, 개발자는 다음을 확인해야 합니다.

* <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 및 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A>에 대한 호출은 올바르게 균형이 조정됩니다.
* 특성은 올바른 위치에만 추가됩니다.

잘못된 수동 렌더링 트리 작성기 논리가 있으면 충돌, 서버 중단 및 보안 취약성을 포함하는 정의되지 않은 임의 동작이 발생할 수 있습니다.

어셈블리 코드 또는 [MSIL(Microsoft Intermediate Language)](/dotnet/standard/managed-code) 명령을 직접 작성하는 것과 동일한 수준의 복잡성과 동일한 수준의 ‘위험’에서 수동 렌더링 트리 작성기 논리를 고려하세요.

## <a name="additional-resources"></a>추가 리소스

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

&dagger;Blazor 앱용 웹 API 백 엔드 앱인 서버 쪽 ASP.NET Core 앱에 적용됩니다.

::: zone-end
