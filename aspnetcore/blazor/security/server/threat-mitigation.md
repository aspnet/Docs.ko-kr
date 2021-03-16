---
title: ASP.NET Core Blazor Server를 위한 위협 완화 지침
author: guardrex
description: Blazor Server 앱의 보안 위협을 완화하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2020
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
uid: blazor/security/server/threat-mitigation
ms.openlocfilehash: 2a5add79d60ddcbb1afdb9489fa16da692a7d6bd
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109665"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-blazor-server"></a>ASP.NET Core Blazor Server를 위한 위협 완화 지침

Blazor Server 앱은 ‘상태 저장’ 데이터 처리 모델을 사용합니다. 이 모델에서는 서버와 클라이언트가 장기적으로 지속되는 관계를 유지합니다. 영구 상태는 [회로](xref:blazor/state-management)에 의해 유지 관리되는데, 회로는 역시 장기적으로 지속되는 여러 연결을 포괄할 수 있습니다.

사용자가 Blazor Server 사이트를 방문하면 서버는 서버의 메모리에 회로를 만듭니다. 회로는 어떤 콘텐츠를 렌더링해야 하는지를 브라우저에 알려 주고 사용자가 UI에서 단추를 선택한 경우와 같은 이벤트에 대응합니다. 이러한 동작을 수행하기 위해 회로는 사용자의 브라우저에서 JavaScript 함수를 호출하고 서버에서 .NET 메서드를 호출합니다. 이와 같은 양방향 JavaScript 기반 상호 작용을 [JS interop(JavaScript interop)](xref:blazor/call-javascript-from-dotnet)라고 합니다.

JS interop은 인터넷을 통해 실행되고 클라이언트가 원격 브라우저를 사용하기 때문에 Blazor Server 앱은 대부분의 웹앱 보안 문제를 공유합니다. 이 항목에서는 Blazor Server 앱에 가해지는 일반적인 위협을 살펴보고 인터넷에 연결된 앱을 위한 위협 완화 지침을 제공합니다.

회사 내부 네트워크와 인트라넷과 같은 제한된 환경에서는 이러한 완화 지침이

* 제한된 환경에 적용되지 않을 수 있습니다.
* 제한된 환경의 보안 위험이 낮기 때문에 구현 비용을 투입할 가치가 없을 수 있습니다.

## <a name="blazor-and-shared-state"></a>Blazor 및 공유 상태

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a>리소스 소모

리소스 소모는 클라이언트가 서버와 상호 작용하는 과정에서 서버가 지나치게 많은 리소스를 사용하도록 만드는 경우에 발생할 수 있습니다. 과도한 리소스 사용은 주로 다음에 영향을 줍니다.

