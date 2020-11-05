---
title: ASP.NET Core Blazor WebAssembly를 사용하여 프로그레시브 웹 애플리케이션 빌드
author: guardrex
description: 최신 브라우저 기능을 사용하여 데스크톱 앱과 같은 동작을 하는 Blazor 기반 PWA(프로그레시브 웹 애플리케이션)를 빌드하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/progressive-web-app
ms.openlocfilehash: c8ff2fc0f2f4d4e75f535f379ec94ea9de2e3ecb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055700"
---
# <a name="build-progressive-web-applications-with-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core Blazor WebAssembly를 사용하여 프로그레시브 웹 애플리케이션 빌드

작성자: [Steve Sanderson](https://github.com/SteveSandersonMS)

일반적으로 PWA(프로그레시브 웹 애플리케이션)는 최신 브라우저 API 및 기능을 사용하여 데스크톱 앱처럼 동작하는 SPA(단일 페이지 애플리케이션)입니다. Blazor WebAssembly는 표준 기반 클라이언트 쪽 웹앱 플랫폼이므로 다음 기능에 필요한 PWA API를 비롯한 모든 브라우저 API를 사용할 수 있습니다.

* 네트워크 속도와 상관없이 오프라인으로 작동하고 항상 즉시 로드
* 브라우저 창뿐만 아니라 자체 앱 창에서 실행
* 호스트 운영 체제 시작 메뉴, 도킹 또는 홈 화면에서 시작
* 사용자가 앱을 사용하지 않을 때도 백엔드 서버에서 푸시 알림 받기
* 백그라운드에서 자동 업데이트

이러한 앱을 설명하는 데 ‘프로그레시브’가 사용되는 이유는 다음과 같습니다.

* 사용자는 먼저 다른 SPA와 마찬가지로 웹 브라우저 내에서 앱을 검색하고 사용할 수 있습니다.
* 나중에 사용자가 OS에 설치하고 푸시 알림을 사용하도록 설정하는 과정을 진행합니다.

## <a name="create-a-project-from-the-pwa-template"></a>PWA 템플릿에서 프로젝트 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**새 프로젝트 만들기** 대화 상자에서 새 **Blazor WebAssembly 앱** 을 만들 때 **프로그레시브 웹 애플리케이션** 확인란을 선택합니다.

![‘프로그레시브 웹 애플리케이션’ 확인란은 Visual Studio 새 프로젝트 대화 상자에서 선택합니다.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

명령 셸에서 `--pwa` 스위치를 사용하여 PWA 프로젝트를 만듭니다.

```dotnetcli
dotnet new blazorwasm -o MyNewProject --pwa
```

---

필요에 따라 ASP.NET Core에 호스트된 템플릿에서 만든 앱에 대해 PWA를 구성할 수 있습니다. PWA 시나리오는 호스팅 모델과 관계없습니다.

## <a name="installation-and-app-manifest"></a>설치 및 앱 매니페스트

PWA 템플릿을 사용하여 만든 앱을 방문하는 사용자에게는 해당 OS의 시작 메뉴, 도킹 또는 홈 화면에 앱을 설치할 수 있는 옵션이 제공됩니다. 이 옵션이 표시되는 방법은 사용자의 브라우저에 따라 달라집니다. Edge 또는 Chrome과 같은 데스크톱 Chromium 기반 브라우저를 사용하는 경우 URL 표시줄에 **추가** 단추가 나타납니다. 사용자가 **추가** 단추를 선택하면 확인 대화 상자가 표시됩니다.

![Google Chrome의 확인 대화 상자는 사용자에게 ‘MyBlazorPwa’ 앱의 설치 단추를 제공합니다.](progressive-web-app/_static/image2.png)

iOS에서 방문자는 Safari의 **‘공유’** 단추 및 **‘홈 화면에 추가’** 옵션을 사용하여 PWA를 설치할 수 있습니다. Android용 Chrome에서 사용자는 오른쪽 위에 있는 **메뉴** 단추를 탭한 다음 **홈 화면에 추가** 를 선택해야 합니다.

설치된 앱은 주소 표시줄 없이 자체 창에 표시됩니다.

![‘MyBlazorPwa’ 앱은 주소 표시줄 없이 Google Chrome에서 실행됩니다.](progressive-web-app/_static/image3.png)

창의 제목, 색 구성표, 아이콘 또는 기타 세부 정보를 사용자 지정하려면 프로젝트의 `wwwroot` 디렉터리에서 `manifest.json` 파일을 참조하세요. 이 파일의 스키마는 웹 표준에 의해 정의됩니다. 자세한 내용은 [MDN 웹 문서: 웹앱 매니페스트](https://developer.mozilla.org/docs/Web/Manifest)를 참조하세요.

## <a name="offline-support"></a>오프라인 지원

기본적으로 PWA 템플릿 옵션을 사용하여 만든 앱은 오프라인 실행을 지원합니다. 사용자가 온라인 상태인 동안 먼저 앱을 방문해야 합니다. 브라우저는 오프라인으로 작업하는 데 필요한 모든 리소스를 자동으로 다운로드하고 캐시합니다.

> [!IMPORTANT]
> 개발 지원은 변경을 수행하고 테스트하는 일반적인 개발 주기를 방해할 수 있습니다. 따라서 오프라인 지원은 ‘게시된’ 앱에 대해서만 사용할 수 있습니다. 

> [!WARNING]
> 오프라인 사용 가능 PWA를 배포하려는 경우 [몇 가지 중요한 경고 및 주의 사항](#caveats-for-offline-pwas)을 이해해야 합니다. 이러한 시나리오는 오프라인 PWA에 해당하는 것으로 Blazor에 국한되지 않습니다. 오프라인 지원 앱이 작동하는 방식에 대해 가정하기 전에 해당 주의 사항을 읽고 이해해야 합니다.

오프라인 지원의 작동 방식을 확인하려면 다음을 수행합니다.

1. 앱을 게시합니다. 자세한 내용은 <xref:blazor/host-and-deploy/index#publish-the-app>를 참조하세요.
1. HTTPS를 지원하는 서버에 앱을 배포하고 보안 HTTPS 주소를 사용하여 브라우저에서 앱에 액세스합니다.
1. 브라우저의 개발자 도구를 열고 **애플리케이션** 탭에서 호스트에 대한 ‘서비스 작업자’가 등록되었는지 확인합니다.

   ![Google Chrome 개발자 도구 ‘애플리케이션’ 탭에서는 활성화되고 실행 중인 서비스 작업자를 표시합니다.](progressive-web-app/_static/image4.png)

1. 페이지를 다시 로드하고 **네트워크** 탭을 검토합니다. **서비스 작업자** 또는 **메모리 캐시** 는 모든 페이지 자산의 원본으로 나열됩니다.

   ![모든 페이지 자산의 원본을 나타내는 Google Chrome 개발자 도구 ‘네트워크’ 탭](progressive-web-app/_static/image5.png)

1. 브라우저가 네트워크 액세스에 의존하지 않고 앱을 로드하는지 확인하려면 다음 중 하나를 수행합니다.

   * 웹 서버를 종료하고 페이지 다시 로드를 포함해 앱이 계속 정상적으로 작동하지 확인합니다. 마찬가지로 네트워크 연결이 느릴 때 앱이 정상적으로 계속 작동하는지 확인합니다.
   * **네트워크** 탭에서 오프라인 모드를 시뮬레이션하도록 브라우저에 명령합니다.

   ![브라우저 모드 드롭다운이 ‘온라인’에서 ‘오프라인’으로 변경된 Google Chrome 개발자 도구 ‘네트워크’ 탭](progressive-web-app/_static/image6.png)

서비스 작업자를 사용하는 오프라인 지원은 Blazor에 국한되지 않는 웹 표준입니다. 서비스 작업자에 대한 자세한 내용은 [MDN 웹 문서: 서비스 작업자 API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API)를 참조하세요. 서비스 작업자의 일반적인 사용 패턴에 대한 자세한 내용은 [Google Web: 서비스 작업자 수명 주기](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)를 참조하세요.

Blazor의 PWA 템플릿은 다음 두 개의 서비스 작업자 파일을 생성합니다.

* 개발 중에 사용되는 `wwwroot/service-worker.js`
* 앱이 게시된 후에 사용되는 `wwwroot/service-worker.published.js`

두 서비스 작업자 파일 간의 논리를 공유하려면 다음 방법을 고려하세요.

* 공통 논리를 보관할 세 번째 JavaScript 파일을 추가합니다.
* [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts)를 사용하여 두 서비스 작업자 파일에 공통 논리를 로드합니다.

### <a name="cache-first-fetch-strategy"></a>캐시 우선 인출 전략

기본 제공 `service-worker.published.js` 서비스 작업자는 ‘캐시 우선’ 전략을 사용하여 요청을 확인합니다. 즉, 사용자가 네트워크에 액세스할 수 있는지 또는 서버에서 최신 콘텐츠를 사용할 수 있는지와 관계없이 캐시된 콘텐츠를 반환하는 것이 항상 선호됩니다.

캐시 우선 전략은 다음 이유로 유용합니다.

* **안정성을 보장합니다.** 네트워크 액세스가 부울 상태가 아닙니다. 사용자는 단순히 온라인 또는 오프라인 상태가 아닙니다.

  * 사용자의 디바이스는 온라인 상태라고 생각할 수 있지만 네트워크가 너무 느려 대기하는 것이 유리하지 않을 수도 있습니다.
  * 현재 특정 요청을 차단하거나 리디렉션하는 종속 WIFI 포털이 있는 경우와 같이 특정 URL에 대해 잘못된 결과가 네트워크에서 반환될 수도 있습니다.
  
  이로 인해 브라우저의 `navigator.onLine` API가 안정적이지 않으므로 의존해서는 안 됩니다.

* **정확성을 보장합니다.** 오프라인 리소스의 캐시를 빌드할 때 서비스 작업자는 콘텐츠 해시를 사용하여 완전하고 자체 일관성 있는 리소스 스냅샷을 인출했음을 보장합니다. 이 캐시는 원자성 단위로 사용됩니다. 사용자가 이미 캐시한 버전만 사용하게 되므로 네트워크에 최신 리소스를 요청할 필요가 없습니다. 그 밖의 위험 요소는 불일치 및 비호환성입니다(예: 함께 컴파일되지 않은 .NET 어셈블리 버전을 사용하려는 경우).

### <a name="background-updates"></a>백그라운드 업데이트

멘탈 모델로서는, 오프라인 중심 PWA가 설치할 수 있는 모바일 앱처럼 동작한다고 생각할 수 있습니다. 앱은 네트워크 연결과 관계없이 항상 즉시 시작되지만, 설치된 앱 논리는 최신 버전이 아닐 수도 있는 지정 시간 스냅샷에서 제공됩니다.

Blazor PWA 템플릿은 사용자가 방문하여 네트워크에 연결되어 있을 때마다 백그라운드에서 자동으로 업데이트를 시도하는 앱을 생성합니다. 이렇게 작동하는 방법은 다음과 같습니다.

* 컴파일하는 동안 프로젝트는 ‘서비스 작업자 자산 매니페스트’를 생성합니다. 기본적으로 `service-worker-assets.js`라고 합니다. 앱에서 콘텐츠 해시를 포함하여 .NET 어셈블리, JavaScript 파일, CSS 등이 오프라인에서 기능하는 데 필요한 모든 정적 리소스를 매니페스트에서 나열합니다. 이 리소스 목록은 캐시할 리소스를 알 수 있도록 서비스 작업자에 의해 로드됩니다.
* 사용자가 앱을 방문할 때마다 브라우저는 백그라운드에서 `service-worker.js` 및 `service-worker-assets.js`를 다시 요청합니다. 이 파일은 설치된 기존 서비스 작업자와 바이트 단위로 비교됩니다. 서버에서 이러한 파일 중 하나에 대해 변경된 콘텐츠를 반환하는 경우 서비스 작업자는 새 버전의 파일을 설치하려고 합니다.
* 새 버전을 설치하는 경우 서비스 작업자는 오프라인 리소스에 대한 별도의 캐시를 새로 만들고 `service-worker-assets.js`에 나열된 리소스로 캐시를 채우기 시작합니다. 이 논리는 `service-worker.published.js` 내의 `onInstall` 함수에서 구현됩니다.
* 모든 리소스가 오류 없이 로드되고 모든 콘텐츠 해시가 일치하면 프로세스가 성공적으로 완료됩니다. 성공하면 새 서비스 작업자는 ‘활성화 대기’ 상태로 전환됩니다. 사용자가 앱을 닫으면(남아 있는 앱 탭 또는 창 없음) 새 서비스 작업자는 ‘활성’ 상태가 되며 이후 앱 방문에 사용됩니다. 이전 서비스 작업자 및 해당 캐시가 삭제됩니다.
* 프로세스가 성공적으로 완료되지 않으면 새 서비스 작업자 인스턴스가 삭제됩니다. 클라이언트에서 요청을 완료할 수 있는 더 나은 네트워크 연결을 사용할 수 있게 되면 사용자의 다음 방문에서 업데이트 프로세스가 다시 시도됩니다.

서비스 작업자 논리를 편집하여 이 프로세스의 사용자 지정할 수 있습니다. 위의 동작은 Blazor에 국한되지 않지만 PWA 템플릿 옵션에서 제공하는 기본 환경입니다. 자세한 내용은 [MDN 웹 문서: 서비스 작업자 API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API)를 참조하세요.

### <a name="how-requests-are-resolved"></a>요청을 해결하는 방법

[캐시 우선 페치 전략](#cache-first-fetch-strategy) 단원에서 설명한 대로 기본 서비스 작업자는 ‘캐시 우선’ 전략을 사용합니다. 즉, 사용 가능한 경우 캐시된 콘텐츠를 제공하려고 시도합니다. 백엔드 API에서 데이터를 요청하는 경우와 같이 특정 URL에 대해 캐시된 콘텐츠가 없는 경우 서비스 작업자는 일반 네트워크 요청으로 대체합니다. 서버에 연결할 수 있는 경우 네트워크 요청이 성공합니다. 이 논리는 `service-worker.published.js` 내의 `onFetch` 함수 안에서 구현됩니다.

앱의 Razor 구성 요소가 백엔드 API의 데이터 요청에 의존하며 네트워크 사용 불가로 인해 실패한 요청에 대해 친숙한 사용자 환경을 제공하려는 경우에는 앱의 구성 요소 내에서 논리를 구현해야 합니다. 예를 들어 <xref:System.Net.Http.HttpClient> 요청에 대해 `try/catch`를 사용합니다.

### <a name="support-server-rendered-pages"></a>서버에서 렌더링된 페이지 지원

사용자가 `/counter`와 같은 URL 또는 앱의 기타 딥 링크로 처음 이동할 때 발생하는 상황을 고려합니다. 이 경우에는 `/counter`로 캐시된 콘텐츠를 반환하지 않고 대신 Blazor WebAssembly 앱을 시작하기 위해 `/index.html`로 캐시된 콘텐츠를 로드하는 브라우저가 필요합니다. 이러한 초기 요청은 다음과 달리 ‘탐색’ 요청이라고 합니다.

* 이미지, 스타일시트 또는 기타 파일에 대한 `subresource` 요청
* API 데이터에 대한 `fetch/XHR` 요청

기본 서비스 작업자는 탐색 요청에 대한 특별 사례 논리를 포함합니다. 서비스 작업자는 요청한 URL과 관계없이 `/index.html`에 대해 캐시된 콘텐츠를 반환하여 요청을 확인합니다. 이 논리는 `service-worker.published.js` 내의 `onFetch` 함수에서 구현됩니다.

앱에 서버에서 렌더링된 HTML을 반환해야 하고 캐시에서 `/index.html`을 제공하지 않는 특정 URL이 있는 경우 서비스 작업자에서 논리를 편집해야 합니다. `/Identity/`를 포함하는 모든 URL을 서버에 대한 일반 온라인 전용 요청으로 처리해야 하는 경우 `service-worker.published.js` `onFetch` 논리를 수정합니다. 다음 코드를 찾습니다.

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

코드를 다음과 같이 변경합니다.

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

이렇게 하지 않으면 네트워크 연결과 관계없이 서비스 작업자는 URL에 대한 요청을 가로채서 `/index.html`을 사용하여 이를 확인합니다.

### <a name="control-asset-caching"></a>자산 캐싱 제어

프로젝트에서 `ServiceWorkerAssetsManifest` MSBuild 속성을 정의하는 경우 Blazor의 빌드 도구는 지정된 이름을 사용하여 서비스 작업자 자산 매니페스트를 생성합니다. 기본 PWA 템플릿은 다음 속성을 포함하는 프로젝트 파일을 생성합니다.

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

파일은 `wwwroot` 출력 디렉터리에 배치되므로 브라우저는 `/service-worker-assets.js`를 요청하여 이 파일을 검색할 수 있습니다. 이 파일의 콘텐츠를 보려면 텍스트 편집기에서 `/bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js`를 엽니다. 그러나 파일은 각 빌드에서 다시 생성되므로 편집하지 마세요.

기본적으로 이 매니페스트는 다음을 나열합니다.

* 오프라인에서 작동하는 데 필요한 .NET 어셈블리 및 .NET WebAssembly 런타임 파일 등의 Blazor 관리되는 리소스
* 외부 프로젝트 및 NuGet 패키지에서 제공하는 정적 웹 자산을 비롯하여 앱의 `wwwroot` 디렉터리에 게시하기 위한 모든 리소스(예: 이미지, 스타일시트 및 JavaScript 파일)

`service-worker.published.js`의 `onInstall`에서 논리를 편집하여 서비스 작업자에서 이 리소스 중 어떤 항목을 페치하고 캐시하도록 할지 제어할 수 있습니다. 기본적으로 서비스 작업자는 일반적인 웹 파일 이름 확장명(예: `.html`, `.css`, `.js`, `.wasm`) 및 Blazor WebAssembly 고유의 파일 형식(`.dll`, `.pdb`)과 일치하는 파일을 페치 및 캐시합니다.

앱의 `wwwroot` 디렉터리에 없는 추가 리소스를 포함하려면 다음 예제와 같이 추가 MSBuild `ItemGroup` 항목을 정의합니다.

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

`AssetUrl` 메타데이터는 브라우저가 캐시할 리소스를 인출할 때 사용해야 할 기본적인 상대 URL을 지정합니다. 이는 디스크의 원래 원본 파일 이름과 무관할 수 있습니다.

> [!IMPORTANT]
> `ServiceWorkerAssetsManifestItem`을 추가한다고 해서 파일이 앱의 `wwwroot` 디렉터리에 게시되지는 않습니다. 게시 출력은 개별적으로 제어해야 합니다. `ServiceWorkerAssetsManifestItem`으로 인해서만 서비스 작업자 자산 매니페스트에 추가 항목이 표시됩니다.

## <a name="push-notifications"></a>푸시 알림

다른 PWA와 마찬가지로 Blazor WebAssembly PWA는 백 엔드 서버에서 푸시 알림을 받을 수 있습니다. 사용자가 앱을 활발히 사용하지 않는 경우에도 서버는 언제든지 푸시 알림을 보낼 수 있습니다. 예를 들어 다른 사용자가 관련 작업을 수행하는 경우 푸시 알림을 보낼 수 있습니다.

푸시 알림을 전송하는 메커니즘은 모든 기술을 사용할 수 있는 백 엔드 서버에서 구현되기 때문에 Blazor WebAssembly와는 완전히 독립적입니다. ASP.NET Core 서버에서 푸시 알림을 보내려면 [Blazing Pizza 워크숍에서 사용하는 접근 방식과 유사한 기법을 사용](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications)하는 것이 좋습니다.

클라이언트에서 푸시 알림을 수신하고 표시하는 메커니즘은 JavaScript 파일인 서비스 작업자에서 구현되기 때문에 이 경우 역시 Blazor WebAssembly와 무관합니다. 예제를 보려면 [Blazing Pizza 워크숍에서 사용하는 접근 방식](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications)을 참조하세요.

## <a name="caveats-for-offline-pwas"></a>오프라인 PWA에 대한 주의 사항

모든 앱이 오프라인 사용을 지원하려고 하지는 않습니다. 오프라인 지원은 상당한 복잡성을 추가하고 필요한 사용 사례와 항상 관련이 있는 것은 아닙니다.

오프라인 지원은 일반적으로 다음과 같은 경우에만 제공됩니다.

* 주 데이터 저장소가 브라우저에 있는 로컬 저장소인 경우. 예를 들어 `localStorage` 또는 [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API)에 데이터를 저장하는 [IoT](https://en.wikipedia.org/wiki/Internet_of_things) 디바이스의 UI를 사용하는 앱에서 이 접근 방식이 적절합니다.
* 앱에서 각 사용자와 관련된 백엔드 API 데이터를 페치 및 캐시하기 위해 많은 작업을 수행하는 경우에는 오프라인으로 데이터를 탐색할 수 있습니다. 앱에서 편집을 지원해야 하는 경우에는 변경 내용을 추적하고 데이터를 백엔드와 동기화하는 시스템도 빌드해야 합니다.
* 앱이 네트워크 상태와 관계없이 즉시 로드되도록 하는 것이 목표인 경우. 그런 경우에는 요청 진행 상황을 표시하고 네트워크를 사용할 수 없게 되면 정상적으로 작동하는 백엔드 API 요청에 적합한 사용자 환경을 구현해야 합니다.

또한 오프라인 지원 PWA는 다양한 복잡성을 다루어야 합니다. 개발자는 다음 단원의 주의 사항을 숙지해야 합니다.

### <a name="offline-support-only-when-published"></a>게시된 경우에만 오프라인 지원

개발 중에 백그라운드 업데이트 프로세스를 거치지 않고 브라우저에 즉시 반영되는 각 변경 내용을 확인하려고 합니다. 따라서 Blazor의 PWA 템플릿에서는 게시된 경우에만 오프라인 지원을 사용할 수 있습니다.

오프라인 지원 앱을 빌드하는 경우 개발 환경에서 앱을 테스트하는 것에 충분하지 않습니다. 게시된 상태에서 앱을 테스트하여 다른 네트워크 조건에 어떻게 반응하는지 이해해야 합니다.

### <a name="update-completion-after-user-navigation-away-from-app"></a>앱에서 사용자 탐색 후 업데이트 완료

사용자가 모든 탭에서 앱을 닫을 때까지는 업데이트가 완료되지 않습니다. [백그라운드 업데이트](#background-updates) 단원에 설명된 대로 업데이트를 앱에 배포한 후 브라우저에서 업데이트된 서비스 작업자 파일을 페치하여 업데이트 프로세스를 시작합니다.

많은 개발자가 놀라워하는 부분은 이 업데이트가 완료되는 경우에도 사용자가 모든 탭에서 나갈 때까지 업데이트가 적용되지 **않는다** 는 점입니다. 앱을 표시하는 유일한 탭인 경우에도 앱을 표시하는 탭을 새로 고치는 것으로 충분하지 **않습니다**. 앱이 완전히 닫힐 때까지 새 서비스 작업자는 ‘활성화 대기’ 상태로 유지됩니다. **이는 Blazor에 한정되지 않는 표준 웹 플랫폼 동작입니다.**

이는 서비스 작업자 또는 오프라인으로 캐시된 리소스에 대한 업데이트를 테스트하려는 개발자를 난감하게 만듭니다. 브라우저의 개발자 도구를 체크 인하면 다음과 같은 내용이 표시될 수 있습니다.

![Google Chrome ‘애플리케이션’ 탭에서는 앱의 서비스 작업자가 ‘활성화 대기 중’인지 보여 줍니다.](progressive-web-app/_static/image7.png)

앱을 표시하는 탭 또는 창에 해당하는 "클라이언트" 목록이 비어 있지 않으면 작업자는 계속 대기합니다. 서비스 작업자가 이를 수행하는 이유는 일관성을 보장하기 위한 것입니다. 일관성은 모든 리소스가 동일한 원자성 캐시에서 페치됨을 의미합니다.

변경 내용을 테스트할 때 앞의 스크린샷에 표시된 것처럼 “skipWaiting” 링크를 클릭하고 페이지를 다시 로드하는 것이 편리할 수 있습니다. [“대기 중” 단계를 건너뛰고 업데이트를 즉시 활성화](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase)하도록 서비스 작업자를 코딩하여 모든 사용자에 대해 이를 자동화할 수 있습니다. 대기 중 단계를 건너뛰면 리소스가 항상 동일한 캐시 인스턴스에서 일관되게 페치되는 것을 보장할 수 없게 됩니다.

### <a name="users-may-run-any-historical-version-of-the-app"></a>사용자는 앱의 모든 기록 버전을 실행할 수 있습니다.

웹 개발자는 사용자가 최신 버전의 웹앱만 실행할 것이라고 습관적으로 예상합니다. 그것이 기존 웹 배포 모델 내에서 일반적인 방식이기 때문입니다. 그러나 오프라인 우선 PWA는 네이티브 모바일 앱과 유사하여 사용자가 반드시 최신 버전을 실행하는 것은 아닙니다.

[백그라운드 업데이트](#background-updates)에 설명된 대로 앱에 업데이트를 배포한 후에는 **각 기존 사용자가 최소 1회 이상의 추가 방문을 위해 이전 버전을 계속 사용합니다.** (업데이트는 백그라운드에서 이루어지며 사용자가 앱을 종료하기 전까지는 업데이트가 활성화되지 않기 때문입니다.) 또한 사용되는 이전 버전은 이전에 배포한 것이 아닐 수도 있습니다. 이전 버전은 사용자가 마지막으로 업데이트를 완료한 시기에 따라 ‘임의의’ 과거 버전일 수 있습니다.

앱의 프런트엔드 및 백엔드 부분에 API 요청을 위한 스키마에 대한 동의가 필요한 경우에 이런 문제가 발생할 수 있습니다. 모든 사용자가 업그레이드되었는지 확인할 수 있기 전에는 호환되지 않는 API 스키마 변경을 배포하지 않아야 합니다. 또는 사용자가 호환되지 않는 이전 버전의 앱을 사용하지 못하도록 차단합니다. 이 시나리오 요구 사항은 기본 모바일 앱의 경우와 동일합니다. 서버 API에서 호환성이 손상되는 변경을 배포하는 경우 클라이언트 앱은 아직 업데이트되지 않은 사용자를 위해 중단됩니다.

가능하면 백엔드 API에 대한 주요 변경 내용을 배포하지 마세요. 그러나 배포해야 한다면 [ServiceWorkerRegistration과 같은 표준 서비스 작업자 API](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration)를 사용하여 앱이 최신 상태인지 여부를 확인하고 그렇지 않은 경우 사용을 금지하는 것이 좋습니다.

### <a name="interference-with-server-rendered-pages"></a>서버 렌더링 페이지 간섭

[서버 렌더링 페이지 지원](#support-server-rendered-pages) 단원에서 설명한 대로 모든 탐색 요청에 대한 서비스 작업자의 `/index.html` 콘텐츠 반환 동작을 무시하려면 서비스 작업자에서 논리를 편집합니다.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>모든 서비스 작업자 자산 매니페스트 콘텐츠는 기본적으로 캐시됩니다.

[자산 캐싱 제어](#control-asset-caching) 섹션에 설명된 대로 `service-worker-assets.js` 파일이 빌드 중에 생성되며 서비스 작업자가 페치 및 캐시해야 하는 모든 자산을 나열합니다.

이 목록에는 기본적으로 `wwwroot`(외부 패키지 및 프로젝트에서 제공하는 콘텐츠 포함)로 내보내지는 모든 항목이 포함되므로 너무 많은 콘텐츠를 포함하지 않도록 주의해야 합니다. `wwwroot` 디렉터리에 수백만 개의 이미지가 포함된 경우 서비스 작업자는 모든 이미지를 페치 및 캐시하기 위해 과도한 대역폭을 사용하므로 성공적으로 완료되지 않을 가능성이 큽니다.

`service-worker.published.js`의 `onInstall` 함수를 편집하여 페치 및 캐시할 매니페스트 콘텐츠의 하위 집합을 제어하기 위한 임의 논리를 구현합니다.

### <a name="interaction-with-authentication"></a>인증과의 상호 작용

PWA 템플릿은 인증과 함께 사용할 수 있습니다. 사용자가 초기 네트워크에 연결된 경우 오프라인 지원 PWA에서도 인증을 지원할 수 있습니다.

사용자가 네트워크에 연결되어 있지 않은 경우에는 액세스 토큰을 인증하거나 획득할 수 없습니다. 기본적으로 네트워크 액세스 없이 로그인 페이지를 방문하려고 하면 "네트워크 오류" 메시지가 표시됩니다. 사용자를 인증하거나 액세스 토큰을 획득하지 않고 사용자가 오프라인 상태에서 유용한 작업을 수행할 수 있도록 하는 UI 흐름을 디자인해야 합니다. 또는 네트워크를 사용할 수 없을 때 정상적으로 실패하도록 앱을 디자인할 수 있습니다. 이러한 시나리오를 처리하도록 앱을 디자인할 수 없는 경우 오프라인 지원을 사용하도록 설정하지 않는 것이 좋습니다.

온라인 및 오프라인에서 사용하도록 디자인된 앱이 다시 온라인 상태가 되는 경우 다음과 같습니다.

* 앱이 새 액세스 토큰을 프로비저닝해야 할 수도 있습니다.
* 앱은 오프라인 상태일 때 사용자 계정에 수행한 작업을 적용할 수 있도록 다른 사용자가 서비스에 로그인되어 있는지 검색해야 합니다.

인증과 상호 작용하는 오프라인 PWA 앱을 만들려면 다음을 수행합니다.

* 마지막으로 로그인한 사용자를 저장하고 앱이 오프라인일 때 저장된 사용자를 사용하는 팩터리로 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>를 바꿉니다.
* 앱이 오프라인 상태인 동안 작업을 큐에 추가한 후 앱이 온라인으로 전환되면 작업을 적용합니다.
* 로그아웃하는 동안 저장된 사용자를 지웁니다.

[`CarChecker`](https://github.com/SteveSandersonMS/CarChecker) 샘플 앱은 위의 접근 방식을 보여 줍니다. 앱의 다음 부분을 참조하세요.

* `OfflineAccountClaimsPrincipalFactory` (`Client/Data/OfflineAccountClaimsPrincipalFactory.cs`)
* `LocalVehiclesStore` (`Client/Data/LocalVehiclesStore.cs`)
* `LoginStatus` 구성 요소(`Client/Shared/LoginStatus.razor`)

## <a name="additional-resources"></a>추가 자료

* [SignalR 인증에 대한 원본 간 협상](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
