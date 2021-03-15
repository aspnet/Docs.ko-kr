---
title: ASP.NET에서 ASP.NET Core로 마이그레이션
author: isaac2004
description: 기존 ASP.NET MVC 또는 Web API 앱을 ASP.NET Core.web으로 마이그레이션하기 위한 지침을 받으세요.
ms.author: scaddie
ms.date: 10/18/2019
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
uid: migration/proper-to-2x/index
ms.openlocfilehash: 7961890becc8f4513e0750f28341c9d4cf94e7ad
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413338"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a>ASP.NET에서 ASP.NET Core로 마이그레이션

작성자: [Isaac Levin](https://isaaclevin.com)

이 문서는 ASP.NET 앱을 ASP.NET Core로 마이그레이션하기 위한 참조 가이드로 사용됩니다. 포괄적인 포팅 가이드는 eBook [Porting existing ASP.NET apps to .NET Core](https://aka.ms/aspnet-porting-ebook)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

[.NET Core SDK 2.2 이상](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a>대상 프레임워크

ASP.NET Core 프로젝트를 사용하면 개발자가 유연하게 .NET Core, .NET Framework 또는 두 항목을 모두 대상으로 지정하거나 둘 다 대상으로 지정할 수 있습니다. 가장 적절한 대상 프레임워크를 결정하려면 [서버 앱에 대해 .NET Core와 .NET Framework 중에서 선택](/dotnet/standard/choosing-core-framework-server)을 참조하세요.

.NET Framework를 대상으로 지정할 경우 프로젝트는 개별 NuGet 패키지를 참조해야 합니다.

.NET Core를 대상으로 지정하면 ASP.NET Core [메타패키지](xref:fundamentals/metapackage-app) 덕분에 수많은 명시적 패키지 참조를 제거할 수 있습니다. 프로젝트에서 `Microsoft.AspNetCore.App` 메타패키지를 설치합니다.

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

메타패키지를 사용하면 메타패키지에서 참조되는 패키지가 앱과 함께 배포되지 않습니다. .NET Core 런타임 저장소에는 이러한 자산이 포함되고 해당 자산은 성능을 개선하도록 미리 컴파일되어 있습니다. 자세한 내용은 [ASP.NET Core에 대한 Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하세요.

## <a name="project-structure-differences"></a>프로젝트 구조 차이점

*.csproj* 파일 형식은 ASP.NET Core에서 간소화되었습니다. 몇 가지 주요 변경 내용은 다음과 같습니다.

- 파일을 프로젝트의 일부로 간주하기 위해 파일을 명시적으로 포함할 필요가 없습니다. 이 덕분에 대규모 팀에서 작업할 때 XML 병합 충돌의 위험이 감소합니다.
- 다른 프로젝트에 대한 GUID 기반 참조가 없으므로 파일 가독성이 향상됩니다.
- Visual Studio에서 파일을 언로드하지 않고 파일을 편집할 수 있습니다.

    ![Visual Studio 2017에서 CSPROJ 상황에 맞는 메뉴 옵션 편집](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Global.asax 파일 바꾸기

ASP.NET Core에는 앱을 부트스트랩하기 위한 새로운 메커니즘이 도입되었습니다. ASP.NET 애플리케이션의 진입점은 *Global.asax* 파일입니다. 경로 구성 및 필터/영역 등록과 같은 작업은 *Global.asax* 파일에서 처리됩니다.

[!code-csharp[](samples/globalasax-sample.cs)]

이 방법은 구현을 방해하는 방식으로 애플리케이션 및 애플리케이션이 배포되는 서버를 결합합니다. 분리 작업 시 여러 프레임워크를 함께 사용할 수 있는 더 분명한 방식을 제공하도록 [OWIN](https://owin.org/)이 도입되었습니다. OWIN은 필요한 모듈만 추가하기 위한 파이프라인을 제공합니다. 호스팅 환경에서는 [Startup](xref:fundamentals/startup) 함수를 사용하여 서비스 및 앱 요청 파이프라인을 구성합니다. `Startup`은 미들웨어 집합을 애플리케이션에 등록합니다. 각 요청에 대해 애플리케이션은 기존 처리기 집합에 대한 연결된 목록의 head 포인터를 사용하여 각 미들웨어 구성 요소를 호출합니다. 각 미들웨어 구성 요소는 요청 처리 파이프라인에 하나 이상의 처리기를 추가할 수 있습니다. 이 작업을 수행하려면 목록의 새 헤드인 처리기에 참조를 반환합니다. 각 처리기는 목록의 다음 처리기를 기억하고 호출해야 합니다. ASP.NET Core에서는 애플리케이션에 대한 진입점이 `Startup`이고 더 이상 *Global.asax* 에 대한 종속성을 포함하지 않습니다. .NET Framework에서 OWIN을 사용할 경우 다음과 같은 항목을 파이프라인으로 사용합니다.

[!code-csharp[](samples/webapi-owin.cs)]

이렇게 하면 기본 경로가 구성되고 기본적으로 JSON을 통한 XmlSerialization으로 설정됩니다. 필요에 따라 이 파이프라인에 다른 미들웨어를 추가합니다(로딩 서비스, 구성 설정, 정적 파일 등).

ASP.NET Core는 비슷한 방법을 사용하지만 항목을 처리하는 데 OWIN을 사용하지 않습니다. 대신, 이 작업에는 *Program.cs* `Main` 메서드(콘솔 애플리케이션과 비슷함)가 사용되고 `Startup`도 이 메서드를 통해 로드됩니다.

[!code-csharp[](samples/program.cs)]

`Startup`에는 `Configure` 메서드가 포함되어야 합니다. `Configure`에서 파이프라인에 필요한 미들웨어를 추가합니다. 기본 웹 사이트 템플릿을 기반으로 한 다음 예제에서는 확장 메서드가 다음 지원을 통해 파이프라인을 구성합니다.

- 오류 페이지
- HTTP 엄격한 전송 보안
- HTTPS로 HTTP 리디렉션
- ASP.NET Core MVC

[!code-csharp[](samples/startup.cs)]

호스트와 애플리케이션이 분리되었으므로 나중에 유연하게 다른 플랫폼으로 이동할 수 있습니다.

> [!NOTE]
> ASP.NET Core 시작 및 미들웨어에 대한 자세한 내용은 [ASP.NET Core의 시작](xref:fundamentals/startup)을 참조하세요.

## <a name="store-configurations"></a>구성 저장

ASP.NET은 정렬 설정을 지원합니다. 예를 들어 이러한 설정은 애플리케이션이 배포된 환경을 지원하는 데 사용됩니다. 일반적으로 모든 사용자 지정 키 값 쌍은 *Web.config* 파일의 `<appSettings>` 섹션에 저장됩니다.

[!code-xml[](samples/webconfig-sample.xml)]

애플리케이션은 `System.Configuration` 네임스페이스의 `ConfigurationManager.AppSettings` 컬렉션을 사용하여 이러한 설정을 읽습니다.

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core는 애플리케이션에 대한 구성 데이터를 파일에 저장하고 미들웨어 부트스트래핑의 일부로 로드할 수 있습니다. 프로젝트 템플릿에 사용되는 기본 파일은 *appsettings.json* 입니다.

[!code-json[](samples/appsettings-sample.json)]

파일을 애플리케이션 내부의 `IConfiguration` 인스턴스로 로드하는 작업은 *Startup.cs* 에서 수행됩니다.

[!code-csharp[](samples/startup-builder.cs)]

앱은 `Configuration`을 읽어서 설정을 가져옵니다.

[!code-csharp[](samples/read-appsettings.cs)]

DI([종속성 주입](xref:fundamentals/dependency-injection))를 사용하여 이러한 값으로 서비스를 로드하는 것과 같이 이 방법을 확장하여 프로세스를 더 강력하게 만들 수 있습니다. DI 방법은 강력한 형식의 구성 개체 집합을 제공합니다.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> ASP.NET Core 구성에 대한 자세한 내용은 [ASP.NET Core의 구성](xref:fundamentals/configuration/index)을 참조하세요.

## <a name="native-dependency-injection"></a>네이티브 종속성 주입

크고 확장 가능한 애플리케이션을 빌드할 때 중요한 목표는 구성 요소와 서비스를 느슨하게 결합하는 것입니다. [종속성 주입](xref:fundamentals/dependency-injection)은 이 목표를 위해 널리 사용되는 기술이고 ASP.NET Core의 네이티브 구성 요소입니다.

ASP.NET 앱에서 개발자는 타사 라이브러리를 사용하여 종속성 주입을 구현합니다. 이러한 라이브러리 중 하나는 Microsoft Patterns & Practices에서 제공하는 [Unity](https://github.com/unitycontainer/unity)입니다.

Unity를 사용한 종속성 주입 설정의 예로는 `UnityContainer`를 래핑하는 `IDependencyResolver`를 구현하는 것입니다.

[!code-csharp[](samples/sample8.cs)]

`UnityContainer`의 인스턴스를 만들고, 서비스를 등록하고, `HttpConfiguration`의 종속성 확인자를 컨테이너에 대한 `UnityResolver`의 새 인스턴스로 설정합니다.

[!code-csharp[](samples/sample9.cs)]

필요한 경우 `IProductRepository`를 삽입합니다.

[!code-csharp[](samples/sample5.cs)]

종속성 주입은 ASP.NET Core의 일부이므로 *Startup.cs* 의 `ConfigureServices` 메서드에서 서비스를 추가할 수 있습니다.

[!code-csharp[](samples/configure-services.cs)]

Unity에서 삽입한 것처럼 리포지토리는 어디든지 삽입될 수 있습니다.

> [!NOTE]
> 종속성 주입에 대한 자세한 내용은 [종속성 주입](xref:fundamentals/dependency-injection)을 참조하세요.

## <a name="serve-static-files"></a>정적 파일 제공

웹 개발의 중요한 부분은 정적 클라이언트 쪽 자산을 지원하는 기능입니다. 정적 파일의 가장 일반적인 예로는 HTML, CSS, Javascript 및 이미지가 있습니다. 이러한 파일은 앱(또는 CDN)의 게시된 위치에 저장되고 요청을 통해 로드될 수 있도록 참조되어야 합니다. 이 프로세스는 ASP.NET Core에서 변경되었습니다.

ASP.NET에서 정적 파일은 다양한 디렉터리에 저장되고 뷰에서 참조됩니다.

ASP.NET Core에서 정적 파일은 별도로 구성되지 않는 한 “웹 루트”( *&lt;content root&gt;/wwwroot*)에 저장됩니다. 파일은 `Startup.Configure`에서 `UseStaticFiles` 확장 메서드를 호출하는 방식으로 요청 파이프라인에 로드됩니다.

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> .NET Framework를 대상으로 지정할 경우 NuGet 패키지 `Microsoft.AspNetCore.StaticFiles`를 설치합니다.

예를 들어 *wwwroot/images* 폴더의 이미지 자산은 `http://<app>/images/<imageFileName>`과 같은 위치의 브라우저에 액세스할 수 있습니다.

> [!NOTE]
> ASP.NET Core의 정적 파일 지원에 대한 자세한 내용은 [정적 파일](xref:fundamentals/static-files)을 참조하세요.

## <a name="multi-value-cookies"></a>다중 값 cookie 여부

[다중 값 cookie](xref:System.Web.HttpCookie.Values)는 ASP.NET Core에서 지원되지 않습니다. 값마다 하나의 cookie를 만듭니다.

## <a name="authentication-cookies-are-not-compressed-in-aspnet-core"></a>인증 cookie는 ASP.NET Core에서 압축되지 않습니다.

[!INCLUDE[](~/includes/cookies-not-compressed.md)]

## <a name="partial-app-migration"></a>부분 앱 마이그레이션

부분 앱 마이그레이션의 한 가지 방법은 IIS 하위 애플리케이션을 만들고 앱의 URL 구조를 유지하면서 특정 경로만 ASP.NET 4.x에서 ASP.NET Core로 이동하는 것입니다. 예를 들어 *applicationHost.config* 파일의 앱 URL 구조를 살펴보세요.

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

디렉터리 구조:

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="bind-and-input-formatters"></a>[BIND] 및 입력 포맷터

[이전 버전의 ASP.NET](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view)에서는 `[Bind]` 특성을 사용하여 초과 게시 공격으로부터 보호했습니다. ASP.NET Core에서 [입력 포맷터](xref:mvc/models/model-binding#input-formatters)는 다르게 작동합니다. JSON 또는 XML을 구문 분석하기 위해 입력 포맷터와 함께 사용되는 경우 `[Bind]` 특성은 더 이상 초과 게시를 방지하도록 설계되지 않습니다. 이 특성은 데이터의 소스가 `x-www-form-urlencoded` 콘텐츠 양식으로 게시된 양식 데이터인 경우 모델 바인딩에 영향을 줍니다.

JSON 정보를 컨트롤러에 게시하고 JSON 입력 포맷터를 사용하여 데이터를 구문 분석하는 앱의 경우에는 `[Bind]` 특성을 `[Bind]` 특성으로 정의된 속성과 일치하는 보기 모델로 바꾸는 것이 좋습니다.

## <a name="additional-resources"></a>추가 자료

- [.NET Core로 라이브러리 이식](/dotnet/core/porting/libraries)
