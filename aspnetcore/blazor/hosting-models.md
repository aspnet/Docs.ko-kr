---
title: ASP.NET Core Blazor 호스팅 모델
author: guardrex
description: Blazor WebAssembly 및 Blazor Server 호스팅 모델을 이해합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
uid: blazor/hosting-models
ms.openlocfilehash: a6f1c88b8e93c0d8ccfebca482895ebab8d18a81
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506918"
---
# <a name="aspnet-core-no-locblazor-hosting-models"></a>ASP.NET Core Blazor 호스팅 모델

작성자: [Daniel Roth](https://github.com/danroth27)

Blazor는 [WebAssembly](https://webassembly.org/) 기반 .NET 런타임( *Blazor WebAssembly* )의 브라우저에서 클라이언트 쪽을 실행하거나 ASP.NET Core( *Blazor Server* )에서 서버 쪽을 실행하도록 설계된 웹 프레임워크입니다. 호스팅 모델에 관계없이 앱 및 구성 요소 모델은 ‘동일합니다’.

## Blazor WebAssembly

기본 Blazor 호스팅 모델은 WebAssembly의 브라우저에서 클라이언트 쪽을 실행하는 것입니다. Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 브라우저에 다운로드됩니다. 해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다. UI 업데이트 및 이벤트 처리는 동일한 프로세스 내에서 발생합니다. 앱 자산은 클라이언트에 정적 콘텐츠를 제공할 수 있는 웹 서버 또는 서비스에 정적 파일로 배포됩니다.

![Blazor WebAssembly: Blazor 앱은 브라우저 내의 UI 스레드에서 실행됩니다.](hosting-models/_static/blazor-webassembly.png)

해당 파일을 제공하는 백 엔드 ASP.NET Core 앱을 사용하지 않는 배포용으로 Blazor WebAssembly 앱을 만드는 경우 ‘독립 실행형’ Blazor WebAssembly 앱이라고 합니다. 해당 파일을 제공하는 백 엔드 앱을 사용하는 배포용으로 앱을 만드는 경우 ‘호스트된’ Blazor WebAssembly 앱이라고 합니다. 호스트된 Blazor WebAssembly 앱은 일반적으로 웹 API 호출 또는 [SignalR](xref:signalr/introduction)(<xref:tutorials/signalr-blazor-webassembly>)을 사용하여 네트워크를 통해 서버와 상호 작용합니다.

`blazor.webassembly.js` 스크립트는 프레임워크 및 핸들에서 제공됩니다.

* .NET 런타임, 앱, 앱 종속성 다운로드
* 앱을 실행할 런타임 초기화

Blazor WebAssembly 호스팅 모델은 다음과 같은 몇 가지 혜택을 제공합니다.

* .NET 서버 쪽 종속성이 없습니다. 클라이언트에 다운로드한 후 앱이 완전히 작동합니다.
* 클라이언트 리소스와 기능은 완전히 이용됩니다.
* 작업이 서버에서 클라이언트로 오프로드됩니다.
* 앱을 호스트하는 데 ASP.NET Core 웹 서버가 필요하지 않습니다. CDN(Content Delivery Network)에서 앱을 제공하는 것과 같은 서버리스 배포 시나리오가 가능합니다.

Blazor WebAssembly 호스팅 모델에는 다음과 같은 제한 사항이 있습니다.

* 앱이 브라우저 기능으로 제한됩니다.
* 기능을 갖춘 클라이언트 하드웨어 및 소프트웨어(예: WebAssembly 지원)가 필요합니다.
* 다운로드 크기가 더 크고 앱 로드 시간이 더 깁니다.
* .NET 런타임 및 도구 지원의 완성도가 더 낮습니다. 예를 들어 [.NET Standard](/dotnet/standard/net-standard) 지원 및 디버깅에 대한 제한 사항이 있습니다.

Blazor WebAssembly 앱을 만들려면 <xref:blazor/tooling>를 참조하세요.

호스트된 Blazor 앱 모델은 [Docker 컨테이너](/dotnet/standard/microservices-architecture/container-docker-introduction/index)를 지원합니다. Visual Studio에서 Docker 지원이 필요하면 호스트된 Blazor WebAssembly 솔루션의 `Server` 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **Docker 지원** 을 선택합니다.

## Blazor Server

Blazor Server 호스팅 모델을 사용하는 경우 서버의 ASP.NET Core 앱 내에서 앱이 실행됩니다. UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.

![브라우저는 SignalR 연결을 통해 서버의 앱(ASP.NET Core 앱 내에서 호스트됨)과 상호 작용합니다.](hosting-models/_static/blazor-server.png)

ASP.NET Core 앱은 앱의 `Startup` 클래스를 참조하여 다음을 추가합니다.

* 서버 쪽 서비스
* 요청 처리 파이프라인에 앱 추가

클라이언트에서 `blazor.server.js` 스크립트는 서버와의 SignalR 연결을 설정합니다. 이 스크립트는 ASP.NET Core 공유 프레임워크의 포함 리소스에서 클라이언트 쪽 앱에 제공됩니다. 클라이언트 쪽 앱은 필요에 따라 앱 상태를 유지하고 복원해야 합니다. 

Blazor Server 호스팅 모델은 다음과 같은 몇 가지 혜택을 제공합니다.

* 다운로드 크기가 Blazor WebAssembly 앱보다 작고 앱이 훨씬 빠르게 로드됩니다.
* 앱이 .NET Core 호환 API 사용을 포함하여 서버 기능을 모두 활용합니다.
* 서버의 .NET Core가 앱을 실행하는 데 사용되므로 디버깅과 같은 기존 .NET 도구가 정상적으로 작동합니다.
* 씬 클라이언트가 지원됩니다. 예를 들어 Blazor Server 앱은 WebAssembly를 지원하지 않는 브라우저와 리소스가 제한된 디바이스에서 작동합니다.
* 앱 구성 요소 코드를 비롯한 앱의 .NET/C# 코드베이스가 클라이언트에 제공되지 않습니다.

> [!IMPORTANT]
> Blazor Server 앱은 첫 번째 클라이언트 요청에 대응하여 미리 렌더링되며 이에 따라 서버의 UI 상태가 만들어집니다. 클라이언트가 SignalR 연결을 만들려는 경우 **클라이언트는 동일한 서버에 다시 연결해야 합니다**. 둘 이상의 백 엔드 서버를 사용하는 Blazor Server 앱은 SignalR 연결에 사용할 ‘고정 세션’을 구현해야 합니다. 자세한 내용은 [서버에 대한 연결](#connection-to-the-server) 섹션을 참조하세요.

Blazor Server 호스팅 모델에는 다음과 같은 제한 사항이 있습니다.

* 일반적으로 대기 시간이 더 깁니다. 모든 사용자 조작에 네트워크 홉이 포함됩니다.
* 오프라인 지원이 없습니다. 클라이언트 연결에 실패하면 앱 작동이 중단됩니다.
* 여러 사용자가 있는 앱의 경우 확장성 구현이 어렵습니다. 서버에서 여러 개의 클라이언트 연결을 관리하고 클라이언트 상태를 처리해야 합니다.
* 앱을 제공하려면 ASP.NET Core 서버가 필요합니다. CDN(Content Delivery Network)에서 앱을 제공하는 것과 같은 서버리스 배포 시나리오가 가능하지 않습니다.

Blazor Server 앱을 만들려면 <xref:blazor/tooling>를 참조하세요.

Blazor Server 앱 모델은 [Docker 컨테이너](/dotnet/standard/microservices-architecture/container-docker-introduction/index)를 지원합니다. Visual Studio에서 Docker 지원이 필요하면 Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **Docker 지원** 을 선택합니다.

### <a name="comparison-to-server-rendered-ui"></a>서버에서 렌더링된 UI와 비교

Blazor Server 앱을 이해하는 한 가지 방법은 Razor 뷰 또는 Razor Pages를 사용하여 ASP.NET Core 앱에서 UI를 렌더링하는 기존 모델과의 차이점을 파악하는 것입니다. 두 모델 모두 [Razor 언어](xref:mvc/views/razor)를 사용하여 렌더링할 HTML 콘텐츠를 설명하지만, 태그 렌더링 ‘방법’에 상당한 차이가 있습니다.

Razor 페이지 또는 뷰를 렌더링하는 경우 Razor 코드의 각 줄이 HTML을 텍스트 형식으로 내보냅니다. 렌더링 후에 서버는 생성된 상태를 포함하여 페이지 또는 뷰 인스턴스를 삭제합니다. 페이지에 대한 다른 요청이 발생하는 경우(예: 서버 유효성 검사에 실패하고 유효성 검사 요약이 표시되는 경우)

* 전체 페이지가 HTML 텍스트로 다시 렌더링됩니다.
* 페이지가 클라이언트로 전송됩니다.

Blazor 앱은 ‘구성 요소’라는 재사용 가능한 UI 요소로 구성됩니다. 구성 요소에는 C# 코드, 태그 및 기타 구성 요소가 포함됩니다. 구성 요소를 렌더링하는 경우 Blazor는 HTML 또는 XML DOM(문서 개체 모델)과 유사한, 포함된 구성 요소 그래프를 생성합니다. 이 그래프에는 속성과 필드에 저장된 구성 요소 상태가 포함됩니다. Blazor는 구성 요소 그래프를 평가하여 태그의 이진 표현을 생성합니다. 이진 형식과 관련해서 다음 사항을 확인합니다.

* 미리 렌더링하는 동안&dagger; HTML 텍스트로 전환할 수 있습니다.
* 정기적으로 렌더링하는 동안 태그를 효율적으로 업데이트하는 데 사용할 수 있습니다.

&dagger;미리 렌더링: 요청한 Razor 구성 요소가 서버에서 정적 HTML로 컴파일되고 클라이언트로 전송되며, 여기서 사용자에게 렌더링됩니다. 클라이언트와 서버 간에 연결이 설정된 후 구성 요소의 미리 렌더링된 정적 요소가 대화형 요소로 바뀝니다. 미리 렌더링하면 사용자에게 앱의 응답성이 향상된 느낌을 줍니다.

Blazor의 UI 업데이트는 다음을 통해 트리거됩니다.

* 사용자 조작(예: 단추 선택)
* 앱 트리거(예: 타이머)

구성 요소 그래프가 다시 렌더링되고, UI *diff*(차이)가 계산됩니다. 이 diff는 클라이언트에서 UI를 업데이트하는 데 필요한 최소한의 DOM 편집 집합입니다. diff는 클라이언트에 이진 형식으로 전송되고 브라우저에서 적용됩니다.

사용자가 클라이언트에서 구성 요소를 벗어나면 구성 요소가 삭제됩니다. 사용자가 구성 요소를 조작하는 동안에는 구성 요소 상태(서비스, 리소스)가 서버 메모리에 유지되어야 합니다. 서버에서 많은 구성 요소의 상태를 동시에 유지 관리할 수 있으므로 메모리 소모는 해결해야 하는 문제입니다. 서버 메모리 사용을 최적화하도록 Blazor Server 앱을 작성하는 방법에 관한 자세한 내용은 <xref:blazor/security/server/threat-mitigation>을 참조하세요.

### <a name="circuits"></a>회로

Blazor Server 앱은 [ASP.NET Core SignalR](xref:signalr/introduction)을 토대로 빌드됩니다. 각 클라이언트는 ‘회로’라는 하나 이상의 SignalR 연결을 통해 서버와 통신합니다. 회로는 일시적인 네트워크 중단을 허용할 수 있는 SignalR 연결을 통한 Blazor의 추상화입니다. Blazor 클라이언트는 SignalR 연결이 끊어진 것을 확인할 경우 새 SignalR 연결을 사용하여 서버에 다시 연결합니다.

Blazor Server 앱에 연결된 각 브라우저 화면(브라우저 탭 또는 iframe)은 SignalR 연결을 사용합니다. 이 동작도 서버에서 렌더링된 일반적인 앱과는 다른 중요한 차이점입니다. 서버에서 렌더링된 앱의 경우, 여러 브라우저 화면에서 동일한 앱을 열어도 일반적으로 서버의 리소스 수요가 증가하지 않습니다. Blazor Server 앱의 경우 각 브라우저 화면에 개별 회로 및 개별 구성 요소 상태 인스턴스가 필요하며 서버에서 관리해야 합니다.

Blazor는 브라우저 탭을 닫거나 외부 URL로 이동하는 것을 *정상적인* 종료로 간주합니다. 정상적인 종료 시 회로 및 연결된 리소스가 즉시 해제됩니다. 네트워크 중단 등으로 인해 클라이언트 연결이 비정상적으로 끊어질 수도 있습니다. Blazor Server는 클라이언트가 다시 연결할 수 있도록 연결이 끊어진 회로를 구성 가능한 간격 동안 저장합니다.

Blazor Server에서는 코드를 통해 사용자 회로 상태 변경 시 코드를 실행할 수 있게 해주는 ‘회로 처리기’를 정의할 수 있습니다. 자세한 내용은 <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>를 참조하세요.

### <a name="ui-latency"></a>UI 대기 시간

UI 대기 시간은 작업 시작부터 UI가 업데이트되는 시간까지 소요되는 시간입니다. 사용자에게 앱의 응답성이 높다는 느낌을 주려면 UI 대기 시간 값이 작아야 합니다. Blazor Server 앱에서는 각 작업이 서버로 전송되어 처리되고 UI diff가 다시 전송됩니다. 따라서 UI 대기 시간은 네트워크 대기 시간과 작업 처리 중 서버 대기 시간의 합계입니다.

프라이빗 회사 네트워크로 제한된 비즈니스 앱의 경우, 일반적으로 네트워크 대기 시간이 사용자의 대기 시간 인식에 미치는 영향이 거의 없습니다. 인터넷을 통해 배포된 앱의 경우, 특히 사용자가 지리적으로 광범위하게 분산되어 있으면 대기 시간이 감지될 수 있습니다.

메모리 사용량도 앱 대기 시간에 영향을 줄 수 있습니다. 메모리 사용량이 증가하면 가비지 수집 또는 디스크로 메모리 페이징이 자주 발생하므로 앱 성능이 저하되고 UI 대기 시간이 증가합니다.

네트워크 대기 시간과 메모리 사용량을 줄여 UI 대기 시간을 최소화하기 위해 Blazor Server 앱을 최적화해야 합니다. 네트워크 대기 시간을 측정하는 방법에 대한 자세한 내용은 <xref:blazor/host-and-deploy/server#measure-network-latency>를 참조하세요. SignalR 및 Blazor에 대한 자세한 내용은 다음을 참조하세요.

* <xref:blazor/host-and-deploy/server>
* <xref:blazor/security/server/threat-mitigation>

### <a name="connection-to-the-server"></a>서버에 연결

Blazor Server 앱을 사용하려면 SignalR이 서버에 연결되어 있어야 합니다. 연결이 끊어지면 앱이 서버에 다시 연결합니다. 클라이언트 상태가 서버의 메모리에 있는 한, 클라이언트 세션이 상태 손실 없이 다시 시작됩니다.

Blazor Server 앱은 첫 번째 클라이언트 요청에 대응하여 미리 렌더링되며 이에 따라 서버의 UI 상태가 만들어집니다. 클라이언트가 SignalR 연결을 만들려는 경우 동일한 서버에 다시 연결해야 합니다. 둘 이상의 백 엔드 서버를 사용하는 Blazor Server 앱은 SignalR 연결에 사용할 ‘고정 세션’을 구현해야 합니다.

Blazor Server 앱에 [Azure SignalR Service](/azure/azure-signalr)를 사용하는 것이 좋습니다. 이 서비스를 사용하면 Blazor Server 앱을 다수의 동시 SignalR 연결로 스케일 업할 수 있습니다. 서비스의 `ServerStickyMode` 옵션 또는 구성 값을 `Required`로 설정하여 Azure SignalR Service에 고정 세션을 사용합니다. 자세한 내용은 <xref:blazor/host-and-deploy/server#signalr-configuration>를 참조하세요.

IIS를 사용하는 경우 ‘애플리케이션 요청 라우팅’을 사용하여 고정 세션을 사용할 수 있습니다. 자세한 내용은 [애플리케이션 요청 라우팅을 사용하여 HTTP 부하 분산](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)을 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/tooling>
* <xref:signalr/introduction>
* <xref:blazor/fundamentals/additional-scenarios>
* <xref:tutorials/signalr-blazor-webassembly>
