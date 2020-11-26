---
title: ASP.NET Core 웹 호스트
author: rick-anderson
description: 앱 시작 및 수명 관리를 담당하는 ASP.NET Core의 웹 호스트에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: 904b57f95cbc48a8177174dc9be770e8a6abf146
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035881"
---
# <a name="aspnet-core-web-host"></a>ASP.NET Core 웹 호스트

ASP.NET Core 앱은 *호스트* 를 구성 및 실행합니다. 호스트는 앱 시작 및 수명 관리를 담당합니다. 최소한으로 호스트는 서버 및 요청 처리 파이프라인을 구성합니다. 호스트는 로깅, 종속성 주입 및 구성을 설정할 수도 있습니다.

::: moniker range=">= aspnetcore-3.0"

이 문서에서는 이전 버전과의 호환성을 위해서만 사용 가능한 상태를 유지하는 웹 호스트에 설명합니다. 모든 앱 유형에 [일반 호스트](xref:fundamentals/host/generic-host)를 사용하는 것이 좋습니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 문서에서는 웹앱 호스트를 위한 웹 호스트에 대해 다룹니다. 다른 종류의 앱인 경우 [일반 호스트](xref:fundamentals/host/generic-host)를 사용합니다.

::: moniker-end

## <a name="set-up-a-host"></a>호스트 설정

[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)의 인스턴스를 사용하여 호스트를 만듭니다. 이는 일반적으로 앱의 진입점인 `Main` 메서드에서 수행됩니다.

프로젝트 템플릿에서 `Main`은 *Program.cs* 에 있습니다. 일반적인 앱은 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)를 호출하여 호스트 설정을 시작합니다.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

`CreateDefaultBuilder`를 호출하는 코드는 `CreateWebHostBuilder`라는 메서드에 포함되어, 빌더 개체에서 `Run`을 호출하는 `Main`의 코드와 분리됩니다. 이 분리는 [Entity Framework Core 도구](/ef/core/miscellaneous/cli/)를 사용하는 경우 필요합니다. 이 도구는 앱을 실행하지 않고 호스트를 구성하기 위해 디자인 타임에 호출할 수 있는 `CreateWebHostBuilder` 메서드를 찾으려 합니다. `IDesignTimeDbContextFactory`를 구현하는 방법도 있습니다. 자세한 내용은 [디자인 타임 DbContext 만들기](/ef/core/miscellaneous/cli/dbcontext-creation)를 참조하세요.

`CreateDefaultBuilder`는 다음 작업을 수행합니다.

