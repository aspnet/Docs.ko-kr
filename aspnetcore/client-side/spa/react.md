---
title: ASP.NET Core에 React 프로젝트 템플릿 사용
author: SteveSandersonMS
description: React 및 create-react-app에 대한 ASP.NET Core SPA(단일 페이지 애플리케이션) 프로젝트 템플릿을 시작하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
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
uid: spa/react
ms.openlocfilehash: 6c3539d96329489189f49a3af0c718791824be6b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054452"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>ASP.NET Core에 React 프로젝트 템플릿 사용

업데이트된 React 프로젝트 템플릿은 React 및 CRA([create-react-app](https://github.com/facebookincubator/create-react-app)) 규칙을 사용하여 풍부한 클라이언트 쪽 UI(사용자 인터페이스)를 구현하는 ASP.NET Core 앱에 대한 편리한 시작점을 제공합니다.

이 템플릿은 API 백 엔드로 사용되는 ASP.NET Core 프로젝트 및 UI로 사용되는 표준 CRA React 프로젝트를 둘 다 만드는 것과 동일하지만, 단일 단위로 빌드 및 게시할 수 있는 단일 앱 프로젝트에 두 프로젝트를 모두 편리하게 호스트할 수 있습니다.

React 프로젝트 템플릿은 SSR(서버 쪽 렌더링)에 사용되지 않습니다. React 및 Node.js를 사용하는 SSR의 경우 [Next.js](https://github.com/zeit/next.js/) 또는 [Razzle](https://github.com/jaredpalmer/razzle)을 고려합니다.

## <a name="create-a-new-app"></a>새 앱 만들기

ASP.NET Core 2.1이 설치되어 있는 경우 React 프로젝트 템플릿을 설치할 필요가 없습니다.

빈 디렉터리에 `dotnet new react` 명령을 사용하여 명령 프롬프트에서 새 프로젝트를 만듭니다. 예를 들어 다음 명령은 *my-new-app* 디렉터리에 앱을 만들고 해당 디렉터리로 전환합니다.

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

Visual Studio 또는 .NET Core CLI에서 앱을 실행합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

생성된 *.csproj* 파일을 열고 여기에서 정상적으로 앱을 실행합니다.

빌드 프로세스는 첫 번째 실행에서 npm 종속성을 복원하고 이 작업에는 몇 분 정도 소요될 수 있습니다. 후속 빌드는 훨씬 더 빠릅니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

`Development` 값을 가진 `ASPNETCORE_Environment` 환경 변수가 있는지 확인합니다. Windows(PowerShell 이외의 프롬프트)에서 `SET ASPNETCORE_Environment=Development`를 실행합니다. Linux 또는 macOS에서 `export ASPNETCORE_Environment=Development`를 실행합니다.

[dotnet build](/dotnet/core/tools/dotnet-build)를 실행하여 앱이 제대로 빌드되는지 확인합니다. 첫 번째 실행에서 빌드 프로세스는 npm 종속성을 복원하고 이 작업에는 몇 분 정도 소요될 수 있습니다. 후속 빌드는 훨씬 더 빠릅니다.

[dotnet run](/dotnet/core/tools/dotnet-run)을 실행하여 앱을 시작합니다.

---

프로젝트 템플릿은 ASP.NET Core 앱과 React 앱을 만듭니다. ASP.NET Core 앱은 데이터 액세스, 권한 부여 및 기타 서버 쪽 문제에 사용해야 합니다. *ClientApp* 하위 디렉터리에 있는 React 앱은 모든 UI 문제에 사용해야 합니다.

## <a name="add-pages-images-styles-modules-etc"></a>페이지, 이미지, 스타일, 모듈 등을 추가합니다.

*ClientApp* 디렉터리는 표준 CRA React 앱입니다. 자세한 내용은 공식 [CRA 설명서](https://create-react-app.dev/docs/getting-started/)를 참조하세요.

이 템플릿에서 만들어진 React 앱과 CRA 자체에서 만들어진 앱 간에는 약간 차이가 있지만 앱의 기능은 변경되지 않습니다. 템플릿에서 만들어진 앱에는 [부트스트랩](https://getbootstrap.com/) 기반 레이아웃 및 기본 라우팅 예제가 포함됩니다.

## <a name="install-npm-packages"></a>npm 패키지 설치

타사 npm 패키지를 설치하려면 *ClientApp* 하위 디렉터리에서 명령 프롬프트를 사용합니다. 예를 들어:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>게시 및 배포

개발 시에 앱은 개발자 편의를 위해 최적화된 모드로 실행됩니다. 예를 들어 디버그할 때 원래 소스 코드를 볼 수 있도록 JavaScript 번들에 소스 맵이 포함됩니다. 앱은 디스크에서 JavaScript, HTML 및 CSS 파일 변경 내용을 감시하고 해당 파일의 변경을 확인하면 자동으로 다시 컴파일하고 다시 로드합니다.

프로덕션 시에는 성능에 최적화된 앱의 버전을 제공합니다. 이는 자동으로 수행하도록 구성됩니다. 게시할 때 빌드 구성은 클라이언트 쪽 코드의 축소된 변환 컴파일된 빌드를 내보냅니다. 개발 빌드와 달리 프로덕션 빌드는 서버에 Node.js를 설치할 필요가 없습니다.

표준 [ASP.NET Core 호스팅 및 배포 방법](xref:host-and-deploy/index)을 사용할 수 있습니다.

## <a name="run-the-cra-server-independently"></a>개별적으로 CRA 서버 실행

프로젝트는 ASP.NET Core 앱이 개발 모드에서 시작될 때 백그라운드에서 CRA 개발 서버의 자체 인스턴스를 시작하도록 구성됩니다. 이 방법은 별도의 서버를 수동으로 실행할 필요가 없기 때문에 편리합니다.

이 기본 설정에는 단점이 있습니다. C# 코드를 수정하고 ASP.NET Core 앱을 다시 시작해야 할 때마다 CRA 서버가 다시 시작됩니다. 백업을 시작하려면 몇 초 정도가 필요합니다. C# 코드를 자주 편집하고 있고 CRA 서버가 다시 시작될 때까지 기다리지 않으려면 ASP.NET Core 프로세스와는 별도로 CRA 서버를 외부에서 실행합니다. 이를 수행하려면:

1. 다음 설정을 사용하여 *ClientApp* 하위 디렉터리에 *.env* 파일을 추가합니다.

    ```
    BROWSER=none
    ```

    이렇게 하면 외부에서 CRA 서버를 시작할 때 웹 브라우저가 열리지 않습니다.

2. 명령 프롬프트에서 *ClientApp* 하위 디렉터리로 전환하고 CRA 개발 서버를 시작합니다.

    ```console
    cd ClientApp
    npm start
    ```

3. 자체 인스턴스 중 하나를 시작하지 않고 외부 CRA 서버 인스턴스를 사용하도록 ASP.NET Core 앱을 수정합니다. *Startup* 클래스에서 `spa.UseReactDevelopmentServer` 호출을 다음으로 바꿉니다.

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

ASP.NET Core 앱을 시작할 때 CRA 서버는 시작되지 않습니다. 수동으로 시작한 인스턴스가 대신 사용됩니다. 이를 통해 더 빠르게 시작하고 다시 시작할 수 있습니다. 더 이상 React 앱이 매번 다시 빌드할 때까지 기다리지 않습니다.

> [!IMPORTANT]
> “서버 쪽 렌더링”은 이 템플릿에서 지원되는 기능이 아닙니다. 이 템플릿의 목표는 “create-react-app”을 사용하여 패리티를 충족하는 것입니다. 따라서 “create-react-app” 프로젝트에 포함되지 않은 시나리오와 기능(예: SSR)은 지원되지 않으며, 사용자를 위한 연습으로 유지되었습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:security/authentication/identity/spa>
