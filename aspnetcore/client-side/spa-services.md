---
title: JavaScript Services를 사용하여 ASP.NET Core에서 단일 페이지 애플리케이션 만들기
author: scottaddie
description: JavaScript Services를 사용하여 ASP.NET Core에서 지원하는 SPA(단일 페이지 애플리케이션)를 만드는 경우의 이점에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
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
uid: client-side/spa-services
ms.openlocfilehash: 1b9f5b4b4e066cdd3dd5fbfa666c7a087949979f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054647"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>JavaScript Services를 사용하여 ASP.NET Core에서 단일 페이지 애플리케이션 만들기

작성자: [Scott Addie](https://github.com/scottaddie) 및 [Fiyaz Hasan](https://fiyazhasan.me/)

SPA(단일 페이지 애플리케이션)는 기본적으로 풍부한 사용자 환경을 제공하므로 널리 사용되는 웹 애플리케이션 형식입니다. 클라이언트 쪽 SPA 프레임워크 또는 라이브러리(예: [Angular](https://angular.io/) 또는 [React](https://facebook.github.io/react/))를 ASP.NET Core와 같은 서버 쪽 프레임워크와 통합하는 것은 어려울 수 있습니다. JavaScript Services는 통합 프로세스의 마찰을 줄이기 위해 개발되었습니다. 이를 통해 다른 클라이언트 및 서버 기술 스택 간에 원활한 작업이 수행될 수 있습니다.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> 이 문서에서 설명하는 기능은 ASP.NET Core 3.0에서 더 이상 사용되지 않습니다. 더 간단한 SPA 프레임워크 통합 메커니즘을 [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet 패키지에서 사용할 수 있습니다. 자세한 내용은 [[알림] Microsoft.AspNetCore.SpaServices 및 Microsoft.AspNetCore.NodeServices가 더 이상 사용되지 않음](https://github.com/dotnet/AspNetCore/issues/12890)을 참조하세요.

::: moniker-end

## <a name="what-is-javascript-services"></a>JavaScript Services란?

JavaScript Services는 ASP.NET Core에 대한 클라이언트 쪽 기술 모음입니다. 해당 목표는 ASP.NET Core를 SPA를 빌드하기 위한 개발자의 기본 서버 쪽 플랫폼으로 만드는 것입니다.

JavaScript 서비스는 다음 두 개의 고유한 NuGet 패키지로 구성됩니다.

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/)(NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)(SpaServices)

이러한 패키지는 다음과 같은 시나리오에서 유용합니다.

* 서버에서 JavaScript 실행
* SPA 프레임워크 또는 라이브러리 사용
* Webpack을 사용하여 클라이언트 쪽 자산 작성

이 문서에 포함된 대부분의 핵심 내용은 SpaServices 패키지를 사용해서 진행됩니다.

## <a name="what-is-spaservices"></a>SpaServices란?

SpaServices는 ASP.NET Core를 SPA를 빌드하기 위한 개발자의 기본 서버 쪽 플랫폼으로 만들어졌습니다. SpaServices는 ASP.NET Core에서 SPA를 개발하는 데 필요하지 않으며, 특정 클라이언트 프레임워크로 개발자가 제한되지 않습니다.

SpaServices는 다음과 같은 유용한 인프라를 제공합니다.

* [서버 쪽 사전 렌더링](#server-side-prerendering)
* [Webpack 개발 미들웨어](#webpack-dev-middleware)
* [핫 모듈 교체](#hot-module-replacement)
* [라우팅 도우미](#routing-helpers)

전체적으로, 이러한 인프라 구성 요소는 개발 워크플로와 런타임 환경을 모두 향상시킵니다. 구성 요소는 개별적으로 채택할 수 있습니다.

## <a name="prerequisites-for-using-spaservices"></a>SpaServices 사용을 위한 필수 구성 요소

SpaServices를 사용하려면 다음을 설치합니다.

* [Node.js](https://nodejs.org/)(버전 6 이상), npm 포함

  * 이러한 구성 요소가 설치되어 있고 찾을 수 있는지 확인하려면 명령줄에서 다음을 실행합니다.

    ```console
    node -v && npm -v
    ```

  * Azure 웹 사이트에 배포하는 경우 아무런 작업도 필요하지 않습니다. 서버 환경에서 Node.js가 설치되며 사용 가능합니다.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * Visual Studio 2017을 사용하는 Windows에서는 **.NET Core 플랫폼 간 개발** 워크로드를 선택하여 SDK가 설치됩니다.

* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 패키지

## <a name="server-side-prerendering"></a>서버 쪽 사전 렌더링

유니버설(같은 구조) 애플리케이션은 서버와 클라이언트 둘 다에서 실행될 수 있는 JavaScript 애플리케이션입니다. Angular, React 및 기타 인기 있는 프레임워크는 이 애플리케이션 개발 스타일에 대한 범용 플랫폼을 제공합니다. 즉, 먼저 Node.js를 통해 서버에서 프레임워크 구성 요소를 렌더링한 다음, 클라이언트에 추가 실행을 위임하는 것이 좋습니다.

SpaServices에서 제공하는 ASP.NET Core [태그 도우미](xref:mvc/views/tag-helpers/intro)는 서버에서 JavaScript 함수를 호출하여 서버 쪽 렌더링의 구현을 단순화합니다.

### <a name="server-side-prerendering-prerequisites"></a>서버 쪽 사전 렌더링 필수 구성 요소

[aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm 패키지를 설치합니다.

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>서버 쪽 사전 렌더링 구성

태그 도우미는 프로젝트의 *_ViewImports.cshtml* 파일에서 네임스페이스 등록을 통해 검색할 수 있게 됩니다.

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

이 태그 도우미는 Razor 뷰 내에서 HTML 유사 구문을 활용하여 하위 수준 API와 직접 통신할 때의 복잡성을 추상화합니다.

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>asp-prerender-module 태그 도우미

위의 코드 예제에서 사용되는 `asp-prerender-module` 태그 도우미는 Node.js를 통해 서버에서 *ClientApp/dist/main-server.js* 를 실행합니다. 명확성을 위해 *main-server.js* 파일은 [Webpack](https://webpack.github.io/) 빌드 프로세스에서 TypeScript에서 JavaScript로의 소스 간 컴파일 태스크의 아티팩트입니다. Webpack은 `main-server`의 진입점 별칭을 정의합니다. 이 별칭에 대한 종속성 그래프 이동은 *ClientApp/boot-server.ts* 파일에서 시작됩니다.

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

다음 Angular 예제에서 *ClientApp/boot-server.ts* 파일은 `createServerRenderer` 함수 및 `aspnet-prerendering` npm 패키지의 `RenderResult` 형식을 활용하여 Node.js를 통해 서버 렌더링을 구성합니다. 서버 쪽 렌더링을 대상으로 하는 HTML 태그는 강력한 형식의 JavaScript `Promise` 개체로 래핑된 resolve 함수 호출에 전달됩니다. `Promise` 개체의 중요성은 DOM의 자리 표시자 요소에 주입하기 위해 페이지에 HTML 태그를 비동기적으로 제공한다는 것입니다.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>asp-prerender-data 태그 도우미

`asp-prerender-module` 태그 도우미와 함께 `asp-prerender-data` 태그 도우미를 사용하여 Razor 뷰에서 서버 쪽 JavaScript로 컨텍스트 정보를 전달할 수 있습니다. 예를 들어, 다음 태그는 사용자 데이터를 `main-server` 모듈에 전달합니다.

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

수신된 `UserName` 인수는 기본 제공 JSON serializer를 사용하여 serialize되고 `params.data` 개체에 저장됩니다. 다음 Angular 예제에서 데이터는 `h1` 요소 내에서 맞춤형 인사말을 생성하는 데 사용됩니다.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

태그 도우미에 전달된 속성 이름은 **PascalCase** 표기법으로 표시됩니다. 동일한 속성 이름이 **camelCase** 로 표시되는 JavaScript와는 다릅니다. 기본 JSON serialization 구성은 이러한 차이를 담당합니다.

위의 코드 예제를 확장하기 위해 `resolve` 함수에 제공 된 `globals` 속성을 하이드레이션하여 서버에서 뷰로 데이터를 전달할 수 있습니다.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

`globals` 개체 내에 정의된 `postList` 배열은 브라우저의 전역 `window` 개체에 연결됩니다. 이와 같이 변수를 전역 범위로 올리면 불필요한 작업이 줄어듭니다. 특히 서버에서 동일한 데이터를 한 번 로드하고 클라이언트에서 다시 로드하는 것과 관련이 있는 경우에 이러한 효과가 더욱 두드러집니다.

![window 개체에 연결된 전역 postList 변수](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Webpack 개발 미들웨어

[Webpack 개발 미들웨어](https://webpack.js.org/guides/development/#using-webpack-dev-middleware)에는 Webpack에서 필요 시 리소스를 작성하는 간소화된 개발 워크플로가 도입되었습니다. 미들웨어는 페이지가 브라우저에서 다시 로드될 때 클라이언트 쪽 리소스를 자동으로 컴파일하고 제공합니다. 또 다른 방법은 타사 종속성 또는 사용자 지정 코드가 변경될 때 프로젝트의 npm 빌드 스크립트를 통해 Webpack을 수동으로 호출하는 것입니다. *package.json* 파일의 npm 빌드 스크립트는 다음 예제에 나와 있습니다.

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Webpack 개발 미들웨어 필수 구성 요소

다음과 같이 [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm 패키지를 설치합니다.

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Webpack 개발 미들웨어 구성

Webpack 개발 미들웨어는 *Startup.cs* 파일의 `Configure` 메서드에서 다음 코드를 통해 HTTP 요청 파이프라인에 등록됩니다.

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

`UseStaticFiles` 확장 메서드를 통해 [정적 파일 호스팅을 등록](xref:fundamentals/static-files)하기 전에 `UseWebpackDevMiddleware` 확장 메서드를 호출해야 합니다. 보안상의 이유로 앱이 개발 모드에서 실행되는 경우에만 미들웨어를 등록합니다.

*webpack.config.js* 파일의 `output.publicPath` 속성은 미들웨어에 `dist` 폴더의 변경 내용을 감시하도록 지시합니다.

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>핫 모듈 교체

Webpack의 HMR([핫 모듈 교체](https://webpack.js.org/concepts/hot-module-replacement/)) 기능을 [Webpack 개발 미들웨어](#webpack-dev-middleware)가 진화된 결과로 간주할 수 있습니다. HMR은 모든 동일한 혜택을 제공하지만 변경 내용을 컴파일한 후 페이지 콘텐츠를 자동으로 업데이트하여 개발 워크플로를 더욱 간소화합니다. 이것을 SPA의 현재 메모리 내 상태와 디버깅 세션을 방해하는 브라우저의 새로 고침과 혼동하지 마세요. Webpack 개발 미들웨어 서비스와 브라우저 간에 라이브 링크가 있습니다. 따라서 변경 내용이 브라우저에 푸시됩니다.

### <a name="hot-module-replacement-prerequisites"></a>핫 모듈 교체 필수 조건

[webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm 패키지를 설치합니다.

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>핫 모듈 교체 구성

HMR 구성 요소는 `Configure` 메서드에서 MVC의 HTTP 요청 파이프라인에 등록되어야 합니다.

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

[Webpack 개발 미들웨어](#webpack-dev-middleware)와 마찬가지로 `UseWebpackDevMiddleware` 확장 메서드를 `UseStaticFiles` 확장 메서드보다 먼저 호출해야 합니다. 보안상의 이유로 앱이 개발 모드에서 실행되는 경우에만 미들웨어를 등록합니다.

*webpack.config.js* 파일은 비어 있는 경우에도 `plugins` 배열을 정의해야 합니다.

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

브라우저에서 앱을 로드한 후 개발자 도구의 콘솔 탭에서 HMR 활성화 확인을 제공합니다.

![핫 모듈 교체 연결된 메시지](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>라우팅 도우미

대부분의 ASP.NET Core 기반 SPA에서 서버 쪽 라우팅 외에 클라이언트 쪽 라우팅이 필요한 경우도 많습니다. SPA 및 MVC 라우팅 시스템은 간섭 없이 독립적으로 작동할 수 있습니다. 그러나 문제를 유발하는 한 가지 극단적인 사례가 있습니다. 404 HTTP 응답이 발생하는 경우입니다.

`/some/page`의 확장 없는 경로가 사용되는 시나리오를 고려해 보세요. 요청이 서버 쪽 경로와 패턴이 일치하지 않지만 해당 패턴은 클라이언트 쪽 경로와 일치하는 경우를 가정해 보세요. 이제, 서버에서 이미지 파일을 찾게 되는 `/images/user-512.png`에 대한 들어오는 요청을 고려합니다. 요청된 리소스 경로가 서버 쪽 경로 또는 정적 파일과 일치하지 않을 경우 클라이언트 쪽 애플리케이션에서 이를 처리할 가능성이 거의 없습니다. 따라서 일반적으로 404 HTTP 상태 코드를 반환하는 것이 바람직합니다.

### <a name="routing-helpers-prerequisites"></a>라우팅 도우미 필수 조건

클라이언트 쪽 라우팅 npm 패키지를 설치합니다. Angular를 예로 사용하는 경우:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>라우팅 도우미 구성

`MapSpaFallbackRoute`라는 확장 메서드는 `Configure` 메서드에서 사용됩니다.

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

경로는 구성된 순서대로 평가됩니다. 따라서 앞의 코드 예제의 `default` 경로는 처음에는 패턴 일치에 사용됩니다.

## <a name="create-a-new-project"></a>새 프로젝트 만들기

JavaScript Services는 미리 구성된 애플리케이션 템플릿을 제공합니다. SpaServices는 이러한 템플릿에서 다른 프레임워크 및 라이브러리(예: Angular, React 및 Redux)와 함께 사용됩니다.

다음 명령을 실행하여 .NET Core CLI를 통해 이러한 템플릿을 설치할 수 있습니다.

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

사용 가능한 SPA 템플릿 목록이 표시됩니다.

| 템플릿                                 | 짧은 이름 | 언어 | Tags        |
| ------------------------------------------| :--------: | :------: | :---------: |
| MVC ASP.NET Core(Angular 사용)             | angular    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core(React.js 사용)            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core(React.js 및 Redux 사용)  | reactredux | [C#]     | Web/MVC/SPA |

SPA 템플릿 중 하나를 사용하여 새 프로젝트를 만들려면 [dotnet new](/dotnet/core/tools/dotnet-new) 명령에 템플릿의 **짧은 이름** 을 포함합니다. 다음 명령은 서버 쪽에 대해 구성된 ASP.NET Core MVC를 사용하여 Angular 애플리케이션을 만듭니다.

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>런타임 구성 모드 설정

다음과 같은 두 가지 기본 런타임 구성 모드가 있습니다.

* **개발**:
  * 디버깅을 용이하게 하는 소스 맵을 포함합니다.
  * 성능을 위해 클라이언트 쪽 코드를 최적화하지 않습니다.
* **프로덕션**:
  * 소스 맵을 제외합니다.
  * 묶음 및 축소를 통해 클라이언트 쪽 코드를 최적화합니다.

ASP.NET Core는 `ASPNETCORE_ENVIRONMENT`라는 환경 변수를 사용하여 구성 모드를 저장합니다. 자세한 내용은 [환경 설정](xref:fundamentals/environments#set-the-environment)을 참조하세요.

### <a name="run-with-net-core-cli"></a>.NET Core CLI를 사용하여 실행

프로젝트 루트에서 다음 명령을 실행하여 필요한 NuGet 및 npm 패키지를 복원합니다.

```dotnetcli
dotnet restore && npm i
```

다음과 같이 애플리케이션을 빌드하고 실행합니다.

```dotnetcli
dotnet run
```

애플리케이션은 [런타임 구성 모드](#set-the-runtime-configuration-mode)에 따라 localhost에서 시작됩니다. 브라우저에서 `http://localhost:5000`으로 이동하면 방문 페이지가 표시됩니다.

### <a name="run-with-visual-studio-2017"></a>Visual Studio 2017로 열기

[dotnet new](/dotnet/core/tools/dotnet-new) 명령으로 생성된 *.csproj* 파일을 엽니다. 필요한 NuGet 및 npm 패키지는 프로젝트를 열 때 자동으로 복원됩니다. 이 복원 프로세스는 몇 분 정도 걸릴 수 있으며 완료되면 애플리케이션을 실행할 준비가 된 것입니다. 녹색 실행 단추를 클릭하거나 `Ctrl + F5`를 누르면 브라우저가 열리고 애플리케이션의 방문 페이지가 열립니다. 애플리케이션은 [런타임 구성 모드](#set-the-runtime-configuration-mode)에 따라 localhost에서 실행됩니다.

## <a name="test-the-app"></a>앱을 테스트합니다.

SpaServices 템플릿은 [Karma](https://karma-runner.github.io/1.0/index.html) 및 [Jasmine](https://jasmine.github.io/)을 사용하여 클라이언트 쪽 테스트를 실행하도록 미리 구성되어 있습니다. Jasmine는 인기 있는 JavaScript용 단위 테스트 프레임워크이지만 Karma는 해당 테스트용 Test Runner입니다. Karma는 [Webpack 개발 미들웨어](#webpack-dev-middleware)와 함께 작동하도록 구성되므로 개발자는 변경이 수행될 때마다 테스트를 중지했다가 다시 실행할 필요가 없습니다. 코드가 테스트 사례 또는 테스트 사례 자체에 대해 실행되는지에 관계없이 테스트는 자동으로 실행됩니다.

한 예로 Angular 애플리케이션을 사용할 경우 *counter.component.spec.ts* 파일의 `CounterComponent`에 대해 두 개의 테스트 사례가 이미 제공되어 있습니다.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

*ClientApp* 디렉터리에서 명령 프롬프트를 엽니다. 다음 명령을 실행합니다.

```console
npm test
```

이 스크립트는 *karma.conf.js* 파일에 정의된 설정을 읽는 Karma Test Runner를 시작합니다. 다른 설정 중에 *karma.conf.js* 는 `files` 배열을 통해 실행할 테스트 파일을 식별합니다.

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>앱 게시

Azure에 게시하는 방법에 대한 자세한 내용은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/12474)를 참조하세요.

생성된 클라이언트 쪽 자산과 게시된 ASP.NET Core 아티팩트를 배포용 패키지에 결합하는 작업은 복잡할 수 있습니다. 다행히 SpaServices는 `RunWebpack`이라는 사용자 지정 MSBuild 대상을 사용하여 전체 게시 프로세스를 오케스트레이션합니다.

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

MSBuild 대상은 다음과 같은 기능을 담당합니다.

1. npm 패키지를 복원합니다.
1. 타사 클라이언트 쪽 자산의 프로덕션 등급 빌드를 만듭니다.
1. 사용자 지정 클라이언트 쪽 자산의 프로덕션 등급 빌드를 만듭니다.
1. Webpack에서 생성된 자산을 게시 폴더에 복사합니다.

MSBuild 대상은 다음을 실행할 때 호출됩니다.

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>추가 자료

* [Angular 설명서](https://angular.io/docs)
