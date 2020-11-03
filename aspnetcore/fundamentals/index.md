---
title: ASP.NET Core 기본 사항
author: rick-anderson
description: ASP.NET Core 앱을 구축하기 위한 기본적인 개념을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
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
uid: fundamentals/index
ms.openlocfilehash: 25348f8486ec6ccb53ebf527ad4519638dd5f73e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059379"
---
# <a name="aspnet-core-fundamentals"></a>ASP.NET Core 기본 사항

::: moniker range=">= aspnetcore-3.0"

이 문서에서는 ASP.NET Core 앱을 개발하는 방법을 이해하는 데 도움이 되는 주요 토픽의 개요를 제공합니다.

## <a name="the-startup-class"></a>Startup 클래스

`Startup` 클래스에서는 다음을 수행합니다.

* 앱에서 요구하는 서비스가 구성됩니다.
* 앱의 요청 처리 파이프라인이 일련의 미들웨어 구성 요소로 정의됩니다.

샘플 `Startup` 클래스는 다음과 같습니다.

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

자세한 내용은 <xref:fundamentals/startup>를 참조하세요.

## <a name="dependency-injection-services"></a>종속성 주입(서비스)

ASP.NET Core는 구성된 서비스를 앱을 통해 사용할 수 있도록 만드는 기본 제공 DI(종속성 주입) 프레임워크를 포함합니다. 예를 들어 로깅 구성 요소는 서비스입니다.

서비스를 구성(또는 *등록* )하는 코드는 `Startup.ConfigureServices` 메서드에 추가됩니다. 예를 들어:

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

서비스는 일반적으로 DI로부터 생성자 주입을 사용하여 확인됩니다. 생성자 주입에서는 클래스가 필요한 형식 또는 인터페이스의 생성자 매개 변수를 선언합니다. DI 프레임워크가 런타임에 이 서비스의 인스턴스를 제공합니다.

다음 예제에서는 생성자 주입을 사용하여 DI로부터 `RazorPagesMovieContext`를 확인합니다.

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

기본 제공 IoC(Inversion of Control) 컨테이너가 앱의 모든 요구 사항을 충족하지 않을 경우 대신 타사 IoC 컨테이너를 사용할 수 있습니다.

자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.

## <a name="middleware"></a>미들웨어

요청 처리 파이프라인은 일련의 미들웨어 구성 요소로 구성됩니다. 각 구성 요소는 `HttpContext`에 대해 작업을 수행한 후 파이프라인에 있는 다음 미들웨어를 호출하거나 요청을 종료합니다.

규칙에 따라 미들웨어 구성 요소는 `Startup.Configure` 메서드에서 `Use...` 확장 메서드를 호출하여 파이프라인에 추가됩니다. 예를 들어 정적 파일을 렌더링하도록 설정하려면 `UseStaticFiles`를 호출합니다.

다음 예제에서는 요청 처리 파이프라인을 구성합니다.

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

ASP.NET Core는 풍부한 일련의 기본 제공 미들웨어를 포함합니다. 사용자 지정 미들웨어 구성 요소를 작성할 수도 있습니다.

자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.

## <a name="host"></a>호스트

ASP.NET Core 앱은 시작 시 ‘호스트’를 빌드합니다. 호스트는 다음과 같은 앱의 리소스를 모두 캡슐화합니다.

* HTTP 서버 구현
* 미들웨어 구성 요소
* 로깅
* DI(종속성 주입) 서비스
* Configuration

호스트에는 다음 두 가지가 있습니다. 

* .NET 일반 호스트
* ASP.NET Core 웹 호스트

.NET 일반 호스트를 사용하는 것이 좋습니다. ASP.NET Core 웹 호스트는 이전 버전과의 호환성을 위해서만 제공됩니다.

다음 예제에서는 .NET 일반 호스트를 만듭니다.

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

`CreateDefaultBuilder` 및 `ConfigureWebHostDefaults` 메서드는 다음과 같은 기본 옵션으로 호스트를 구성합니다.

