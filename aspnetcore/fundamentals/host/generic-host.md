---
title: ASP.NET Core의 .NET 일반 호스트
author: rick-anderson
description: ASP.NET Core 앱에서 .NET Core 일반 호스트를 사용합니다.  일반 호스트는 앱 시작 및 수명 관리를 담당합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: 3e44932c302713132a37534b97fffdd91acce2c7
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234558"
---
# <a name="net-generic-host-in-aspnet-core"></a>ASP.NET Core의 .NET 일반 호스트

::: moniker range=">= aspnetcore-5.0"

ASP.NET Core 템플릿은 .NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 만듭니다.

이 항목에서는 ASP.NET Core에서 .NET 일반 호스트를 사용하는 방법에 관한 정보를 제공합니다. 콘솔 앱에서 .NET 일반 호스트를 사용하는 방법에 관한 자세한 내용은 [.NET 일반 호스트](/dotnet/core/extensions/generic-host)를 참조하세요.

## <a name="host-definition"></a>호스트 정의

*호스트* 는 다음과 같이 앱의 리소스를 캡슐화하는 개체입니다.

* DI(종속성 주입)
* 로깅
* Configuration
* `IHostedService` 구현

호스트가 시작될 때 서비스 컨테이너의 호스티드 서비스 컬렉션에 등록된 <xref:Microsoft.Extensions.Hosting.IHostedService>의 각 구현에서 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType>을 호출합니다. 웹앱에서 `IHostedService` 구현 중 하나는 [HTTP 서버 구현](xref:fundamentals/index#servers)을 시작하는 웹 서비스입니다.

하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.

## <a name="set-up-a-host"></a>호스트 설정

호스트는 일반적으로 `Program` 클래스의 코드로 구성, 빌드 및 실행됩니다. `Main` 메서드는 다음 작업을 수행합니다.

* `CreateHostBuilder` 메서드를 호출하여 작성기 개체를 만들고 구성합니다.
* 작성기 개체에서 `Build` 및 `Run` 메서드를 호출합니다.

ASP.NET Core 웹 템플릿은 다음과 같은 코드를 생성하여 호스트를 만듭니다.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

다음 코드는 DI 컨테이너에 `IHostedService` 구현이 추가된 비 HTTP 워크로드를 만듭니다.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

HTTP 워크로드의 경우 `Main` 메서드는 동일하지만 `CreateHostBuilder`는 `ConfigureWebHostDefaults`를 호출합니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

앱에서 Entity Framework Core를 사용하는 경우 `CreateHostBuilder` 메서드의 이름이나 서명을 변경하지 마세요. [Entity Framework Core 도구](/ef/core/miscellaneous/cli/)는 앱을 실행하지 않고 호스트를 구성하는 `CreateHostBuilder` 메서드를 찾으려고 합니다. 자세한 내용은 [디자인 타임 DbContext 만들기](/ef/core/miscellaneous/cli/dbcontext-creation)를 참조하세요.

## <a name="default-builder-settings"></a>기본 작성기 설정

<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 메서드는 다음 작업을 수행합니다.

* [콘텐츠 루트](xref:fundamentals/index#content-root)를 <xref:System.IO.Directory.GetCurrentDirectory*>에서 반환된 경로로 설정합니다.
* 다음에서 호스트 구성을 로드합니다.
  * 접두사가 `DOTNET_`인 환경 변수.
  * 명령줄 인수.
* 다음에서 앱 구성을 로드합니다.
  * *appsettings.json*.
  * *appsettings.{Environment}.json*.
  * [비밀 관리자](xref:security/app-secrets): 앱이 `Development` 환경에서 실행되는 경우
  * 환경 변수.
  * 명령줄 인수.
* 다음 [로깅](xref:fundamentals/logging/index) 공급자를 추가합니다.
  * Console
  * Debug
  * EventSource
  * EventLog(Windows에서 실행 중인 경우에만)
* 환경이 개발 중일 때 [범위 유효성 검사](xref:fundamentals/dependency-injection#scope-validation) 및 [종속성 유효성 검사](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)를 사용하도록 설정합니다.

<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 메서드는 다음 작업을 수행합니다.

* 접두사가 `ASPNETCORE_`인 환경 변수에서 호스트 구성을 로드합니다.
* [Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 설정하고 앱의 호스팅 구성 공급자를 사용하여 구성합니다. Kestrel 서버의 기본 옵션은 <xref:fundamentals/servers/kestrel#kestrel-options>을 참조하세요.
* [호스트 필터링 미들웨어](xref:fundamentals/servers/kestrel#host-filtering)를 추가합니다.
* `ASPNETCORE_FORWARDEDHEADERS_ENABLED`가 `true`와 같으면 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)를 추가합니다.
* IIS 통합을 사용합니다. IIS 기본 옵션은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.

이 문서 뒷부분의 [모든 앱 유형에 대한 설정](#settings-for-all-app-types) 및 [웹앱 설정](#settings-for-web-apps) 섹션에서는 기본 작성기 설정을 재정의하는 방법을 보여줍니다.

## <a name="framework-provided-services"></a>프레임워크에서 제공하는 서비스

다음 서비스가 자동으로 등록됩니다.

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

프레임워크에서 제공하는 서비스에 대한 자세한 내용은 <xref:fundamentals/dependency-injection#framework-provided-services>를 참조하세요.

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>(이전의 `IApplicationLifetime`) 서비스를 모든 클래스에 삽입하여 사후 시작 및 정상 종료 작업을 처리합니다. 인터페이스의 세 가지 속성은 앱 시작 및 앱 중지 이벤트 처리기 메서드를 등록하는 데 사용되는 취소 토큰입니다. 인터페이스에는 `StopApplication` 메서드도 포함됩니다.

다음 예제는 `IHostApplicationLifetime` 이벤트를 등록하는 `IHostedService` 구현입니다.

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

<xref:Microsoft.Extensions.Hosting.IHostLifetime> 구현은 호스트가 시작될 때와 중지될 때를 제어합니다. 등록된 마지막 구현이 사용됩니다.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 `IHostLifetime` 구현입니다. `ConsoleLifetime`:

* <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*>을 호출하여 종료 프로세스를 시작합니다.
* [RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.

## <a name="ihostenvironment"></a>IHostEnvironment

<xref:Microsoft.Extensions.Hosting.IHostEnvironment> 서비스를 클래스에 삽입하여 다음 설정에 대한 정보를 가져옵니다.

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentroot)

웹앱은 `IHostEnvironment`를 상속하고 [WebRootPath](#webroot)를 추가하는 `IWebHostEnvironment` 인터페이스를 구현합니다.

## <a name="host-configuration"></a>호스트 구성

호스트 구성은 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 구현의 속성에 사용됩니다.

호스트 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 내부에 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)에서 사용할 수 있습니다. `ConfigureAppConfiguration` 다음에 `HostBuilderContext.Configuration`이 앱 구성으로 대체됩니다.

호스트 구성을 추가하려면 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 호출합니다. `ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다. 호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.

접두사 `DOTNET_` 및 명령줄 인수가 있는 환경 변수 공급자는 `CreateDefaultBuilder`에 포함되어 있습니다. 웹앱의 경우 접두사 `ASPNETCORE_`가 있는 환경 변수 공급자가 추가됩니다. 접두사는 환경 변수를 읽을 때 제거됩니다. 예를 들어 `ASPNETCORE_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.

다음 예제에서는 호스트 구성을 만듭니다.

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>앱 구성

앱 구성은 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다. `ConfigureAppConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다. 앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다. 

`ConfigureAppConfiguration`에 의해 생성된 구성은 다음 작업을 위해 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)에서 사용 가능하거나 DI의 서비스로 사용할 수 있습니다. 호스트 구성도 앱 구성에 추가됩니다.

자세한 내용은 [ASP.NET Core의 구성](xref:fundamentals/configuration/index#configureappconfiguration)을 참조하세요.

## <a name="settings-for-all-app-types"></a>모든 앱 유형에 대한 설정

이 섹션에는 HTTP 및 비 HTTP 워크로드 모두에 적용되는 호스트 설정이 나열되어 있습니다. 기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다. 자세한 내용은 [기본 작성기 설정](#default-builder-settings) 섹션을 참조하세요.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

호스트를 생성하는 동안 호스트 구성에서 [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 속성을 설정합니다.

**키** : `applicationName`  
**형식** : `string`  
**기본값** : 앱의 진입점을 포함하는 어셈블리의 이름입니다.  
**환경 변수** : `<PREFIX_>APPLICATIONNAME`

이 값을 설정하려면 환경 변수를 사용합니다. 

### <a name="contentroot"></a>ContentRoot

[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 속성은 콘텐츠 파일 검색을 시작하는 위치를 결정합니다. 경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.

**키** : `contentRoot`  
**형식** : `string`  
**기본값** : 앱 어셈블리가 있는 폴더입니다.  
**환경 변수** : `<PREFIX_>CONTENTROOT`

이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseContentRoot`를 호출합니다.

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

자세한 내용은 다음을 참조하세요.

* [기본 사항: 콘텐츠 루트](xref:fundamentals/index#content-root)
* [WebRoot](#webroot)

### <a name="environmentname"></a>EnvironmentName

[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 속성을 임의의 값으로 설정할 수 있습니다. 프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다. 값은 대/소문자를 구분하지 않습니다.

**키** : `environment`  
**형식** : `string`  
**기본** : `Production`  
**환경 변수** : `<PREFIX_>ENVIRONMENT`

이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseEnvironment`를 호출합니다.

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*)이 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다. 기본값은 5초입니다.  시간 제한 기간 동안 호스트는 다음을 수행합니다.

* [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)을 트리거합니다.
* 중지하지 못한 서비스에 대한 오류를 로깅하면서 호스팅된 서비스 중지를 시도합니다.

모든 호스팅된 서비스가 중지하기 전에 시간 제한 기간이 만료되면 앱이 종료될 때 모든 활성화된 나머지 서비스가 중지됩니다. 처리를 완료하지 않은 경우에도 서비스가 중지됩니다. 서비스를 중지하는 데 시간이 더 필요한 경우 시간 제한을 늘립니다.

**키** : `shutdownTimeoutSeconds`  
**형식** : `int`  
**기본값** : 5초  
**환경 변수** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

이 값을 설정하려면 환경 변수를 사용하거나 `HostOptions`를 구성합니다. 다음 예제에서는 시간 제한을 20초로 설정합니다.

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a>변경 시 앱 구성 다시 로드 사용 안 함

[기본적으로](xref:fundamentals/configuration/index#default) 파일이 변경되면 *appsettings.json* 및 *appsettings.{Environment}.json* 이 다시 로드됩니다. ASP.NET Core 5.0 이상에서 해당 다시 로드 동작을 사용하지 않도록 설정하려면 `hostBuilder:reloadConfigOnChange` 키를 `false`로 설정합니다.

**키** : `hostBuilder:reloadConfigOnChange`  
**형식** : `bool`(`true` 또는 `1`)  
**기본** : `true`  
**명령줄 인수** : `hostBuilder:reloadConfigOnChange`  
**환경 변수** : `<PREFIX_>hostBuilder:reloadConfigOnChange`

> [!WARNING]
> 콜론(`:`) 구분 기호는 모든 플랫폼의 환경 변수 계층적 키에서 작동하지 않습니다. 자세한 내용은 [환경 변수](xref:fundamentals/configuration/index#environment-variables)를 참조하세요.

## <a name="settings-for-web-apps"></a>웹앱 설정

일부 호스트 설정은 HTTP 워크로드에만 적용됩니다. 기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.

이러한 설정에 대해 `IWebHostBuilder`의 확장 메서드를 사용할 수 있습니다. 확장 메서드를 호출하는 방법을 보여주는 코드 샘플은 다음 예제와 같이 `webBuilder`가 `IWebHostBuilder`의 인스턴스라고 가정합니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

`false`인 경우 시작 시 오류가 발생하면 호스트가 종료됩니다. `true`이면 호스트가 시작 시 예외를 캡처하고 서버 시작을 시도합니다.

**키** : `captureStartupErrors`  
**형식** : `bool`(`true` 또는 `1`)  
**기본값** : 기본값이 `true`인 IIS 뒤에 있는 Kestrel로 앱이 실행하지 않는 한 기본값은 `false`로 지정됩니다.  
**환경 변수** : `<PREFIX_>CAPTURESTARTUPERRORS`

이 값을 설정하려면 구성을 사용하거나 `CaptureStartupErrors`를 호출합니다.

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

활성화하거나 환경이 `Development`인 경우 앱은 자세한 오류를 캡처합니다.

**키** : `detailedErrors`  
**형식** : `bool`(`true` 또는 `1`)  
**기본** : `false`  
**환경 변수** : `<PREFIX_>_DETAILEDERRORS`

이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

시작 시 로드할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다. 구성 값의 기본값이 빈 문자열이지만, 호스팅 시작 어셈블리는 항상 앱의 어셈블리를 포함합니다. 호스팅 시작 어셈블리가 제공되는 경우, 시작 시 앱이 일반적인 서비스를 빌드할 때 로드를 위해 앱의 어셈블리에 추가됩니다.

**키** : `hostingStartupAssemblies`  
**형식** : `string`  
**기본값** : 빈 문자열  
**환경 변수** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.

**키** : `hostingStartupExcludeAssemblies`  
**형식** : `string`  
**기본값** : 빈 문자열  
**환경 변수** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

HTTPS 리디렉션 포트. [HTTPS 적용](xref:security/enforcing-ssl)에 사용됩니다.

**키** : `https_port`  
**형식** : `string`  
**기본값** : 기본값은 설정되지 않습니다.  
**환경 변수** : `<PREFIX_>HTTPS_PORT`

이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

호스트가 `IServer` 구현으로 구성된 URL 대신에 `IWebHostBuilder`로 구성된 URL에서 수신 대기할지 아닌지를 나타냅니다.

**키** : `preferHostingUrls`  
**형식** : `bool`(`true` 또는 `1`)  
**기본** : `true`  
**환경 변수** : `<PREFIX_>_PREFERHOSTINGURLS`

이 값을 설정하려면 환경 변수를 사용하거나 `PreferHostingUrls`를 호출합니다.

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

앱의 어셈블리에 의해 구성된 호스팅 시작 어셈블리를 포함한 호스팅 시작 어셈블리의 자동 로딩을 방지합니다. 자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.

**키** : `preventHostingStartup`  
**형식** : `bool`(`true` 또는 `1`)  
**기본** : `false`  
**환경 변수** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`

이 값을 설정하려면 환경 변수를 사용하거나 `UseSetting`을 호출합니다.

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

`Startup` 클래스를 검색할 어셈블리입니다.

**키** : `startupAssembly`  
**형식** : `string`  
**기본값** : 앱의 어셈블리  
**환경 변수** : `<PREFIX_>STARTUPASSEMBLY`

이 값을 설정하려면 환경 변수를 사용하거나 `UseStartup`을 호출합니다. `UseStartup`은 어셈블리 이름(`string`) 또는 형식(`TStartup`)을 사용할 수 있습니다. `UseStartup` 메서드가 여러 개 호출된 경우 마지막 메서드가 우선 적용됩니다.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URL

서버에서 요청을 수신해야 하는 포트와 프로토콜을 포함하는 세미클론으로 구분된 IP 주소 또는 호스트 주소의 목록입니다. 예: `http://localhost:123`. “\*”를 사용하여 서버가 지정된 포트 및 프로토콜을 사용하는 IP 주소 또는 호스트 이름에서 요청을 수신해야 함을 나타냅니다(예: `http://*:5000`). 프로토콜(`http://` 또는 `https://`)은 각 URL에 포함되어 있어야 합니다. 지원되는 형식은 서버마다 다릅니다.

**키** : `urls`  
**형식** : `string`  
**기본값** : `http://localhost:5000` 및 `https://localhost:5001`  
**환경 변수** : `<PREFIX_>URLS`

이 값을 설정하려면 환경 변수를 사용하거나 `UseUrls`를 호출합니다.

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel에는 자체 엔드포인트 구성 API가 있습니다. 자세한 내용은 <xref:fundamentals/servers/kestrel#endpoint-configuration>를 참조하세요.

### <a name="webroot"></a>WebRoot

[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) 속성은 앱 정적 자산의 상대 경로를 결정합니다. 경로가 존재하지 않으면 no-op 파일 공급자가 사용됩니다.  

**키** : `webroot`  
**형식** : `string`  
**기본값** : 기본값은 `wwwroot`입니다. *{content root}/wwwroot* 경로가 존재해야 합니다.  
**환경 변수** : `<PREFIX_>WEBROOT`

이 값을 설정하려면 환경 변수를 사용하거나 `IWebHostBuilder`에서 `UseWebRoot`를 호출합니다.

```csharp
webBuilder.UseWebRoot("public");
```

자세한 내용은 다음을 참조하세요.

* [기본 사항: 웹 루트](xref:fundamentals/index#web-root)
* [ContentRoot](#contentroot)

## <a name="manage-the-host-lifetime"></a>호스트 수명 관리

기본 제공된 <xref:Microsoft.Extensions.Hosting.IHost> 구현에 대한 메서드를 호출하여 애플리케이션을 시작하고 중지합니다. 이러한 메서드는 서비스 컨테이너에 등록된 모든 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현에 영향을 줍니다.

### <a name="run"></a>Run

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔을 지원하고 호스트를 빌드 및 시작하며 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.

### <a name="start"></a>Start

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>는 호스트를 시작하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 `StartAsync`의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다. 이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 통해 IHostLifetime에 의해 종료가 트리거될 때까지 호출 스레드를 차단합니다.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.

### <a name="external-control"></a>외부 제어

호스트 수명에 대한 직접 제어는 외부에서 호출할 수 있는 메서드를 사용하여 달성할 수 있습니다.

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

ASP.NET Core 템플릿은 .NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 만듭니다.

이 항목에서는 ASP.NET Core에서 .NET 일반 호스트를 사용하는 방법에 관한 정보를 제공합니다. 콘솔 앱에서 .NET 일반 호스트를 사용하는 방법에 관한 자세한 내용은 [.NET 일반 호스트](/dotnet/core/extensions/generic-host)를 참조하세요.

## <a name="host-definition"></a>호스트 정의

*호스트* 는 다음과 같이 앱의 리소스를 캡슐화하는 개체입니다.

* DI(종속성 주입)
* 로깅
* Configuration
* `IHostedService` 구현

호스트가 시작될 때 서비스 컨테이너의 호스티드 서비스 컬렉션에 등록된 <xref:Microsoft.Extensions.Hosting.IHostedService>의 각 구현에서 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType>을 호출합니다. 웹앱에서 `IHostedService` 구현 중 하나는 [HTTP 서버 구현](xref:fundamentals/index#servers)을 시작하는 웹 서비스입니다.

하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.

## <a name="set-up-a-host"></a>호스트 설정

호스트는 일반적으로 `Program` 클래스의 코드로 구성, 빌드 및 실행됩니다. `Main` 메서드는 다음 작업을 수행합니다.

* `CreateHostBuilder` 메서드를 호출하여 작성기 개체를 만들고 구성합니다.
* 작성기 개체에서 `Build` 및 `Run` 메서드를 호출합니다.

ASP.NET Core 웹 템플릿은 다음과 같은 코드를 생성하여 일반 호스트를 만듭니다.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

다음 코드는 비 HTTP 워크로드를 사용하여 일반 호스트를 만듭니다. `IHostedService` 구현은 DI 컨테이너에 추가됩니다.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

HTTP 워크로드의 경우 `Main` 메서드는 동일하지만 `CreateHostBuilder`는 `ConfigureWebHostDefaults`를 호출합니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

위의 코드는 ASP.NET Core 템플릿에서 생성됩니다.

앱에서 Entity Framework Core를 사용하는 경우 `CreateHostBuilder` 메서드의 이름이나 서명을 변경하지 마세요. [Entity Framework Core 도구](/ef/core/miscellaneous/cli/)는 앱을 실행하지 않고 호스트를 구성하는 `CreateHostBuilder` 메서드를 찾으려고 합니다. 자세한 내용은 [디자인 타임 DbContext 만들기](/ef/core/miscellaneous/cli/dbcontext-creation)를 참조하세요.

## <a name="default-builder-settings"></a>기본 작성기 설정

<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 메서드는 다음 작업을 수행합니다.

* [콘텐츠 루트](xref:fundamentals/index#content-root)를 <xref:System.IO.Directory.GetCurrentDirectory%2A>에서 반환된 경로로 설정합니다.
* 다음에서 호스트 구성을 로드합니다.
  * 접두사가 `DOTNET_`인 환경 변수.
  * 명령줄 인수.
* 다음에서 앱 구성을 로드합니다.
  * *appsettings.json*.
  * *appsettings.{Environment}.json*.
  * [비밀 관리자](xref:security/app-secrets): 앱이 `Development` 환경에서 실행되는 경우
  * 환경 변수.
  * 명령줄 인수.
* 다음 [로깅](xref:fundamentals/logging/index) 공급자를 추가합니다.
  * Console
  * Debug
  * EventSource
  * EventLog(Windows에서 실행 중인 경우에만)
* 환경이 개발 중일 때 [범위 유효성 검사](xref:fundamentals/dependency-injection#scope-validation) 및 [종속성 유효성 검사](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)를 사용하도록 설정합니다.

`ConfigureWebHostDefaults` 메서드는 다음 작업을 수행합니다.

* 접두사가 `ASPNETCORE_`인 환경 변수에서 호스트 구성을 로드합니다.
* [Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 설정하고 앱의 호스팅 구성 공급자를 사용하여 구성합니다. Kestrel 서버의 기본 옵션은 <xref:fundamentals/servers/kestrel#kestrel-options>을 참조하세요.
* [호스트 필터링 미들웨어](xref:fundamentals/servers/kestrel#host-filtering)를 추가합니다.
* `ASPNETCORE_FORWARDEDHEADERS_ENABLED`가 `true`와 같으면 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)를 추가합니다.
* IIS 통합을 사용합니다. IIS 기본 옵션은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.

이 문서 뒷부분의 [모든 앱 유형에 대한 설정](#settings-for-all-app-types) 및 [웹앱 설정](#settings-for-web-apps) 섹션에서는 기본 작성기 설정을 재정의하는 방법을 보여줍니다.

## <a name="framework-provided-services"></a>프레임워크에서 제공하는 서비스

다음 서비스가 자동으로 등록됩니다.

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

프레임워크에서 제공하는 서비스에 대한 자세한 내용은 <xref:fundamentals/dependency-injection#framework-provided-services>를 참조하세요.

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>(이전의 `IApplicationLifetime`) 서비스를 모든 클래스에 삽입하여 사후 시작 및 정상 종료 작업을 처리합니다. 인터페이스의 세 가지 속성은 앱 시작 및 앱 중지 이벤트 처리기 메서드를 등록하는 데 사용되는 취소 토큰입니다. 인터페이스에는 `StopApplication` 메서드도 포함됩니다.

다음 예제는 `IHostApplicationLifetime` 이벤트를 등록하는 `IHostedService` 구현입니다.

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

<xref:Microsoft.Extensions.Hosting.IHostLifetime> 구현은 호스트가 시작될 때와 중지될 때를 제어합니다. 등록된 마지막 구현이 사용됩니다.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 `IHostLifetime` 구현입니다. `ConsoleLifetime`:

* <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A>을 호출하여 종료 프로세스를 시작합니다.
* [RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.

## <a name="ihostenvironment"></a>IHostEnvironment

<xref:Microsoft.Extensions.Hosting.IHostEnvironment> 서비스를 클래스에 삽입하여 다음 설정에 대한 정보를 가져옵니다.

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentroot)

웹앱은 `IHostEnvironment`를 상속하고 [WebRootPath](#webroot)를 추가하는 `IWebHostEnvironment` 인터페이스를 구현합니다.

## <a name="host-configuration"></a>호스트 구성

호스트 구성은 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 구현의 속성에 사용됩니다.

호스트 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 내부에 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)에서 사용할 수 있습니다. `ConfigureAppConfiguration` 다음에 `HostBuilderContext.Configuration`이 앱 구성으로 대체됩니다.

호스트 구성을 추가하려면 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A>을 호출합니다. `ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다. 호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.

접두사 `DOTNET_` 및 명령줄 인수가 있는 환경 변수 공급자는 `CreateDefaultBuilder`에 포함되어 있습니다. 웹앱의 경우 접두사 `ASPNETCORE_`가 있는 환경 변수 공급자가 추가됩니다. 접두사는 환경 변수를 읽을 때 제거됩니다. 예를 들어 `ASPNETCORE_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.

다음 예제에서는 호스트 구성을 만듭니다.

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>앱 구성

앱 구성은 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다. `ConfigureAppConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다. 앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다. 

`ConfigureAppConfiguration`에 의해 생성된 구성은 다음 작업을 위해 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)에서 사용 가능하거나 DI의 서비스로 사용할 수 있습니다. 호스트 구성도 앱 구성에 추가됩니다.

자세한 내용은 [ASP.NET Core의 구성](xref:fundamentals/configuration/index#configureappconfiguration)을 참조하세요.

## <a name="settings-for-all-app-types"></a>모든 앱 유형에 대한 설정

이 섹션에는 HTTP 및 비 HTTP 워크로드 모두에 적용되는 호스트 설정이 나열되어 있습니다. 기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

호스트를 생성하는 동안 호스트 구성에서 [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 속성을 설정합니다.

**키** : `applicationName`  
**형식** : `string`  
**기본값** : 앱의 진입점을 포함하는 어셈블리의 이름입니다.  
**환경 변수** : `<PREFIX_>APPLICATIONNAME`

이 값을 설정하려면 환경 변수를 사용합니다. 

### <a name="contentroot"></a>ContentRoot

[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 속성은 콘텐츠 파일 검색을 시작하는 위치를 결정합니다. 경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.

**키** : `contentRoot`  
**형식** : `string`  
**기본값** : 앱 어셈블리가 있는 폴더입니다.  
**환경 변수** : `<PREFIX_>CONTENTROOT`

이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseContentRoot`를 호출합니다.

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

자세한 내용은 다음을 참조하세요.

* [기본 사항: 콘텐츠 루트](xref:fundamentals/index#content-root)
* [WebRoot](#webroot)

### <a name="environmentname"></a>EnvironmentName

[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 속성을 임의의 값으로 설정할 수 있습니다. 프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다. 값은 대/소문자를 구분하지 않습니다.

**키** : `environment`  
**형식** : `string`  
**기본** : `Production`  
**환경 변수** : `<PREFIX_>ENVIRONMENT`

이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseEnvironment`를 호출합니다.

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*)이 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다. 기본값은 5초입니다.  시간 제한 기간 동안 호스트는 다음을 수행합니다.

* [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)을 트리거합니다.
* 중지하지 못한 서비스에 대한 오류를 로깅하면서 호스팅된 서비스 중지를 시도합니다.

모든 호스팅된 서비스가 중지하기 전에 시간 제한 기간이 만료되면 앱이 종료될 때 모든 활성화된 나머지 서비스가 중지됩니다. 처리를 완료하지 않은 경우에도 서비스가 중지됩니다. 서비스를 중지하는 데 시간이 더 필요한 경우 시간 제한을 늘립니다.

**키** : `shutdownTimeoutSeconds`  
**형식** : `int`  
**기본값** : 5초  
**환경 변수** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

이 값을 설정하려면 환경 변수를 사용하거나 `HostOptions`를 구성합니다. 다음 예제에서는 시간 제한을 20초로 설정합니다.

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>웹앱 설정

일부 호스트 설정은 HTTP 워크로드에만 적용됩니다. 기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.

이러한 설정에 대해 `IWebHostBuilder`의 확장 메서드를 사용할 수 있습니다. 확장 메서드를 호출하는 방법을 보여주는 코드 샘플은 다음 예제와 같이 `webBuilder`가 `IWebHostBuilder`의 인스턴스라고 가정합니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

`false`인 경우 시작 시 오류가 발생하면 호스트가 종료됩니다. `true`이면 호스트가 시작 시 예외를 캡처하고 서버 시작을 시도합니다.

**키** : `captureStartupErrors`  
**형식** : `bool`(`true` 또는 `1`)  
**기본값** : 기본값이 `true`인 IIS 뒤에 있는 Kestrel로 앱이 실행하지 않는 한 기본값은 `false`로 지정됩니다.  
**환경 변수** : `<PREFIX_>CAPTURESTARTUPERRORS`

이 값을 설정하려면 구성을 사용하거나 `CaptureStartupErrors`를 호출합니다.

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

활성화하거나 환경이 `Development`인 경우 앱은 자세한 오류를 캡처합니다.

**키** : `detailedErrors`  
**형식** : `bool`(`true` 또는 `1`)  
**기본** : `false`  
**환경 변수** : `<PREFIX_>_DETAILEDERRORS`

이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

시작 시 로드할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다. 구성 값의 기본값이 빈 문자열이지만, 호스팅 시작 어셈블리는 항상 앱의 어셈블리를 포함합니다. 호스팅 시작 어셈블리가 제공되는 경우, 시작 시 앱이 일반적인 서비스를 빌드할 때 로드를 위해 앱의 어셈블리에 추가됩니다.

**키** : `hostingStartupAssemblies`  
**형식** : `string`  
**기본값** : 빈 문자열  
**환경 변수** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.

**키** : `hostingStartupExcludeAssemblies`  
**형식** : `string`  
**기본값** : 빈 문자열  
**환경 변수** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

HTTPS 리디렉션 포트. [HTTPS 적용](xref:security/enforcing-ssl)에 사용됩니다.

**키** : `https_port`  
**형식** : `string`  
**기본값** : 기본값은 설정되지 않습니다.  
**환경 변수** : `<PREFIX_>HTTPS_PORT`

이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

호스트가 `IServer` 구현으로 구성된 URL 대신에 `IWebHostBuilder`로 구성된 URL에서 수신 대기할지 아닌지를 나타냅니다.

**키** : `preferHostingUrls`  
**형식** : `bool`(`true` 또는 `1`)  
**기본** : `true`  
**환경 변수** : `<PREFIX_>_PREFERHOSTINGURLS`

이 값을 설정하려면 환경 변수를 사용하거나 `PreferHostingUrls`를 호출합니다.

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

앱의 어셈블리에 의해 구성된 호스팅 시작 어셈블리를 포함한 호스팅 시작 어셈블리의 자동 로딩을 방지합니다. 자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.

**키** : `preventHostingStartup`  
**형식** : `bool`(`true` 또는 `1`)  
**기본** : `false`  
**환경 변수** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`

이 값을 설정하려면 환경 변수를 사용하거나 `UseSetting`을 호출합니다.

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

`Startup` 클래스를 검색할 어셈블리입니다.

**키** : `startupAssembly`  
**형식** : `string`  
**기본값** : 앱의 어셈블리  
**환경 변수** : `<PREFIX_>STARTUPASSEMBLY`

이 값을 설정하려면 환경 변수를 사용하거나 `UseStartup`을 호출합니다. `UseStartup`은 어셈블리 이름(`string`) 또는 형식(`TStartup`)을 사용할 수 있습니다. `UseStartup` 메서드가 여러 개 호출된 경우 마지막 메서드가 우선 적용됩니다.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URL

서버에서 요청을 수신해야 하는 포트와 프로토콜을 포함하는 세미클론으로 구분된 IP 주소 또는 호스트 주소의 목록입니다. 예: `http://localhost:123`. “\*”를 사용하여 서버가 지정된 포트 및 프로토콜을 사용하는 IP 주소 또는 호스트 이름에서 요청을 수신해야 함을 나타냅니다(예: `http://*:5000`). 프로토콜(`http://` 또는 `https://`)은 각 URL에 포함되어 있어야 합니다. 지원되는 형식은 서버마다 다릅니다.

**키** : `urls`  
**형식** : `string`  
**기본값** : `http://localhost:5000` 및 `https://localhost:5001`  
**환경 변수** : `<PREFIX_>URLS`

이 값을 설정하려면 환경 변수를 사용하거나 `UseUrls`를 호출합니다.

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel에는 자체 엔드포인트 구성 API가 있습니다. 자세한 내용은 <xref:fundamentals/servers/kestrel#endpoint-configuration>를 참조하세요.

### <a name="webroot"></a>WebRoot

[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) 속성은 앱 정적 자산의 상대 경로를 결정합니다. 경로가 존재하지 않으면 no-op 파일 공급자가 사용됩니다.  

**키** : `webroot`  
**형식** : `string`  
**기본값** : 기본값은 `wwwroot`입니다. *{content root}/wwwroot* 경로가 존재해야 합니다.  
**환경 변수** : `<PREFIX_>WEBROOT`

이 값을 설정하려면 환경 변수를 사용하거나 `IWebHostBuilder`에서 `UseWebRoot`를 호출합니다.

```csharp
webBuilder.UseWebRoot("public");
```

자세한 내용은 다음을 참조하세요.

* [기본 사항: 웹 루트](xref:fundamentals/index#web-root)
* [ContentRoot](#contentroot)

## <a name="manage-the-host-lifetime"></a>호스트 수명 관리

기본 제공된 <xref:Microsoft.Extensions.Hosting.IHost> 구현에 대한 메서드를 호출하여 애플리케이션을 시작하고 중지합니다. 이러한 메서드는 서비스 컨테이너에 등록된 모든 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현에 영향을 줍니다.

### <a name="run"></a>Run

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔을 지원하고 호스트를 빌드 및 시작하며 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.

### <a name="start"></a>Start

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>는 호스트를 시작하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 `StartAsync`의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다. 이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 통해 IHostLifetime에 의해 종료가 트리거될 때까지 호출 스레드를 차단합니다.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.

### <a name="external-control"></a>외부 제어

호스트 수명에 대한 직접 제어는 외부에서 호출할 수 있는 메서드를 사용하여 달성할 수 있습니다.

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 앱은 호스트를 구성 및 실행합니다. 호스트는 앱 시작 및 수명 관리를 담당합니다.

이 문서에서는 HTTP 요청을 처리하지 않는 앱에 사용되는 ASP.NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 다룹니다.

일반 호스트의 목적은 웹 호스트 API에서 HTTP 파이프라인을 분리하여 호스트 시나리오의 더 광범위한 배열을 구현하는 것입니다. 일반 호스트에 기반을 둔 메시징, 백그라운드 작업 및 기타 HTTP 이외 워크로드는 구성, DI(종속성 주입) 및 로깅과 같은 교차 편집 기능에서 이점을 얻습니다.

일반 호스트는 ASP.NET Core 2.1의 새로운 기능이며 웹 호스팅 시나리오에 적합하지 않습니다. 웹 호스팅 시나리오의 경우 [웹 호스트](xref:fundamentals/host/web-host)를 사용하세요. 일반 호스트는 향후 릴리스에서 웹 호스트를 대체하고 HTTP 및 HTTP 이외 시나리오에서 기본 호스트 API 역할을 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

[Visual Studio Code](https://code.visualstudio.com/)에서 샘플 앱을 실행할 때는 *외부 또는 통합 터미널* 을 사용하세요. `internalConsole`에서는 샘플을 실행하지 마세요.

Visual Studio Code에서 콘솔을 설정하려면:

1. *.vscode/launch.json* 파일을 엽니다.
1. **.NET Core 시작(콘솔)** 구성에서 **콘솔** 항목을 찾습니다. 값을 `externalTerminal` 또는 `integratedTerminal` 중 하나로 설정합니다.

## <a name="introduction"></a>소개

일반 호스트 라이브러리는 <xref:Microsoft.Extensions.Hosting> 네임스페이스에서 사용할 수 있으며, [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 패키지에서 제공합니다. `Microsoft.Extensions.Hosting` 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 이상)에 포함되어 있습니다.

<xref:Microsoft.Extensions.Hosting.IHostedService>는 코드 실행 진입점입니다. 각 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현은 [ConfigureServices의 서비스 등록](#configureservices) 순서대로 실행됩니다. <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>는 호스트가 시작될 때 각 <xref:Microsoft.Extensions.Hosting.IHostedService>에서 호출되고, <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>는 호스트가 점진적으로 종료될 때 등록 순서의 역순으로 호출됩니다.

## <a name="set-up-a-host"></a>호스트 설정

<xref:Microsoft.Extensions.Hosting.IHostBuilder>는 라이브러리 및 앱이 호스트를 초기화, 빌드 및 실행하는 데 사용하는 주 구성 요소입니다.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>옵션

<xref:Microsoft.Extensions.Hosting.HostOptions>는 <xref:Microsoft.Extensions.Hosting.IHost>에 대한 옵션을 구성합니다.

### <a name="shutdown-timeout"></a>시스템 종료 시간 제한

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>은 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다. 기본값은 5초입니다.

`Program.Main`의 다음 옵션 구성은 기본 5초 시스템 종료 시간 제한을 20초로 늘립니다.

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a>기본 서비스

호스트 초기화 중에 다음 서비스가 등록됩니다.

* [환경](xref:fundamentals/environments)(<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* [구성](xref:fundamentals/configuration/index)(<xref:Microsoft.Extensions.Configuration.IConfiguration>)
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [옵션](xref:fundamentals/configuration/options)(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)
* [로깅](xref:fundamentals/logging/index)(<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)

## <a name="host-configuration"></a>호스트 구성

호스트 구성은 다음에 의해 만들어집니다.

* <xref:Microsoft.Extensions.Hosting.IHostBuilder>에서 확장 메서드를 호출하여 [콘텐츠 루트](#content-root) 및 [환경](#environment)을 설정합니다.
* <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>의 구성 공급자에서 구성을 읽습니다.

### <a name="extension-methods"></a>확장 메서드

### <a name="application-key-name"></a>애플리케이션 키(이름)

호스트를 생성하는 동안 호스트 구성에서 [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 속성을 설정합니다. 값을 명시적으로 설정하려면 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)를 사용합니다.

**키** : `applicationName`  
**형식** : `string`  
**기본값** : 앱의 진입점을 포함하는 어셈블리의 이름입니다.  
**설정 방법** : `HostBuilderContext.HostingEnvironment.ApplicationName`  
**환경 변수** : `<PREFIX_>APPLICATIONNAME`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))

### <a name="content-root"></a>콘텐츠 루트

이 설정은 호스트가 콘텐츠 파일을 검색하기 시작하는 지점을 결정합니다.

**키** : `contentRoot`  
**형식** : `string`  
**기본값** : 앱 어셈블리가 있는 폴더가 기본값으로 지정됩니다.  
**설정 방법** : `UseContentRoot`  
**환경 변수** : `<PREFIX_>CONTENTROOT`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))

경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

자세한 내용은 [기본 사항: 콘텐츠 루트](xref:fundamentals/index#content-root)를 참조하세요.

### <a name="environment"></a>환경

앱의 [환경](xref:fundamentals/environments)을 설정합니다.

**키** : `environment`  
**형식** : `string`  
**기본** : `Production`  
**설정 방법** : `UseEnvironment`  
**환경 변수** : `<PREFIX_>ENVIRONMENT`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))

환경은 어떠한 값으로도 설정할 수 있습니다. 프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다. 값은 대/소문자를 구분하지 않습니다.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>ConfigureHostConfiguration

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>은 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하여 호스트에 대한 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 만듭니다. 호스트 구성은 앱의 빌드 프로세스에 사용할 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>를 초기화하는 데 사용됩니다.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다. 호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.

기본적으로 공급자는 포함되지 않습니다. 다음을 포함하여 앱에 필요한 모든 구성 공급자를 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>에 명시적으로 지정해야 합니다.

* 구성 파일(예: *hostsettings.json* 파일에서).
* 환경 변수 구성.
* 명령줄 인수 구성.
* 기타 필수 구성 공급자.

호스트의 파일 구성은 `SetBasePath`를 사용하여 앱의 기본 경로를 지정한 후 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider) 중 하나를 호출하여 활성화됩니다. 샘플 앱은 JSON 파일인 *hostsettings.json* 을 사용하고 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>을 호출하여 파일의 호스트 구성 설정을 사용합니다.

[환경 변수 구성](xref:fundamentals/configuration/index#environment-variables-configuration-provider)을 추가하려면 호스트 작성기에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>를 호출합니다. `AddEnvironmentVariables`는 선택적 사용자 정의 접두사를 허용합니다. 샘플 앱은 `PREFIX_` 접두사를 사용합니다. 접두사는 환경 변수를 읽을 때 제거됩니다. 샘플 앱의 호스트가 구성되면 `PREFIX_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.

[Visual Studio](https://visualstudio.microsoft.com)를 사용하거나 `dotnet run`을 통해 앱을 실행할 때 개발하는 동안에 환경 변수는 *Properties/launchSettings.json* 파일에 설정될 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)에서 환경 변수는 개발하는 동안에 *.vscode/launch.json* 파일에 설정될 수 있습니다. 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.

[명령줄 구성](xref:fundamentals/configuration/index#command-line-configuration-provider)은 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>을 호출하여 추가됩니다. 명령줄 구성이 마지막으로 추가되어 명령줄 인수가 이전 구성 공급자가 제공한 구성을 재정의할 수 있습니다.

*hostsettings.json* :

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

[applicationName](#application-key-name) 및 [contentRoot](#content-root) 키를 사용하여 추가 구성을 제공할 수 있습니다.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 사용한 `HostBuilder` 구성 예:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

앱 구성은 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 구현에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>은 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하여 앱에 대한 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 만듭니다. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다. 앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>에 의해 생성된 구성은 다음 작업에 대한 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 및 <xref:Microsoft.Extensions.Hosting.IHost.Services*>에서 사용할 수 있습니다.

앱 구성은 [ConfigureHostConfiguration](#configurehostconfiguration)에서 제공하는 호스트 구성을 자동으로 수신합니다.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 사용한 앱 구성 예:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appsettings.json* :

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*appsettings.Development.json* :

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*appsettings.Production.json* :

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

출력 디렉터리로 설정 파일을 이동하려면, 설정 파일을 프로젝트 파일 내 [MSBuild 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items)으로 지정합니다. 샘플 앱은 다음 `<Content>` 항목과 함께 JSON 앱 설정 파일과 *hostsettings.json* 을 이동합니다.

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 및 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>와 같은 구성 확장 메서드에는 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) 및 [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)와 같은 추가 NuGet 패키지가 필요합니다. 앱에서 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 사용하는 경우가 아니면 이 패키지는 코어 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) 패키지에 추가로 프로젝트에 추가되어야 합니다. 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.

## <a name="configureservices"></a>ConfigureServices

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>는 앱의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 서비스를 추가합니다. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.

호스티드 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현하는 백그라운드 작업 논리가 있는 클래스입니다. 자세한 내용은 <xref:fundamentals/host/hosted-services>를 참조하세요.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)은 `AddHostedService` 확장 메서드를 사용하여 수명 이벤트, `LifetimeEventsHostedService` 및 시간 제한 백그라운드 작업에 대한 서비스 `TimedHostedService`를 앱에 추가합니다.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>ConfigureLogging

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>에는 제공된 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>를 구성하는 대리자가 추가됩니다. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>을 호출하여 종료 프로세스를 시작합니다. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>은 [RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다. `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 수명 구현으로 미리 등록됩니다. 등록된 마지막 수명이 사용됩니다.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>컨테이너 구성

호스트는 다른 컨테이너 연결을 지원하기 위해 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>를 사용할 수 있습니다. 팩터리 제공은 DI 컨테이너 등록의 일부가 아니지만 구체적 DI 컨테이너를 만드는 데 사용되는 내장 호스트입니다. [UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*)는 앱의 서비스 공급자를 만드는 데 사용되는 기본 팩터리를 재정의합니다.

사용자 지정 컨테이너 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 메서드로 관리됩니다. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>는 기본 호스트 API 위에 컨테이너를 구성하기 위한 강력한 형식의 환경입니다. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.

앱에 대한 서비스 컨테이너를 만듭니다.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

서비스 컨테이너 팩터리를 제공합니다.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

팩터리를 사용하고 앱에 대 한 사용자 지정 서비스 컨테이너를 구성 합니다.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>확장성

<xref:Microsoft.Extensions.Hosting.IHostBuilder>에서 확장 메서드를 사용하여 호스트 확장성이 수행됩니다. 다음 예제는 <xref:fundamentals/host/hosted-services>에 설명된 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 예제를 사용하여 확장 메서드가 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 구현을 확장하는 방법을 보여줍니다.

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

앱은 `UseHostedService` 확장 메서드를 설정하여 `T`에서 전달되는 호스티드 서비스를 등록합니다.

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a>호스트 관리

<xref:Microsoft.Extensions.Hosting.IHost> 구현은 서비스 컨테이너에 등록된 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현의 시작 및 중지를 담당합니다.

### <a name="run"></a>Run

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔을 지원하고 호스트를 빌드 및 시작하며 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a>Start 및 StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a>StartAsync 및 StopAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>가 서버를 시작합니다.

<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>가 서버를 중지합니다.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`(<kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM 수신 대기)과 같이 <xref:Microsoft.Extensions.Hosting.IHostLifetime>을 통해 트리거됩니다. <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a>외부 제어

외부에서 호출할 수 있는 메서드를 사용하여 호스트의 외부 제어를 구현할 수 있습니다.

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다. 이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment 인터페이스

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>는 앱의 호스팅 환경에 대한 정보를 제공합니다. 해당 속성 및 확장 메서드를 사용하기 위해 [생성자 주입](xref:fundamentals/dependency-injection)을 사용하여 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>를 가져옵니다.

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

자세한 내용은 <xref:fundamentals/environments>를 참조하세요.

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime 인터페이스

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime>은 점진적인 종료 요청을 비롯한 사후 시작 및 종료 작업을 고려합니다. 인터페이스에서 세 가지 속성은 취소 토큰으로, 시작 및 종료 이벤트를 정의하는 <xref:System.Action> 메서드를 등록하는 데 사용됩니다.

| 취소 토큰 | 트리거되는 경우: |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | 호스트가 완벽하게 시작되었습니다. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | 호스트가 정상적으로 종료되었습니다. 모든 요청이 처리되어야 합니다. 종료는 이 이벤트가 완료될 때까지 차단합니다. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | 호스트가 정상적으로 종료되고 있습니다. 요청은 계속 처리할 수 있습니다. 종료는 이 이벤트가 완료될 때까지 차단합니다. |

클래스에 대한 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 서비스 생성자 주입을 수행합니다. [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)은 `LifetimeEventsHostedService` 클래스(<xref:Microsoft.Extensions.Hosting.IHostedService> 구현)에 대한 생성자 주입을 사용하여 이벤트를 등록합니다.

*LifetimeEventsHostedService.cs* :

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>은 앱의 종료를 요청합니다. 다음 클래스에서는 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>을 사용하여 해당 클래스의 `Shutdown` 메서드를 호출하는 경우 앱을 정상 종료합니다.

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/host/hosted-services>