* 앱의 호스팅 구성 공급자를 사용하여 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 구성합니다. Kestrel 서버의 기본 옵션은 <xref:fundamentals/servers/kestrel#kestrel-options>을 참조하세요.
* [콘텐츠 루트](xref:fundamentals/index#content-root)를 [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)에서 반환된 경로로 설정합니다.
* 다음에서 [호스트 구성](#host-configuration-values)을 로드합니다.
  * 접두사가 `ASPNETCORE_`인 환경 변수(예: `ASPNETCORE_ENVIRONMENT`).
  * 명령줄 인수.
* 다음 순서대로 앱 구성을 로드합니다.
  * *appsettings.json*.
  * *appsettings.{Environment}.json*.
  * 앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [사용자 암호](xref:security/app-secrets)입니다.
  * 환경 변수.
  * 명령줄 인수.
* 콘솔 및 디버그 출력에 대한 [로깅](xref:fundamentals/logging/index)을 구성합니다. 로깅은 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일의 로깅 구성 섹션에 지정된 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 규칙을 포함합니다.
* [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 사용하여 IIS에서 실행하는 경우 `CreateDefaultBuilder`는 앱의 기본 주소와 포트를 구성하는 [IIS 통합](xref:host-and-deploy/iis/index)을 활성화합니다. 또한 IIS 통합은 [시작 오류를 캡처](#capture-startup-errors)하도록 앱을 구성합니다. IIS 기본 옵션은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.
* 앱의 환경이 Development인 경우 [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes)를 `true`로 설정합니다. 자세한 내용은 [범위 유효성 검사](#scope-validation)를 참조하세요.

`CreateDefaultBuilder`에 의해 정의된 구성은 [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), 기타 메서드 및 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)의 확장 메서드로 재정의되고 확대될 수 있습니다. 몇 가지 예제는 다음과 같습니다.

* [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration)은 앱에 대한 추가 `IConfiguration`을 지정하는 데 사용됩니다. 다음 `ConfigureAppConfiguration` 호출은 *appsettings.xml* 파일에 앱 구성을 포함하는 대리자를 추가합니다. `ConfigureAppConfiguration`이 여러 번 호출될 수 있습니다. 이 구성은 호스트(예: 서버 URL 또는 환경)에 적용되지 않습니다. [호스트 구성 값](#host-configuration-values) 섹션을 참조하세요.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* 다음 `ConfigureLogging` 호출은 최소 로깅 수준([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel))을 [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel)으로 구성하는 대리자를 추가합니다. 이 설정은 `CreateDefaultBuilder`에 의해 구성된 *appsettings.Development.json*(`LogLevel.Debug`) 및 *appsettings.Production.json*(`LogLevel.Error`)의 설정을 재정의합니다. `ConfigureLogging`이 여러 번 호출될 수 있습니다.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* `ConfigureKestrel`에 대한 다음 호출은 Kestrel이 `CreateDefaultBuilder`에 의해 구성될 때 설정된 30,000,000바이트의 기본 [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)를 재정의합니다.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel)에 대한 다음 호출은 Kestrel이 `CreateDefaultBuilder`에 의해 구성될 때 설정된 30,000,000바이트의 기본 [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)를 재정의합니다.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

[콘텐츠 루트](xref:fundamentals/index#content-root)는 호스트가 MVC 뷰 파일과 같은 콘텐츠 파일을 검색하는 위치를 결정합니다. 앱이 프로젝트의 루트 폴더에서 시작되면 프로젝트의 루트 폴더가 콘텐츠 루트로 사용됩니다. 이것이 [Visual Studio](https://visualstudio.microsoft.com) 및 [dotnet 새 템플릿](/dotnet/core/tools/dotnet-new)에서 사용되는 기본값입니다.

앱 구성에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.

> [!NOTE]
> ASP.NET Core 2.x에서는 정적 `CreateDefaultBuilder` 메서드 사용에 대한 대안으로 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)에서 호스트를 만들 수 있도록 지원합니다.

호스트를 설정할 때 [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) 및 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) 메서드가 제공됩니다. `Startup` 클래스가 지정된 경우 `Configure` 메서드를 정의해야 합니다. 자세한 내용은 <xref:fundamentals/startup>를 참조하세요. `ConfigureServices`에 대한 여러 호출은 서로 추가합니다. `WebHostBuilder`에서 `Configure` 또는 `UseStartup`에 대한 여러 호출은 이전 설정을 대체합니다.

## <a name="host-configuration-values"></a>호스트 구성 값

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)는 호스트 구성 값을 설정하기 위해 다음 방법을 사용합니다.

* `ASPNETCORE_{configurationKey}` 형식의 환경 변수를 포함하는 호스트 빌더 구성. 예: `ASPNETCORE_ENVIRONMENT`.
* [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) 및 [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) 같은 확장([구성 재정의](#override-configuration) 섹션을 참조하세요).
* [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) 및 연결된 키. `UseSetting`을 사용하여 값을 설정할 때 값은 형식에 관계 없이 문자열로 설정됩니다.

호스트는 마지막에 값을 설정한 옵션을 사용합니다. 자세한 내용은 다음 섹션의 [구성 재정의](#override-configuration)를 참조하세요.

### <a name="application-key-name"></a>애플리케이션 키(이름)

::: moniker range=">= aspnetcore-3.0"

[UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) 또는 [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure)가 호스트 생성 중에 호출되는 경우 `IWebHostEnvironment.ApplicationName` 속성이 자동으로 설정됩니다. 해당 값은 앱의 진입점을 포함하는 어셈블리의 이름으로 설정됩니다. 값을 명시적으로 설정하려면 [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)를 사용합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) 또는 [구성](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure)이 호스트 생성 중에 호출되는 경우 [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) 속성이 자동으로 설정됩니다. 해당 값은 앱의 진입점을 포함하는 어셈블리의 이름으로 설정됩니다. 값을 명시적으로 설정하려면 [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)를 사용합니다.

::: moniker-end

**키**: applicationName  
**형식**: *string*  
**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.  
**설정 방법**: `UseSetting`  
**환경 변수**: `ASPNETCORE_APPLICATIONNAME`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a>시작 오류 캡처

이 설정은 시작 오류의 캡처를 제어합니다.

**키**: captureStartupErrors  
**형식**: *bool*(`true` 또는 `1`)  
**기본값**: 기본값이 `true`인 IIS 뒤에 있는 Kestrel로 앱이 실행하지 않는 한 기본값은 `false`로 지정됩니다.  
**설정 방법**: `CaptureStartupErrors`  
**환경 변수**: `ASPNETCORE_CAPTURESTARTUPERRORS`

`false`인 경우 시작 시 오류가 발생하면 호스트가 종료됩니다. `true`이면 호스트가 시작 시 예외를 캡처하고 서버 시작을 시도합니다.

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a>콘텐츠 루트

이 설정은 ASP.NET Core가 콘텐츠 파일을 검색하기 시작하는 지점을 결정합니다.

**키**: contentRoot  
**형식**: *string*  
**기본값**: 앱 어셈블리가 있는 폴더가 기본값으로 지정됩니다.  
**설정 방법**: `UseContentRoot`  
**환경 변수**: `ASPNETCORE_CONTENTROOT`

콘텐츠 루트는 또한 [웹 루트](xref:fundamentals/index#web-root)에 대한 기본 경로로 사용됩니다. 콘텐츠 루트 경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

자세한 내용은 다음을 참조하세요.

* [기본 사항: 콘텐츠 루트](xref:fundamentals/index#content-root)
* [웹 루트](#web-root)

### <a name="detailed-errors"></a>자세한 오류

자세한 오류를 캡처해야 하는지를 결정합니다.

**키**: detailedErrors  
**형식**: *bool*(`true` 또는 `1`)  
**기본값**: false  
**설정 방법**: `UseSetting`  
**환경 변수**: `ASPNETCORE_DETAILEDERRORS`

사용하는 경우(또는 <a href="#environment">환경</a>이 `Development`로 설정된 경우) 앱은 자세한 예외를 캡처합니다.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a>환경

앱의 환경을 설정합니다.

**키**: environment  
**형식**: *string*  
**기본값**: Production  
**설정 방법**: `UseEnvironment`  
**환경 변수**: `ASPNETCORE_ENVIRONMENT`

환경은 어떠한 값으로도 설정할 수 있습니다. 프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다. 값은 대/소문자를 구분하지 않습니다. 기본적으로 *환경* 은 `ASPNETCORE_ENVIRONMENT` 환경 변수에서 읽습니다. [Visual Studio](https://visualstudio.microsoft.com)를 사용하는 경우 환경 변수는 *launchSettings.json* 파일에서 설정할 수 있습니다. 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a>호스팅 시작 어셈블리

앱의 호스팅 시작 어셈블리를 설정합니다.

**키**: hostingStartupAssemblies  
**형식**: *string*  
**기본값**: 빈 문자열  
**설정 방법**: `UseSetting`  
**환경 변수**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`

시작 시 로드할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.

구성 값의 기본값이 빈 문자열이지만, 호스팅 시작 어셈블리는 항상 앱의 어셈블리를 포함합니다. 호스팅 시작 어셈블리가 제공되는 경우, 시작 시 앱이 일반적인 서비스를 빌드할 때 로드를 위해 앱의 어셈블리에 추가됩니다.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a>HTTPS 포트

HTTPS 리디렉션 포트를 설정합니다. [HTTPS 적용](xref:security/enforcing-ssl)에 사용됩니다.

**키**: https_port **형식**: *문자열*
**기본값**: 기본값은 설정되지 않습니다.
**설정 방법**: `UseSetting`
**환경 변수**: `ASPNETCORE_HTTPS_PORT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a>호스팅 시작 제외 어셈블리

시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.

**키**: hostingStartupExcludeAssemblies  
**형식**: *string*  
**기본값**: 빈 문자열  
**설정 방법**: `UseSetting`  
**환경 변수**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a>호스팅 URL 선호

호스트가 `IServer` 구현으로 구성된 URL 대신에 `WebHostBuilder`로 구성된 URL에서 수신 대기할지 여부를 나타냅니다.

**키**: preferHostingUrls  
**형식**: *bool*(`true` 또는 `1`)  
**기본값**: true  
**설정 방법**: `PreferHostingUrls`  
**환경 변수**: `ASPNETCORE_PREFERHOSTINGURLS`

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a>호스팅 시작 방지

앱의 어셈블리에 의해 구성된 호스팅 시작 어셈블리를 포함한 호스팅 시작 어셈블리의 자동 로딩을 방지합니다. 자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.

**키**: preventHostingStartup  
**형식**: *bool*(`true` 또는 `1`)  
**기본값**: false  
**설정 방법**: `UseSetting`  
**환경 변수**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a>서버 URL

서버에서 요청을 수신해야 하는 포트와 프로토콜이 있는 IP 주소 또는 호스트 주소를 나타냅니다.

**키**: urls  
**형식**: *string*  
**기본**: http://localhost:5000  
**설정 방법**: `UseUrls`  
**환경 변수**: `ASPNETCORE_URLS`

서버가 응답해야 하는 세미콜론으로 구분된(;) URL 접두사의 목록으로 설정합니다. 예: `http://localhost:123`. “\*”를 사용하여 서버가 지정된 포트 및 프로토콜을 사용하는 IP 주소 또는 호스트 이름에서 요청을 수신해야 함을 나타냅니다(예: `http://*:5000`). 프로토콜(`http://` 또는 `https://`)은 각 URL에 포함되어 있어야 합니다. 지원되는 형식은 서버마다 다릅니다.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

Kestrel에는 자체 엔드포인트 구성 API가 있습니다. 자세한 내용은 <xref:fundamentals/servers/kestrel#endpoint-configuration>를 참조하세요.

### <a name="shutdown-timeout"></a>시스템 종료 제한 시간

웹 호스트가 종료될 때까지 기다리는 시간을 지정합니다.

**키**: shutdownTimeoutSeconds  
**형식**: *int*  
**기본값**: 5  
**설정 방법**: `UseShutdownTimeout`  
**환경 변수**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`

키가 `UseSetting`을 사용할 때는 *int* 를 받지만(예: `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`) [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) 확장 메서드는 [TimeSpan](/dotnet/api/system.timespan)을 사용합니다.

제한 시간 기간 동안 호스팅은:

* [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping)을 트리거합니다.
* 중지하지 못한 서비스에 대한 모든 오류를 기록하면서 호스팅된 서비스 중지를 시도합니다.

모든 호스팅된 서비스가 중지하기 전에 시간 제한 기간이 만료되면 앱이 종료될 때 모든 활성화된 나머지 서비스가 중지됩니다. 처리를 완료하지 않은 경우에도 서비스가 중지됩니다. 서비스를 중지하는 데 시간이 더 필요한 경우 시간 제한을 늘립니다.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a>시작 어셈블리

`Startup` 클래스를 검색할 어셈블리를 결정합니다.

**키**: startupAssembly  
**형식**: *string*  
**기본값**: 앱의 어셈블리  
**설정 방법**: `UseStartup`  
**환경 변수**: `ASPNETCORE_STARTUPASSEMBLY`

이름(`string`) 또는 형식(`TStartup`)으로 어셈블리를 참조할 수 있습니다. `UseStartup` 메서드가 여러 개 호출된 경우 마지막 메서드가 우선 적용됩니다.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a>웹 루트

앱의 정적 자산에 대한 상대 경로를 설정합니다.

**키**: webroot  
**형식**: *string*  
**기본값**: 기본값은 `wwwroot`입니다. *{content root}/wwwroot* 경로가 존재해야 합니다. 경로가 존재하지 않으면 no-op 파일 공급자가 사용됩니다.  
**설정 방법**: `UseWebRoot`  
**환경 변수**: `ASPNETCORE_WEBROOT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

자세한 내용은 다음을 참조하세요.

* [기본 사항: 웹 루트](xref:fundamentals/index#web-root)
* [콘텐츠 루트](#content-root)

## <a name="override-configuration"></a>구성 재정의

[구성](xref:fundamentals/configuration/index)을 사용하여 웹 호스트를 구성합니다. 다음 예제에서는 호스트 구성이 필요에 따라 *hostsettings.json* 파일에 지정됩니다. *hostsettings.json* 파일에서 로드된 모든 구성은 명령줄 인수에 의해 재정의될 수도 있습니다. 빌드된 구성(`config`에 있음)은 [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration)을 통해 호스트를 구성하는 데 사용됩니다. `IWebHostBuilder` 구성이 앱의 구성에 추가되지만 반대의 경우에는 true가 아닙니다. &mdash;`ConfigureAppConfiguration`은 `IWebHostBuilder` 구성에 영향을 주지 않습니다.

`UseUrls`에 의해 제공되는 구성을 먼저 *hostsettings.json* 의 구성으로 재정의하고, 두 번째로 명령줄 인수의 구성으로 재정의합니다.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

*hostsettings.json*:

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration)은 제공된 `IConfiguration`의 키만 호스트 작성기 구성에 복사합니다. 따라서 JSON, INI 및 XML 설정 파일에 대해 `reloadOnChange: true`를 설정해도 영향을 미치지 않습니다.

특정 URL에서 실행하는 호스트를 지정하려면 [dotnet run](/dotnet/core/tools/dotnet-run) 실행 시 원하는 값을 명령 프롬프트에서 전달할 수 있습니다. 명령줄 인수는 *hostsettings.json* 파일의 `urls` 값을 재정의하고, 서버는 포트 8080에서 수신합니다.

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a>호스트 관리

**Run**

`Run` 메서드는 웹앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.

```csharp
host.Run();
```

**Start**

해당 `Start` 메서드를 호출하여 비차단 방식으로 호스트를 실행합니다.

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

URL 목록이 `Start` 메서드에 전달되면 지정된 URL에서 수신합니다.

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

앱은 정적 편의 메서드를 사용하여 `CreateDefaultBuilder`의 미리 구성된 기본 값을 사용하는 새 호스트를 초기화하고 시작합니다. 이러한 메서드는 콘솔 출력 없이 서버를 시작하고 [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown)으로 중단(Ctrl-C/SIGINT 또는 SIGTERM)될 때까지 대기합니다.

**Start(RequestDelegate app)**

`RequestDelegate`로 시작합니다.

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

`http://localhost:5000`에 대한 브라우저에서 요청을 수행하여 “Hello World!” 응답을 수신합니다. `WaitForShutdown`은 중단(Ctrl-C/SIGINT 또는 SIGTERM)이 발생할 때까지 차단합니다. 앱은 `Console.WriteLine` 메시지를 표시하고 종료하기 위한 키 입력을 대기합니다.

**Start(string url, RequestDelegate app)**

URL 및 `RequestDelegate`로 시작합니다.

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

앱이 `http://localhost:8080`에서 응답한다는 점을 제외하고 **Start(RequestDelegate app)** 와 동일한 결과가 생성됩니다.

**Start(Action\<IRouteBuilder> routeBuilder)**

`IRouteBuilder`의 인스턴스([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/))를 사용하여 라우팅 미들웨어를 사용합니다.

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

예제에서는 다음 브라우저 요청을 사용합니다.

| 요청                                    | 응답                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Hello, Martin!                           |
| `http://localhost:5000/buenosdias/Catrina` | Buenos dias, Catrina!                    |
| `http://localhost:5000/throw/ooops!`       | “ooops!” 문자열을 사용하여 예외를 throw합니다. |
| `http://localhost:5000/throw`              | “Uh oh!” 문자열을 사용하여 예외를 throw합니다. |
| `http://localhost:5000/Sante/Kevin`        | Sante, Kevin!                            |
| `http://localhost:5000`                    | Hello World!                             |

`WaitForShutdown`은 중단(Ctrl-C/SIGINT 또는 SIGTERM)이 발생할 때까지 차단합니다. 앱은 `Console.WriteLine` 메시지를 표시하고 keypress가 종료될 때까지 대기합니다.

**Start(string url, Action\<IRouteBuilder> routeBuilder)**

URL 및 `IRouteBuilder`의 인스턴스를 사용합니다.

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

앱이 `http://localhost:8080`에서 응답한다는 점을 제외하고 **Start(Action\<IRouteBuilder> routeBuilder)** 와 동일한 결과가 생성됩니다.

**StartWith(Action\<IApplicationBuilder> app)**

대리자를 제공하여 `IApplicationBuilder`를 구성합니다.

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

`http://localhost:5000`에 대한 브라우저에서 요청을 수행하여 “Hello World!” 응답을 수신합니다. `WaitForShutdown`은 중단(Ctrl-C/SIGINT 또는 SIGTERM)이 발생할 때까지 차단합니다. 앱은 `Console.WriteLine` 메시지를 표시하고 종료하기 위한 키 입력을 대기합니다.

**StartWith(string url, Action\<IApplicationBuilder> app)**

URL 및 대리자를 제공하여 `IApplicationBuilder`를 구성합니다.

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

앱이 `http://localhost:8080`에서 응답한다는 점을 제외하고 **StartWith(Action\<IApplicationBuilder> app)** 와 동일한 결과가 생성됩니다.

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a>IWebHostEnvironment 인터페이스

`IWebHostEnvironment` 인터페이스는 앱의 웹 호스팅 환경에 대한 정보를 제공합니다. 해당 속성 및 확장 메서드를 사용하기 위해 [생성자 주입](xref:fundamentals/dependency-injection)을 사용하여 `IWebHostEnvironment`를 가져옵니다.

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

[규칙 기반 접근 방식](xref:fundamentals/environments#environment-based-startup-class-and-methods)은 시작할 때 환경에 따라 앱을 구성하는 데 사용할 수 있습니다. 또는 `ConfigureServices`에서 사용할 수 있도록 `IWebHostEnvironment`를 `Startup` 생성자에 주입합니다.

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> `IsDevelopment` 확장 메서드 외에 `IWebHostEnvironment`는 `IsStaging`, `IsProduction` 및 `IsEnvironment(string environmentName)` 메서드를 제공합니다. 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.

또한 `IWebHostEnvironment` 서비스를 파이프라인 처리를 설정하기 위한 `Configure` 메서드에 직접 주입할 수 있습니다.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

사용자 지정 [미들웨어](xref:fundamentals/middleware/write)를 만들 때 `IWebHostEnvironment`를 `Invoke` 메서드에 주입할 수 있습니다.

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment 인터페이스

[IHostingEnvironment 인터페이스](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment)는 앱의 웹 호스팅 환경에 대한 정보를 제공합니다. 해당 속성 및 확장 메서드를 사용하기 위해 [생성자 주입](xref:fundamentals/dependency-injection)을 사용하여 `IHostingEnvironment`를 가져옵니다.

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

[규칙 기반 접근 방식](xref:fundamentals/environments#environment-based-startup-class-and-methods)은 시작할 때 환경에 따라 앱을 구성하는 데 사용할 수 있습니다. 또는 `ConfigureServices`에서 사용할 수 있도록 `IHostingEnvironment`를 `Startup` 생성자에 주입합니다.

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> `IsDevelopment` 확장 메서드 외에 `IHostingEnvironment`는 `IsStaging`, `IsProduction` 및 `IsEnvironment(string environmentName)` 메서드를 제공합니다. 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.

또한 `IHostingEnvironment` 서비스를 파이프라인 처리를 설정하기 위한 `Configure` 메서드에 직접 주입할 수 있습니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

사용자 지정 [미들웨어](xref:fundamentals/middleware/write)를 만들 때 `IHostingEnvironment`를 `Invoke` 메서드에 주입할 수 있습니다.

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a>IHostApplicationLifetime 인터페이스

`IHostApplicationLifetime`은 시작 후 및 종료 작업을 허용합니다. 인터페이스에서 세 가지 속성은 취소 토큰으로, 시작 및 종료 이벤트를 정의하는 `Action` 메서드를 등록하는 데 사용됩니다.

| 취소 토큰    | 트리거되는 경우: |
| --------------------- | --------------------- |
| `ApplicationStarted`  | 호스트가 완벽하게 시작되었습니다. |
| `ApplicationStopped`  | 호스트가 정상적으로 종료되었습니다. 모든 요청이 처리되어야 합니다. 종료는 이 이벤트가 완료될 때까지 차단합니다. |
| `ApplicationStopping` | 호스트가 정상적으로 종료되고 있습니다. 요청은 계속 처리할 수 있습니다. 종료는 이 이벤트가 완료될 때까지 차단합니다. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

`StopApplication`은 앱의 종료를 요청합니다. 다음 클래스에서는 `StopApplication`을 사용하여 해당 클래스의 `Shutdown` 메서드를 호출하는 경우 앱을 정상 종료합니다.

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime 인터페이스

[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime)은 사후 시작 및 종료 작업을 고려합니다. 인터페이스에서 세 가지 속성은 취소 토큰으로, 시작 및 종료 이벤트를 정의하는 `Action` 메서드를 등록하는 데 사용됩니다.

| 취소 토큰    | 트리거되는 경우: |
| --------------------- | --------------------- |
| [ApplicationStarted](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | 호스트가 완벽하게 시작되었습니다. |
| [ApplicationStopped](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | 호스트가 정상적으로 종료되었습니다. 모든 요청이 처리되어야 합니다. 종료는 이 이벤트가 완료될 때까지 차단합니다. |
| [ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | 호스트가 정상적으로 종료되고 있습니다. 요청은 계속 처리할 수 있습니다. 종료는 이 이벤트가 완료될 때까지 차단합니다. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication)은 앱의 종료를 요청합니다. 다음 클래스에서는 `StopApplication`을 사용하여 해당 클래스의 `Shutdown` 메서드를 호출하는 경우 앱을 정상 종료합니다.

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

## <a name="scope-validation"></a>범위 유효성 검사

[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)는 앱의 환경이 개발인 경우 [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes)를 `true`로 설정합니다.

`ValidateScopes`이 `true`로 설정된 경우 기본 서비스 공급자는 다음을 확인하기 위해 검사를 수행합니다.

* 범위가 지정된 서비스는 직접 또는 간접적으로 루트 서비스 공급자에서 해결되지 않습니다.
* 범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.

루트 서비스 공급자는 [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider)를 호출할 때 만들어집니다. 루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.

범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다. 범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다. 서비스 범위의 유효성 검사는 `BuildServiceProvider`이 호출될 경우 이러한 상황을 알아챕니다.

프로덕션 환경을 포함하여 범위의 유효성을 검사하려면 호스트 작성기에서 [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions)을 [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider)로 구성합니다.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a>추가 자료

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