* [CPU](#cpu)
* [메모리](#memory)
* [클라이언트 연결](#client-connections)

DoS(서비스 거부) 공격은 일반적으로 앱 또는 서버 리소스의 소모를 유발합니다. 그러나 리소스 소모가 항상 시스템에 대한 공격의 결과로 발생하는 것은 아닙니다. 예를 들어 사용자 수요가 높을 경우 유한한 리소스가 소모될 수 있습니다. DoS는 [DoS(서비스 거부) 공격](#denial-of-service-dos-attacks) 섹션에 자세히 설명되어 있습니다.

데이터베이스나 (파일 읽기 및 쓰기에 사용되는) 파일 핸들과 같은 Blazor 프레임워크 외부 리소스에서도 리소스 소모가 발생할 수 있습니다. 자세한 내용은 <xref:performance/performance-best-practices>를 참조하세요.

### <a name="cpu"></a>CPU

하나 이상의 클라이언트에서 서버가 CPU 작업을 많이 수행하도록 강제하는 경우 CPU 소모가 발생할 수 있습니다.

예를 들어 ‘피보나치 수’를 계산하는 Blazor Server 앱이 있다고 가정하겠습니다. 피보나치 수는 피보나치 수열에서 생성됩니다. 피보나치 수열에서 각 숫자는 앞에 나온 두 개의 숫자의 합입니다. 정답을 도출하는 데 필요한 작업량은 수열의 길이와 초기 값의 크기에 따라 달라집니다. 앱이 클라이언트의 요청에 제한을 두지 않으면 CPU를 많이 사용하는 계산이 CPU의 시간을 차지하여 다른 작업의 성능을 저하시킬 수 있습니다. 과도한 리소스 사용은 가용성에 영향을 주는 보안 문제입니다.

CPU 소모는 모든 공용 앱의 중요한 문제입니다. 일반적인 웹앱에서는 보호 기능으로 요청 및 연결의 시간이 초과되지만 Blazor Server 앱에는 이와 같은 보호 기능이 없습니다. Blazor Server 앱의 경우 CPU 사용량이 많은 작업을 수행하기 전에 반드시 적절한 검사와 제한을 포함해야 합니다.

### <a name="memory"></a>메모리

하나 이상의 클라이언트에서 서버가 많은 양의 메모리를 사용하도록 강제하는 경우 메모리 소모가 발생할 수 있습니다.

예를 들어 항목 목록을 받아서 표시하는 구성 요소가 포함된 Blazor서버 쪽 앱이 있다고 가정하겠습니다. Blazor 앱이 허용되는 항목 수 또는 클라이언트로 다시 렌더링되는 항목 수에 제한을 설정하지 않을 경우 메모리를 많이 사용하는 처리 및 렌더링이 서버의 메모리를 차지하여 서버의 성능을 저하시킬 수 있습니다. 서버는 크래시가 발생한 것처럼 보일 정도로 느려지거나 실제로 크래시가 발생할 수 있습니다.

서버의 잠재적 메모리 소모 시나리오와 관련하여 항목 목록을 유지 관리하고 표시하는 다음 시나리오를 살펴보겠습니다.

* `List<MyItem>` 속성 또는 필드의 항목은 서버의 메모리를 사용합니다. 앱에서 항목 목록이 무제한으로 늘어나도록 허용할 경우 서버의 메모리가 부족해질 수 있습니다. 메모리가 부족해지면 현재 세션이 종료되고(크래시) 해당 서버 인스턴스의 모든 동시 세션에서 메모리 부족 예외가 수신됩니다. 이 시나리오가 발생하지 않도록 하려면 앱에서 동시 사용자에게 항목 제한을 적용하는 데이터 구조를 사용해야 합니다.
* 렌더링에 페이지 매기기 스키마가 사용되지 않는 경우 서버는 UI에 표시되지 않는 개체를 위해 추가 메모리를 사용합니다. 항목 수에 제한이 없는 경우 메모리 수요로 인해 사용 가능한 서버 메모리가 소모될 수 있습니다. 이 시나리오를 방지하려면 다음 방법 중 하나를 사용합니다.
  * 렌더링 시에 페이지가 매겨진 목록을 사용합니다.
  * 처음 100개에서 1,000개의 항목만 표시하고, 표시된 항목보다 더 많은 항목을 찾으려면 사용자가 검색 기준을 입력하도록 요구합니다.
  * 고급 렌더링 시나리오의 경우 ‘가상화’를 지원하는 목록 또는 그리드를 구현합니다. 가상화를 사용하면 목록이 현재 사용자에게 표시되는 항목의 하위 집합만 렌더링합니다. 사용자가 UI의 스크롤 막대와 상호 작용하면 구성 요소는 표시하는 데 필요한 항목만 렌더링합니다. 현재 표시하는 데 필요하지 않은 항목은 보조 스토리지에 보관될 수 있습니다. 이렇게 보조 스토리지에 보관하는 것이 이상적인 방법입니다. 표시되지 않은 항목을 메모리에 보관할 수도 있는데, 이것은 덜 이상적인 방법입니다.

Blazor Server 앱은 WPF, Windows Forms, Blazor WebAssembly와 같은 상태 저장의 다른 UI 프레임워크에서도 비슷한 프로그래밍 모델을 제공합니다. 주된 차이점은 이러한 몇몇 UI 프레임워크에서 앱에 의해 사용되는 메모리는 클라이언트에 속하기 때문에 해당 개별 클라이언트에만 영향을 준다는 것입니다. 예를 들어 Blazor WebAssembly 앱은 전적으로 클라이언트에서 실행되며 클라이언트 메모리 리소스만 사용합니다. Blazor Server 시나리오에서 앱이 사용하는 메모리는 서버에 속하며 서버 인스턴스에 있는 여러 클라이언트 간에 공유됩니다.

서버 쪽 메모리 수요는 모든 Blazor Server 앱의 고려 사항입니다. 그러나 대부분의 웹앱은 상태 비저장이며, 요청을 처리하는 동안 사용되는 메모리는 응답이 반환될 때 해제됩니다. 일반적으로, 클라이언트 연결이 끊겨도 유지되는 다른 서버 쪽 앱에서와 같이 클라이언트가 무제한으로 늘어나는 메모리를 할당할 수 있도록 허용하지 않는 것이 좋습니다. Blazor Server 앱이 사용하는 메모리는 단일 요청보다 오랜 시간 동안 지속됩니다.

> [!NOTE]
> 개발 단계에서 프로파일러를 사용하거나 추적을 캡처하여 클라이언트의 메모리 수요를 평가할 수 있습니다. 프로파일러나 추적은 특정 클라이언트에 할당된 메모리를 캡처하지 않습니다. 개발 단계에서 특정 클라이언트의 메모리 사용을 캡처하려면 덤프를 캡처한 다음 해당 사용자의 회로에서 루트된 모든 개체의 메모리 수요를 살펴보세요.

### <a name="client-connections"></a>클라이언트 연결

하나 이상의 클라이언트에서 서버에 대한 동시 연결을 너무 많이 열어서 다른 클라이언트가 새 연결을 설정하는 것을 차단하는 경우 연결 소모가 발생할 수 있습니다.

Blazor 클라이언트는 세션당 단일 연결을 설정하고 브라우저 창이 열려 있는 한 연결을 계속 열어 둡니다. 서버에 가해지는 모든 연결에 대한 유지 관리 수요는 Blazor 앱에 국한되지 않습니다. 연결의 지속적인 특성과 Blazor Server 앱의 상태 저장 특성을 고려하면 연결 소모는 앱 가용성에 대한 보다 큰 위험이 됩니다.

기본적으로 Blazor Server 앱에 대한 사용자당 연결 수에는 제한이 없습니다. 앱에 연결 제한이 필요한 경우 다음 방법 중 하나 이상을 사용하세요.

* 인증을 요구합니다. 이렇게 하면 인증되지 않은 사용자가 자연적으로 앱에 연결할 수 없게 됩니다. 이 시나리오가 유효해지려면 사용자가 마음대로 새 사용자를 프로비저닝하는 것을 차단해야 합니다.
* 사용자당 연결 수를 제한합니다. 다음 방법을 통해 연결을 제한할 수 있습니다. 정상 사용자가 앱에 액세스할 수 있도록 주의를 기울이세요(예: 클라이언트의 IP 주소를 기준으로 연결 제한이 설정된 경우).
  * 앱 수준에서:
    * 엔드포인트 라우팅 확장성.
    * 앱에 연결할 때 인증을 요구하고, 사용자당 활성 세션을 추적합니다.
    * 제한에 도달하면 새 세션을 거부합니다.
    * 프록시를 사용한 앱에 대한 프록시 WebSocket 연결(예: 클라이언트에서 앱으로의 연결을 다중 송신하는 [Azure SignalR 서비스](/azure/azure-signalr/signalr-overview)). 이렇게 하면 앱에 단일 클라이언트에서 설정할 수 있는 것보다 더 많은 연결 용량이 제공되어 하나의 클라이언트가 서버에 대한 연결을 소모하는 일을 방지할 수 있습니다.
  * 서버 수준에서: 앱 앞에서 프록시/게이트웨이를 사용합니다. 예를 들어 [Azure Front Door](/azure/frontdoor/front-door-overview)는 앱으로 가는 웹 트래픽의 전역 라우팅을 정의, 관리, 모니터링할 수 있고, Blazor Server 앱이 Long Polling을 사용하도록 구성된 경우 작동합니다.
  
    > [!NOTE]
    > Long Polling은 Blazor Server 앱에서 지원되지만 [WebSocket이 권장 전송 프로토콜](xref:blazor/host-and-deploy/server#azure-signalr-service)입니다. [Azure Front Door](/azure/frontdoor/front-door-overview)는 현재 WebSocket을 지원하지 않지만 향후 서비스 릴리스에서 WebSocket을 지원하는 것을 검토 중입니다.

## <a name="denial-of-service-dos-attacks"></a>DoS(서비스 거부) 공격

DoS(서비스 거부) 공격에서는 클라이언트가 서버로 하여금 하나 이상의 리소스를 소모하도록 하여 앱을 사용할 수 없도록 만듭니다. Blazor Server 앱에는 기본 제한이 있으며 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>에 설정된 다른 ASP.NET Core 및 SignalR 제한을 사용하여 DoS 공격으로부터 보호합니다.

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType>

자세한 내용 및 구성 코딩 예제는 다음 문서를 참조하세요.

* <xref:blazor/fundamentals/signalr>
* <xref:signalr/configuration>

## <a name="interactions-with-the-browser-client"></a>브라우저와의 상호 작용(클라이언트)

클라이언트는 JS interop 이벤트 디스패치 및 렌더링 완료를 통해 서버와 상호 작용합니다. JS interop 통신은 JavaScript와 .NET 사이에서 양방향으로 이루어집니다.

* 브라우저 이벤트는 비동기 방식으로 클라이언트에서 서버로 디스패치됩니다.
* 서버는 비동기적으로 응답하며 필요에 따라 UI를 다시 렌더링합니다.

### <a name="javascript-functions-invoked-from-net"></a>.NET에서 호출되는 JavaScript 함수

.NET 메서드에서 JavaScript로의 호출:

* 모든 호출에는 구성 가능한 시간 제한이 있습니다. 이 시간이 경과하면 장애가 발생하고 호출자에게 <xref:System.OperationCanceledException>이 반환됩니다.
  * 호출의 기본 시간 제한(<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>)은 1분입니다. 이 제한을 구성하려면 <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>의 내용을 참조하세요.
  * 취소 토큰을 제공하여 개별 호출별로 취소를 제어할 수 있습니다. 가능한 경우 기본 호출 시간 제한을 사용하고, 취소 토큰을 제공한 경우에는 클라이언트에 대한 호출의 시간을 제한하세요.
* JavaScript 호출 결과는 신뢰할 수 없습니다. 브라우저에서 실행되는 Blazor 앱 클라이언트는 호출할 JavaScript 함수를 검색합니다. 함수가 호출되면 결과 또는 오류가 생성됩니다. 악의적인 클라이언트가 다음을 시도할 수 있습니다.
  * JavaScript 함수에서 오류를 반환하여 앱에서 문제를 발생시킬 수 있습니다.
  * JavaScript 함수에서 예기치 않은 결과를 반환하여 서버에서 의도치 않은 동작을 유도할 수 있습니다.

위와 같은 시나리오를 방지하려면 다음과 같은 예방 조치를 수행하세요.

* 호출 중에 발생할 수 있는 오류를 고려하여 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문 내에 JS interop 호출을 래핑합니다. 자세한 내용은 <xref:blazor/fundamentals/handle-errors?pivots=server#javascript-interop-server>를 참조하세요.
* 다른 동작을 수행하기 전에 JS interop 호출에서 반환된 데이터(오류 메시지 포함)의 유효성을 검사합니다.

### <a name="net-methods-invoked-from-the-browser"></a>브라우저에서 호출된 .NET 메서드

JavaScript에서 .NET 메서드로의 호출은 신뢰하지 마시기 바랍니다. .NET 메서드가 JavaScript에 노출된 경우 .NET 메서드가 어떻게 호출되었는지 살펴보세요.

* JavaScript에 노출된 모든 .NET 메서드는 앱에 대한 공용 엔드포인트를 취급할 때와 마찬가지로 취급합니다.
  * 입력의 유효성을 검사합니다.
    * 값이 예상 범위 내에 있는지 확인합니다.
    * 사용자에게 요청된 작업을 수행할 권한이 있는지 확인합니다.
  * .NET 메서드 호출의 일부로 과도한 리소스를 할당하지 않습니다. 예를 들어 CPU 및 메모리 사용에 대한 검사를 수행하고 제한을 설정합니다.
  * 정적 메서드와 인스턴스 메서드가 JavaScript 클라이언트에 노출될 수 있음을 고려합니다. 적절한 제약 조건을 사용하여 상태를 공유하는 것이 필요한 디자인이 아닌 이상 여러 세션 간에 상태를 공유하지 않습니다.
    * 처음에 DI(종속성 주입)를 통해 생성된 `DotNetReference` 개체를 통해 노출되는 인스턴스 메서드의 경우 해당 개체를 범위가 지정된 개체로 등록해야 합니다. 이는 Blazor Server 앱에서 사용하는 모든 DI 서비스에 적용됩니다.
    * 정적 메서드의 경우, 앱이 서버 인스턴스의 모든 사용자 간에 명시적으로 상태를 공유하는 디자인이 아닌 이상 클라이언트로 범위를 지정할 수 없는 상태를 설정하지 않습니다.
  * 사용자가 제공한 데이터를 JavaScript 호출의 매개 변수로 전달하지 않습니다. 반드시 매개 변수로 데이터를 전달해야 하는 경우에는 JavaScript 코드가 [XSS(교차 사이트 스크립팅)](#cross-site-scripting-xss) 취약성을 유발하지 않으면서 데이터 전달을 처리하는지 확인합니다. 예를 들어, 요소의 `innerHTML` 속성을 설정하여 DOM(문서 개체 모델)에 사용자가 제공한 데이터를 쓰지 않습니다. [CSP(콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP)를 사용하여 `eval` 및 그 밖의 안전하지 않은 JavaScript 기본 형식을 사용하지 않도록 설정하는 것이 좋습니다.
* 프레임워크의 디스패치 구현 위에 .NET 호출의 사용자 지정 디스패치를 구현하지 않습니다. .NET 메서드를 브라우저에 노출하는 것은 고급 시나리오로, 일반적인 Blazor 개발에서는 권장되지 않습니다.

### <a name="events"></a>이벤트

이벤트는 Blazor Server 앱에 대한 진입점을 제공합니다. 웹앱에서 엔드포인트를 보호하기 위한 규칙이 Blazor Server 앱의 이벤트 처리에도 동일하게 적용됩니다. 악의적인 클라이언트는 이벤트에 대한 페이로드로서 어떤 데이터도 전송할 수 있습니다.

예를 들어:

* `<select>`에 대한 변경 이벤트는 앱이 클라이언트에 제공한 옵션에 포함되지 않는 값을 보낼 수 있습니다.
* `<input>`은 클라이언트 쪽 유효성 검사를 우회하고 서버로 임의의 텍스트 데이터를 보낼 수 있습니다.

앱은 해당 앱에서 처리하는 모든 이벤트에 대한 데이터의 유효성을 검사해야 합니다. Blazor 프레임워크 [forms 구성 요소](xref:blazor/forms-validation)는 기본적인 유효성 검사를 수행합니다. 앱에서 사용자 지정 forms 구성 요소를 사용하는 경우, 그에 맞게 이벤트 데이터의 유효성을 검사하는 사용자 지정 코드를 작성해야 합니다.

Blazor Server 이벤트는 비동기적이므로 앱이 새 렌더링을 생성하여 대응할 시간을 갖기 전에 서버로 여러 이벤트를 디스패치할 수 있습니다. 이 과정에서 몇 가지 고려해야 할 보안 문제가 발생합니다. 앱에서 클라이언트 작업을 제한하는 작업은 이벤트 처리기 내에서 수행해야 하며 현재 렌더링된 보기 상태에 종속되지 않아야 합니다.

사용자가 카운터를 최대 세 차례 늘릴 수 있도록 허용하는 counter 구성 요소를 살펴보겠습니다. 카운터를 늘리는 단추는 `count`의 값에 따라 조건부로 정해집니다.

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

클라이언트는 프레임워크가 이 구성 요소의 새 렌더링을 생성하기 전에 하나 이상의 증분 이벤트를 디스패치할 수 있습니다. 따라서 단추가 UI에 의해 충분히 빠르게 제거되지 않기 때문에 사용자에 의해 `count`가 ‘세 차례보다 많이’ 늘어날 수 있습니다. 다음 예제에는 세 차례의 `count` 증분이라는 제한을 올바르게 설정하는 방법이 나와 있습니다.

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

처리기 안에 `if (count < 3) { ... }` 검사를 추가한 결과 `count`를 늘린다는 결정이 현재 앱 상태를 기준으로 이루어집니다. 이 결정은 이전 예제에서처럼 일시적으로 오래되었을 수 있는 UI의 상태를 기준으로 이루어지지 않습니다.

### <a name="guard-against-multiple-dispatches"></a>여러 디스패치를 방지하는 가드

이벤트 콜백이 장기 작업을 비동기적으로 호출하는 경우(예: 외부 서비스 또는 데이터베이스에서 데이터를 페치하는 경우) 가드를 사용하는 것이 좋습니다. 가드는 작업이 진행되는 동안 시각적 피드백을 사용하여 사용자가 여러 작업을 큐에 대기시키는 것을 차단할 수 있습니다. 다음 구성 요소 코드는 `GetForecastAsync`가 서버에서 데이터를 가져오는 동안 `isLoading`을 `true`로 설정합니다. `isLoading`이 `true`인 동안에는 UI에서 단추가 사용하지 않도록 설정됩니다.

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

위 예제에서 살펴본 가드 패턴은 백그라운드 작업이 `async`-`await` 패턴으로 비동기적으로 실행될 경우에 작동합니다.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>조기에 취소하여 use-after-dispose 방지

[여러 디스패치를 방지하는 가드](#guard-against-multiple-dispatches) 섹션에서 설명한 것과 같이 가드를 사용하는 것 외에도, 구성 요소가 폐기된 경우 <xref:System.Threading.CancellationToken>을 사용하여 장기 작업을 취소하는 방안을 고려할 수 있습니다. 이 접근 방식에는 구성 요소에서 *use-after-dispose* 를 방지할 수 있다는 이점도 있습니다.

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>많은 양의 데이터를 생성하는 이벤트 방지

`oninput` 또는 `onscroll`과 같은 일부 DOM 이벤트는 많은 양의 데이터를 생성할 수 있습니다. Blazor 서버 앱에서 이러한 이벤트를 사용하는 일이 없도록 하세요.

## <a name="additional-security-guidance"></a>추가 보안 지침

ASP.NET Core 앱을 보호하기 위한 지침은 Blazor Server 앱에도 적용되며 다음 섹션에서 설명합니다.

* [로깅 및 중요한 데이터](#logging-and-sensitive-data)
* [HTTPS를 사용하여 전송 중인 정보 보호](#protect-information-in-transit-with-https)
* [XSS(교차 사이트 스크립팅)](#cross-site-scripting-xss)
* [교차 원본 보호](#cross-origin-protection)
* [클릭재킹](#click-jacking)
* [오픈 리디렉션](#open-redirects)

### <a name="logging-and-sensitive-data"></a>로깅 및 중요한 데이터

클라이언트와 서버 간의 JS interop 상호 작용은 <xref:Microsoft.Extensions.Logging.ILogger> 인스턴스를 사용하여 서버 로그에 기록됩니다. Blazor는 실제 이벤트나 JS interop 입력 및 출력과 같은 중요한 정보를 로깅하지 않습니다.

서버에서 오류가 발생할 경우 프레임워크가 이를 클라이언트에 알리고 세션을 중단합니다. 기본적으로 클라이언트는 일반 오류 메시지를 수신하며, 이 메시지는 브라우저의 개발자 도구에서 볼 수 있습니다.

클라이언트 쪽 오류는 호출 스택을 포함하지 않으며 오류의 원인에 대한 세부 정보를 제공하지 않지만 서버 로그에는 이러한 정보가 포함되어 있습니다. 개발 단계에서는 [자세한 오류를 사용하도록 설정](xref:blazor/fundamentals/handle-errors#blazor-server-detailed-circuit-errors)하여 클라이언트에서 중요한 오류 정보를 볼 수 있도록 설정할 수 있습니다.

> [!WARNING]
> 인터넷에서 사용되는 클라이언트로 오류 정보를 노출하는 것은 항상 피해야 하는 보안 위험입니다.

### <a name="protect-information-in-transit-with-https"></a>HTTPS를 사용하여 전송 중인 정보 보호

Blazor Server는 클라이언트와 서버 간 통신을 위해 SignalR을 사용합니다. Blazor Server는 일반적으로 SignalR이 협상하는 전송(일반적으로 WebSocket)을 사용합니다.

Blazor Server는 서버와 클라이언트 간에 전송되는 데이터의 무결성과 기밀성을 보장하지 않으므로 항상 HTTPS를 사용하세요.

### <a name="cross-site-scripting-xss"></a>XSS(교차 사이트 스크립팅)

XSS(교차 사이트 스크립팅)를 사용하면 권한이 없는 주체가 브라우저의 컨텍스트에서 임의의 논리를 실행할 수 있습니다. 손상된 앱은 클라이언트에서 임의의 코드를 실행할 수 있게 됩니다. 이 취약성은 서버에 대해 몇 가지 악의적인 작업을 수행하는 데 사용될 수 있습니다.

* 서버로 가짜/잘못된 이벤트 디스패치.
* 실패한/잘못된 렌더링 완료 디스패치.
* 렌더링 완료의 디스패치 차단.
* JavaScript에서 .NET으로의 interop 호출 디스패치.
* .NET에서 JavaScript로의 interop 호출에 대한 응답 수정.
* .NET이 JS interop 결과로 디스패치되는 것을 차단.

Blazor Server 프레임워크는 위와 같은 위협으로부터 보호하기 위한 단계를 수행합니다.

* 클라이언트에서 렌더링 일괄 처리를 승인하지 않는 경우 새 UI 업데이트의 생성을 중지합니다. <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>로 구성됩니다.
* 클라이언트에서 응답을 받지 않은 상태에서 1분이 지나면 .NET에서 JavaScript로의 호출을 시간 초과 처리합니다. <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>로 구성됩니다.
* JS interop 중에 브라우저에서 들어오는 모든 입력에 대해 다음과 같은 기본적인 유효성 검사를 수행합니다.
  * .NET 참조가 유효하며 .NET 메서드에 예상하는 형식입니다.
  * 데이터 형식이 잘못되지 않았습니다.
  * 페이로드에 메서드에 대한 올바른 인수 개수가 있습니다.
  * 메서드를 호출하기 전에 인수나 결과를 올바르게 역직렬화할 수 있습니다.
* 브라우저에서 디스패치된 이벤트로부터 들어오는 모든 입력에 대해 다음과 같은 기본적인 유효성 검사를 수행합니다.
  * 이벤트의 형식이 유효합니다.
  * 이벤트의 데이터를 역직렬화할 수 있습니다.
  * 이벤트에 연결된 이벤트 처리기가 있습니다.

프레임워크에서 구현하는 보호 기능에 더해, 개발자가 위협으로부터 보호하기 위해 앱을 코딩하고 적절한 조치를 취해야 합니다.

* 이벤트를 처리할 때 항상 데이터의 유효성을 검사합니다.
* 잘못된 데이터가 수신되면 적절한 조치를 취합니다.
  * 데이터를 무시하고 반환합니다. 이렇게 하면 앱에서 요청을 계속 처리할 수 있습니다.
  * 앱에서 입력이 정상이 아니고 정상 클라이언트에 의해 생성될 수 없는 값임을 확인한 경우 예외를 throw합니다. 예외를 throw하면 회로가 중단되고 세션이 종료됩니다.
* 로그에 포함된 렌더링 일괄 처리 완료에서 제공하는 오류 메시지를 신뢰하지 않습니다. 오류는 클라이언트가 제공하는 것인데, 클라이언트가 손상되었을 수 있으므로 일반적으로 신뢰할 수 없습니다.
* JavaScript와 .NET 메서드 사이의 두 방향 모두에서 JS interop 호출의 입력을 신뢰하지 않습니다.
* 앱은 인수나 결과가 올바르지 역직렬화되는 경우에도 인수와 결과의 내용이 올바른지 확인하기 위해 유효성을 검사할 책임이 있습니다.

XSS 취약성이 존재하기 위해서는 앱이 렌더링된 페이지에 사용자 입력을 포함해야 합니다. Blazor Server 구성 요소는 `.razor` 파일의 태그가 절차적 C# 논리로 변환되는 컴파일 시간 단계를 실행합니다. C# 논리는 런타임에 요소, 텍스트 및 자식 구성 요소를 설명하는 ‘렌더링 트리’를 빌드합니다. 이는 JavaScript 명령 시퀀스를 통해 브라우저의 DOM에 적용됩니다(또는 사전 렌더링의 경우 HTML로 직렬화됩니다).

* Razor 구문은 텍스트만 쓸 수 있는 명령을 통해 DOM에 추가되기 때문에 일반적인 Razor 구문을 통해 렌더링된 사용자 입력(예: `@someStringValue`)은 XSS 취약성을 노출하지 않습니다. 값에 HTML 마크업이 포함된 경우에도 이 값은 정적 텍스트로 표시됩니다. 사전 렌더링을 수행하는 경우 출력은 HTML로 인코딩되며 이 또한 콘텐츠를 정적 텍스트로 표시합니다.
* 스크립트 태그는 허용되지 않으며 앱의 구성 요소 렌더링 트리에 포함해서는 안 됩니다. 스크립트 태그가 구성 요소의 마크업에 포함된 경우 컴파일 시간 오류가 생성됩니다.
* 구성 요소 작성자는 Razor를 사용하지 않고 C#에서 구성 요소를 작성할 수 있습니다. 구성 요소 작성자는 출력을 내보낼 때 올바른 API를 사용해야 합니다. 예를 들어 XSS 취약성을 생성할 수 있는 `builder.AddMarkupContent(0, someUserSuppliedString)` 대신 `builder.AddContent(0, someUserSuppliedString)`을 사용하세요.

XSS 공격으로부터 보호하기 위한 조치의 일환으로 [CSP(콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP)와 같은 XSS 위험 완화를 구현하는 것이 좋습니다.

자세한 내용은 <xref:security/cross-site-scripting>를 참조하세요.

### <a name="cross-origin-protection"></a>교차 원본 보호

교차 원본 공격에서는 다른 원본의 클라이언트가 서버에 대한 동작을 수행합니다. 이때 진행되는 악의적인 동작은 보통 GET 요청이나 양식 POST(교차 사이트 요청 위조/CSRF)이며, 악성 WebSocket을 여는 것도 가능합니다. Blazor Server 앱은 [허브 프로토콜을 사용하는 다른 모든 SignalR 앱에서 제공하는 보장과 동일한 보장](xref:signalr/security)을 제공합니다.

* Blazor Server 앱은 원본 간 액세스를 차단하기 위한 조치를 취하지 않은 이상 원본 간에 액세스할 수 있습니다. 교차 소스 액세스를 사용하지 않도록 설정하려면 파이프라인에 CORS 미들웨어를 추가하고 Blazor 엔드포인트 메타데이터에 <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute>을 추가하여 엔드포인트에서 CORS를 사용하지 않도록 설정하거나 [교차 소스 리소스 공유에 대해 SignalR을 구성](xref:signalr/security#cross-origin-resource-sharing)하여 허용되는 소스 집합을 제한합니다.
* CORS가 사용하도록 설정된 경우에는 CORS 구성에 따라 앱을 보호하는 추가 단계가 필요할 수 있습니다. CORS가 전역적으로 사용하도록 설정된 경우 엔드포인트 경로 빌더에서 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>를 호출한 후에 엔드포인트 메타데이터에 <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> 메타데이터를 추가하여 Blazor Server 허브에 대해 CORS를 사용하지 않도록 설정할 수 있습니다.

자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.

### <a name="click-jacking"></a>클릭재킹

클릭재킹은 특정 사이트를 다른 원본의 사이트 안에서 `<iframe>`으로 렌더링하여 사용자가 공격 대상 사이트에서 동작을 수행하도록 유도하는 공격입니다.

앱이 `<iframe>` 내에서 렌더링되지 않도록 보호하려면 [CSP(콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 및 `X-Frame-Options` 헤더를 사용하세요. 자세한 내용은 [MDN 웹 문서: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)를 참조하세요.

### <a name="open-redirects"></a>오픈 리디렉션

Blazor Server 앱 세션이 시작되면 서버는 세션 시작 과정의 일환으로 전송된 URL에 대해 기본적인 유효성 검사를 수행합니다. 프레임워크는 기준 URL이 현재 URL의 부모인지 확인한 후에 회로를 설정합니다. 프레임워크에서 추가로 수행하는 검사는 없습니다.

사용자가 클라이언트에서 링크를 선택하면 링크의 URL이 서버로 전송되어 이를 바탕으로 수행할 작업이 결정됩니다. 예를 들어 앱이 클라이언트 쪽 탐색을 수행하거나 새 위치로 이동하도록 브라우저에 알려줄 수 있습니다.

구성 요소는 <xref:Microsoft.AspNetCore.Components.NavigationManager>를 사용하여 프로그래밍 방식으로 탐색 요청을 트리거할 수도 있습니다. 이러한 시나리오에서는 앱이 클라이언트 쪽 탐색을 수행하거나 브라우저에 새 위치로 이동하도록 알려줍니다.

구성 요소는 다음을 수행해야 합니다.

* 탐색 호출 인수의 일부로 사용자 입력을 사용하지 않습니다.
* 앱에서 대상이 허용되는지 확인하기 위해 인수의 유효성을 검사합니다.

이렇게 하지 않으면 악의적인 사용자가 공격자가 제어하는 사이트로 이동하도록 브라우저를 강제할 수 있습니다. 이 시나리오에서 공격자는 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 메서드 호출의 일환으로 사용자 입력을 사용하도록 앱을 유도합니다.

이 조건은 앱의 일부로 링크를 렌더링하는 경우에도 적용됩니다.

* 가능한 경우 상대 링크를 사용합니다.
* 절대 링크 대상을 페이지에 포함하기 전에 절대 링크 대상이 유효한지 확인합니다.

자세한 내용은 <xref:security/preventing-open-redirects>를 참조하세요.

## <a name="security-checklist"></a>보안 검사 목록

다음 보안 고려 사항 목록은 모든 항목을 포함하지 않습니다.

* 이벤트 인수의 유효성을 검사합니다.
* JS interop 호출 입력 및 결과의 유효성을 검사합니다.
* .NET에서 JS interop로의 호출에 대한 사용자 입력을 사용하지 않습니다(또는 미리 유효성을 검사합니다).
* 클라이언트가 메모리를 무제한으로 할당하지 못하도록 합니다.
  * 구성 요소 내의 데이터.
  * 클라이언트로 반환되는 `DotNetObject` 참조.
* 여러 디스패치를 방지합니다.
* 구성 요소가 폐기된 경우 장기 작업을 취소합니다.
* 많은 양의 데이터를 생성하는 이벤트를 방지합니다.
* <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>에 대한 호출의 일환으로 사용자 입력을 사용하지 않고, 반드시 사용해야 하는 경우 허용되는 원본 집합을 기준으로 사용자 입력에서 URL의 유효성을 검사합니다.
* UI 상태가 아닌 구성 요소 상태를 기준으로 권한 부여를 결정을 내립니다.
* [CSP(콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP)를 사용하여 XSS 공격으로부터 보호하는 것이 좋습니다.
* CSP 및 [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)를 사용하여 클릭재킹으로부터 보호하는 것이 좋습니다.
* CORS를 사용하도록 설정하거나 Blazor 앱에 대해 CORS를 명시적으로 사용하지 않도록 설정할 때 CORS 설정이 적절한지 확인합니다.
* Blazor 앱의 서버 측 제한이 허용할 수 없는 위험 수준을 유발하지 않으면서 허용할 수 있는 사용자 환경을 제공하는지 테스트합니다.