* [Kestrel](#servers)을 웹 서버로 사용하고 IIS 통합을 설정합니다.
* *appsettings.json* , *appsettings.{Environment Name}.json* , 환경 변수, 명령줄 인수 및 기타 구성 소스의 구성을 로드합니다.
* 콘솔 및 디버그 공급자에게 로깅 출력을 보냅니다.

자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.

### <a name="non-web-scenarios"></a>비 웹 시나리오

제네릭 호스트를 통해 다른 형식의 앱에서 로깅, DI(종속성 주입), 구성 및 앱 수명 관리와 같은 교차 프레임워크 확장을 사용할 수 있습니다. 자세한 내용은 <xref:fundamentals/host/generic-host> 및 <xref:fundamentals/host/hosted-services>를 참조하세요.

## <a name="servers"></a>서버

ASP.NET Core 앱은 HTTP 요청을 수신하기 위해 HTTP 서버 구현을 사용합니다. 해당 서버는 `HttpContext`에 구성된 일련의 [요청 기능](xref:fundamentals/request-features)으로 앱에 대한 요청을 표시합니다.

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core는 다음과 같은 서버 구현을 제공합니다.

* *Kestrel* 은 플랫폼 간 웹 서버입니다. Kestrel은 보통 [IIS](https://www.iis.net/)를 사용하여 역방향 프록시 구성에서 실행됩니다. Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.
* ‘IIS HTTP 서버’는 IIS를 사용하는 Windows용 서버입니다. 이 서버를 사용하면 ASP.NET Core 앱 및 IIS는 동일한 프로세스에서 실행됩니다.
* *HTTP.sys* 는 IIS에서 사용되지 않는 Windows용 서버입니다.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다. Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수 있습니다. Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다. Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수 있습니다. Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.

---

자세한 내용은 <xref:fundamentals/servers/index>를 참조하세요.

## <a name="configuration"></a>Configuration

ASP.NET Core는 정렬된 일련의 구성 공급 기업에서 이름-값 쌍으로 설정을 가져오는 구성 프레임워크를 제공합니다. *.json* 파일, *.xml* 파일, 환경 변수 및 명령줄 인수와 같은 다양한 소스에서 기본 제공 구성 공급자를 사용할 수 있습니다. 다른 소스를 지원하려면 사용자 지정 구성 공급자를 작성하세요.

[기본적으로](xref:fundamentals/configuration/index#default) ASP.NET Core 앱은 *appsettings.json* , 환경 변수, 명령줄 등에서 읽도록 구성되어 있습니다. 앱의 구성이 로드되면 환경 변수의 값이 *appsettings.json* 의 값을 재정의합니다.

관련 구성 값을 읽는 기본 방법은 [옵션 패턴](xref:fundamentals/configuration/options)를 사용하는 것입니다. 자세한 내용은 [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat)(옵션 패턴을 사용하여 계층적 구성 데이터 바인딩)을 참조하세요.

ASP.NET Core는 암호와 같은 기밀 구성 데이터의 관리를 위해 [비밀 관리자](xref:security/app-secrets#secret-manager)를 제공합니다. 프로덕션 비밀의 경우 [Azure Key Vault](xref:security/key-vault-configuration)를 사용하는 것이 좋습니다.

자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.

## <a name="environments"></a>환경

`Development`, `Staging` 및 `Production`과 같은 실행 환경은 ASP.NET Core의 일급 개념입니다. `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하여 앱을 실행 중인 환경을 지정합니다. ASP.NET Core는 앱 시작 시 해당 환경 변수를 읽고 `IWebHostEnvironment` 구현에서 값을 저장합니다. 이 구현은 DI(종속성 주입)를 통해 앱의 어디서나 사용할 수 있습니다.

다음 예제에서는 앱이 `Development` 환경에서 실행될 때 상세한 오류 정보를 제공하도록 구성합니다.

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

자세한 내용은 <xref:fundamentals/environments>를 참조하세요.

## <a name="logging"></a>로깅

ASP.NET Core는 다양한 기본 제공 및 타사 로깅 공급자와 함께 작동하는 로깅 API를 지원합니다. 사용 가능한 공급자는 다음과 같습니다.

* Console
* 디버그
* Windows 이벤트 추적
* Windows 이벤트 로그
* TraceSource
* Azure App Service
* Azure Application Insights

로그를 만들려면 DI(종속성 주입)로부터 <xref:Microsoft.Extensions.Logging.ILogger%601> 서비스를 확인하고 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>과 같은 로깅 메서드를 호출합니다. 예를 들어:

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

`LogInformation`과 같은 로깅 메서드는 임의 개수의 필드를 지원합니다. 필드는 일반적으로 메시지 `string`을 생성하는 데 사용되지만 일부 로깅 공급자는 이들 필드를 별도의 필드로 데이터 저장소로 보냅니다. 이 기능을 사용하면 로그 공급 기업이 [구조적 로깅이라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 구현할 수 있게 됩니다.

자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.

## <a name="routing"></a>라우팅

*경로* 는 처리기에 매핑되는 URL 패턴입니다. 처리기는 일반적으로 Razor Page, MVC 컨트롤러의 작업 메서드 또는 미들웨어와 같습니다. ASP.NET Core 라우팅을 사용하면 앱에서 사용되는 URL을 제어할 수 있습니다.

자세한 내용은 <xref:fundamentals/routing>를 참조하세요.

## <a name="error-handling"></a>오류 처리

ASP.NET Core에는 다음과 같은 오류를 처리하기 위한 기본 제공 기능이 포함됩니다.

* 개발자 예외 페이지
* 사용자 지정 오류 페이지
* 정적 상태 코드 페이지
* 시작 예외 처리

자세한 내용은 <xref:fundamentals/error-handling>를 참조하세요.

## <a name="make-http-requests"></a>HTTP 요청 만들기

`HttpClient` 인스턴스를 만들기 위해 `IHttpClientFactory`를 구현할 수 있습니다. 팩터리는 다음과 같습니다.

* 논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다. 예를 들어, GitHub에 액세스하는 *github* 클라이언트를 등록 및 구성합니다. 다른 용도를 위한 기본 클라이언트를 등록 및 구성합니다.
* 나가는 요청 미들웨어 파이프라인을 빌드하기 위해 여러 위임 처리기를 연결하고 등록하도록 지원합니다. 이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다. 이 패턴은 캐싱, 오류 처리, serialization 및 로깅을 포함한 HTTP 요청 관련 횡단 문제를 관리하기 위한 메커니즘을 제공합니다.
* 일시적인 오류를 처리하기 위해 널리 사용되는 타사 라이브러리인 *Polly* 와 통합합니다.
* `HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientHandler` 인스턴스의 풀링 및 수명을 관리합니다.
* 팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 <xref:Microsoft.Extensions.Logging.ILogger>를 통해 구성 가능한 로깅 환경을 추가합니다.

자세한 내용은 <xref:fundamentals/http-requests>를 참조하세요.

## <a name="content-root"></a>콘텐츠 루트

콘텐츠 루트는 다음의 기본 경로입니다.

* 앱을 호스트하는 실행 파일( *.exe* )
* 앱을 구성하는 컴파일된 어셈블리( *.dll* )
* 앱에서 사용하는 다음과 같은 콘텐츠 파일:
  * Razor 파일( *.cshtml* , *.razor* )
  * 구성 파일( *.json* , *.xml* )
  * 데이터 파일( *.db* )
* [웹 루트](#web-root)(일반적으로 *wwwroot* 폴더)

개발 중에는 콘텐츠 루트가 기본적으로 프로젝트의 루트 디렉터리로 설정됩니다. 이 디렉터리는 앱의 콘텐츠 파일과 [웹 루트](#web-root)의 기본 경로이기도 합니다. [호스트를 빌드](#host)할 때는 경로를 설정하여 다른 콘텐츠 루트를 지정하세요. 자세한 내용은 [콘텐츠 루트](xref:fundamentals/host/generic-host#contentroot)를 참조하세요.

## <a name="web-root"></a>웹 루트

웹 루트는 다음과 같은 퍼블릭 정적 리소스 파일의 기본 경로입니다.

* 스타일시트( *.css* )
* JavaScript( *.js* )
* 이미지( *.png* , *.jpg* )

정적 파일은 기본적으로 웹 루트 디렉터리와 그 하위 디렉터리에서만 제공됩니다. 웹 루트 경로는 기본적으로 *{content root}/wwwroot* 로 설정됩니다. [호스트를 빌드](#host)할 때는 경로를 설정하여 다른 웹 루트를 지정하세요. 자세한 내용은 [웹 루트](xref:fundamentals/host/generic-host#webroot)를 참조하세요.

프로젝트 파일에서 [\<Content> 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items#content)을 사용하여 *wwwroot* 에 파일을 게시하지 못하도록 합니다. 다음 예제에서는 *wwwroot/local* 과 그 하위 디렉터리에서 콘텐츠가 게시되지 못하도록 합니다.

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Razor *.cshtml* 파일에서는 물결표 슬래시(`~/`)가 웹 루트를 가리킵니다. `~/`(으)로 시작하는 경로를 *가상 경로* 라고 합니다.

자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 문서는 ASP.NET Core 앱을 개발하는 방법을 이해하기 위한 주요 항목의 개요입니다.

## <a name="the-startup-class"></a>Startup 클래스

`Startup` 클래스에서는 다음을 수행합니다.

* 앱에서 요구하는 서비스가 구성됩니다.
* 요청 처리 파이프라인이 정의됩니다.

*서비스* 는 앱에서 사용되는 구성 요소입니다. 예를 들어 로깅 구성 요소는 서비스입니다. 서비스를 구성(또는 *등록* )하는 코드는 `Startup.ConfigureServices` 메서드에 추가됩니다.

요청 처리 파이프라인은 일련의 *미들웨어* 구성 요소로 구성됩니다. 예를 들어 미들웨어는 정적 파일에 대한 요청을 처리하거나 HTTP 요청을 HTTPS로 리디렉션할 수 있습니다. 각 미들웨어는 `HttpContext` 상에서 비동기 작업을 수행한 다음, 파이프라인의 다음 미들웨어를 호출하거나 요청을 종료합니다. 요청 처리 파이프라인을 구성하는 코드는 `Startup.Configure` 메서드에 추가됩니다.

샘플 `Startup` 클래스는 다음과 같습니다.

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

자세한 내용은 <xref:fundamentals/startup>를 참조하세요.

## <a name="dependency-injection-services"></a>종속성 주입(서비스)

ASP.NET Core에는 구성된 서비스를 앱의 클래스에 사용할 수 있도록 만드는 기본 제공 DI(종속성 주입) 프레임워크가 포함됩니다. 클래스에서 서비스의 인스턴스를 가져오는 한 가지 방법은 필수 형식의 매개 변수를 사용하여 생성자를 만드는 것입니다. 해당 매개 변수는 서비스 형식 또는 인터페이스일 수 있습니다. DI 시스템에서는 런타임 시 서비스를 제공합니다.

Entity Framework Core 컨텍스트 개체를 가져오는 데 DI를 사용하는 클래스는 다음과 같습니다. 강조 표시된 줄은 생성자 주입의 예제입니다.

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

DI가 기본 제공되기는 하지만 원하는 경우 타사 IoC(Inversion of Control) 컨테이너를 플러그 인할 수 있도록 설계되었습니다.

자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.

## <a name="middleware"></a>미들웨어

요청 처리 파이프라인은 일련의 미들웨어 구성 요소로 구성됩니다. 각 구성 요소는 `HttpContext` 상에서 비동기 작업을 수행한 다음, 파이프라인의 다음 미들웨어를 호출하거나 요청을 종료합니다.

규칙에 따라 미들웨어 구성 요소는 `Startup.Configure` 메서드에서 `Use...` 확장 메서드를 호출하여 파이프라인에 추가됩니다. 예를 들어 정적 파일을 렌더링하도록 설정하려면 `UseStaticFiles`를 호출합니다.

다음 예제에서 강조 표시된 코드는 요청 처리 파이프라인을 구성합니다.

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

ASP.NET Core는 풍부한 일련의 기본 제공 미들웨어를 포함하고 있으며, 사용자 지정 미들웨어를 작성할 수 있습니다.

자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.

## <a name="host"></a>호스트

ASP.NET Core 앱은 시작 시 *호스트* 를 빌드합니다. 호스트는 다음과 같은 앱의 리소스를 모두 캡슐화하는 개체입니다.

* HTTP 서버 구현
* 미들웨어 구성 요소
* 로깅
* DI
* Configuration

하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.

두 호스트, 웹 호스트와 제네릭 호스트를 사용할 수 있습니다. ASP.NET Core 2.x에서 제네릭 호스트는 비 웹 시나리오에만 해당됩니다.

호스트를 만드는 코드는 `Program.Main`에 있습니다.

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

`CreateDefaultBuilder` 메서드는 다음과 같이 일반적으로 사용되는 옵션으로 호스트를 구성합니다.

* [Kestrel](#servers)을 웹 서버로 사용하고 IIS 통합을 설정합니다.
* *appsettings.json* , *appsettings.{Environment Name}.json* , 환경 변수, 명령줄 인수 및 기타 구성 소스의 구성을 로드합니다.
* 콘솔 및 디버그 공급자에게 로깅 출력을 보냅니다.

자세한 내용은 <xref:fundamentals/host/web-host>를 참조하세요.

### <a name="non-web-scenarios"></a>비 웹 시나리오

제네릭 호스트를 통해 다른 형식의 앱에서 로깅, DI(종속성 주입), 구성 및 앱 수명 관리와 같은 교차 프레임워크 확장을 사용할 수 있습니다. 자세한 내용은 <xref:fundamentals/host/generic-host> 및 <xref:fundamentals/host/hosted-services>를 참조하세요.

## <a name="servers"></a>서버

ASP.NET Core 앱은 HTTP 요청을 수신하기 위해 HTTP 서버 구현을 사용합니다. 해당 서버는 `HttpContext`에 구성된 일련의 [요청 기능](xref:fundamentals/request-features)으로 앱에 대한 요청을 표시합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core는 다음과 같은 서버 구현을 제공합니다.

* *Kestrel* 은 플랫폼 간 웹 서버입니다. Kestrel은 보통 [IIS](https://www.iis.net/)를 사용하여 역방향 프록시 구성에서 실행됩니다. Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.
* ‘IIS HTTP 서버’는 IIS를 사용하는 Windows용 서버입니다. 이 서버를 사용하면 ASP.NET Core 앱 및 IIS는 동일한 프로세스에서 실행됩니다.
* *HTTP.sys* 는 IIS에서 사용되지 않는 Windows용 서버입니다.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다. Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다. Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다. Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다. Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core는 다음과 같은 서버 구현을 제공합니다.

* *Kestrel* 은 플랫폼 간 웹 서버입니다. Kestrel은 보통 [IIS](https://www.iis.net/)를 사용하여 역방향 프록시 구성에서 실행됩니다. Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.
* *HTTP.sys* 는 IIS에서 사용되지 않는 Windows용 서버입니다.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다. Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다. Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다. Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다. Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

자세한 내용은 <xref:fundamentals/servers/index>를 참조하세요.

## <a name="configuration"></a>Configuration

ASP.NET Core는 정렬된 일련의 구성 공급 기업에서 이름-값 쌍으로 설정을 가져오는 구성 프레임워크를 제공합니다. *.json* 파일, *.xml* 파일, 환경 변수 및 명령줄 인수와 같은 다양한 원본의 기본 제공 구성 공급 기업이 있습니다. 또는 사용자 지정 구성 공급 기업을 작성할 수도 있습니다.

예를 들어 구성이 *appsettings.json* 및 환경 변수에서 제공되도록 지정할 수 있습니다. 그런 다음, *ConnectionString* 값이 요청되면 프레임워크는 먼저 *appsettings.json* 파일을 찾습니다. 값을 찾았지만 환경 변수에서도 값을 찾을 수 있다면 환경 변수의 값이 우선 적용됩니다.

ASP.NET Core는 암호와 같은 기밀 구성 데이터를 관리하기 위해 [비밀 관리자 도구](xref:security/app-secrets)를 제공합니다. 프로덕션 비밀의 경우 [Azure Key Vault](xref:security/key-vault-configuration)를 사용하는 것이 좋습니다.

자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.

## <a name="options"></a>옵션

가능할 경우 ASP.NET Core는 구성 값을 저장하고 검색하기 위해 *옵션 패턴* 을 따릅니다. 옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.

예를 들어 다음 코드에서는 WebSockets 옵션을 설정합니다.

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.

## <a name="environments"></a>환경

*개발* , *준비* 및 *프로덕션* 과 같은 실행 환경은 ASP.NET Core의 일급 개념입니다. `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하여 앱을 실행 중인 환경을 지정할 수 있습니다. ASP.NET Core는 앱 시작 시 해당 환경 변수를 읽고 `IHostingEnvironment` 구현에서 값을 저장합니다. 환경 개체는 DI를 통해 앱에서 사용할 수 있습니다.

`Startup` 클래스의 다음 샘플 코드는 개발 중에 실행할 때만 자세한 오류 정보를 제공하도록 앱을 구성합니다.

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

자세한 내용은 <xref:fundamentals/environments>를 참조하세요.

## <a name="logging"></a>로깅

ASP.NET Core는 다양한 기본 제공 및 타사 로깅 공급자와 함께 작동하는 로깅 API를 지원합니다. 지원되는 공급 기업에는 다음이 포함됩니다.

* Console
* 디버그
* Windows 이벤트 추적
* Windows 이벤트 로그
* TraceSource
* Azure App Service
* Azure Application Insights

DI에서 `ILogger` 개체를 가져오고 로그 메서드를 호출하여 앱 코드 어디서나 로그를 작성합니다.

생성자 주입 및 로깅 메서드 호출을 강조 표시하고 `ILogger` 개체를 사용하는 샘플 코드는 다음과 같습니다.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

`ILogger` 인터페이스를 통해 필드 개수에 관계 없이 로그 공급 기업에 전달할 수 있습니다. 필드는 일반적으로 메시지 문자열을 생성하는 데 사용되지만 공급 기업은 이를 별도 필드로 데이터 저장소에 보낼 수도 있습니다. 이 기능을 사용하면 로그 공급 기업이 [구조적 로깅이라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 구현할 수 있게 됩니다.

자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.

## <a name="routing"></a>라우팅

*경로* 는 처리기에 매핑되는 URL 패턴입니다. 처리기는 일반적으로 Razor Page, MVC 컨트롤러의 작업 메서드 또는 미들웨어와 같습니다. ASP.NET Core 라우팅을 사용하면 앱에서 사용되는 URL을 제어할 수 있습니다.

자세한 내용은 <xref:fundamentals/routing>를 참조하세요.

## <a name="error-handling"></a>오류 처리

ASP.NET Core에는 다음과 같은 오류를 처리하기 위한 기본 제공 기능이 포함됩니다.

* 개발자 예외 페이지
* 사용자 지정 오류 페이지
* 정적 상태 코드 페이지
* 시작 예외 처리

자세한 내용은 <xref:fundamentals/error-handling>를 참조하세요.

## <a name="make-http-requests"></a>HTTP 요청 만들기

`HttpClient` 인스턴스를 만들기 위해 `IHttpClientFactory`를 구현할 수 있습니다. 팩터리는 다음과 같습니다.

* 논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다. 예를 들어, *github* 클라이언트는 GitHub에 액세스하도록 등록 및 구성할 수 있습니다. 기본 클라이언트는 다른 용도로 등록할 수 있습니다.
* 나가는 요청 미들웨어 파이프라인을 빌드하기 위해 여러 위임 처리기를 연결하고 등록하도록 지원합니다. 이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다. 이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.
* 일시적인 오류를 처리하기 위해 널리 사용되는 타사 라이브러리인 *Polly* 와 통합합니다.
* `HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientHandler` 인스턴스의 풀링 및 수명을 관리합니다.
* 팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.

자세한 내용은 <xref:fundamentals/http-requests>를 참조하세요.

## <a name="content-root"></a>콘텐츠 루트

콘텐츠 루트는 다음의 기본 경로입니다.

* 앱을 호스트하는 실행 파일( *.exe* )
* 앱을 구성하는 컴파일된 어셈블리( *.dll* )
* 다음과 같은 앱에서 사용하는 비코드 콘텐츠 파일:
  * Razor 파일( *.cshtml* , *.razor* )
  * 구성 파일( *.json* , *.xml* )
  * 데이터 파일( *.db* )
* [웹 루트](#web-root)(일반적으로 *wwwroot* 폴더에 게시됨)

개발 중:

* 콘텐츠 루트의 기본값은 프로젝트의 루트 디렉터리입니다.
* 프로젝트의 루트 디렉터리는 다음을 만드는 데 사용됩니다.
  * 프로젝트의 루트 디렉터리에서 앱의 비코드 콘텐츠 파일의 경로
  * [웹 루트](#web-root)(일반적으로 프로젝트의 루트 디렉터리의 *wwwroot* 폴더)

[호스트를 빌드](#host)할 때 지정될 수 있는 대체 콘텐츠 루트 경로 자세한 내용은 <xref:fundamentals/host/web-host#content-root>를 참조하세요.

## <a name="web-root"></a>웹 루트

웹 루트는 다음과 같은 공용, 비코드, 정적 리소스 파일의 기본 경로입니다.

* 스타일시트( *.css* )
* JavaScript( *.js* )
* 이미지( *.png* , *.jpg* )

정적 파일은 기본적으로 웹 루트 디렉터리 및 하위 디렉터리에서만 제공됩니다.

웹 루트 경로는 *{content root}/wwwroot* 를 기본값으로 지정하지만 [호스트를 빌드](#host)할 때 다른 웹 루트를 지정할 수도 있습니다. 자세한 내용은 [웹 루트](xref:fundamentals/host/web-host#web-root)를 참조하세요.

프로젝트 파일에서 [\<Content> 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items#content)을 사용하여 *wwwroot* 에 파일을 게시하지 못하도록 합니다. 다음 예에서는 *wwwroot/local* 디렉터리 및 하위 디렉터리에 콘텐츠를 게시하지 못하도록 합니다.

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Razor( *.cshtml* ) 파일에서는 물결표 슬래시(`~/`)가 웹 루트를 가리킵니다. `~/`(으)로 시작하는 경로를 *가상 경로* 라고 합니다.

자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.

::: moniker-end
