---
title: ASP.NET Core에서 호스팅 시작 어셈블리 사용
author: rick-anderson
description: IHostingStartup 구현을 사용하여 외부 어셈블리에서 ASP.NET Core 앱을 강화하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: b39215a70f990afeb7d3fe0a62981113b154354e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588245"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>ASP.NET Core에서 호스팅 시작 어셈블리 사용

작성자: [Pavel Krymets](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup>(호스팅 시작) 구현에서는 외부 어셈블리에서 시작할 때 앱에 향상된 기능을 추가합니다. 예를 들어 외부 라이브러리는 호스팅 시작 구현을 사용하여 앱에 추가 구성 공급자 또는 서비스를 제공할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>HostingStartup 특성

[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성은 런타임에 활성화할 호스팅 시작 어셈블리의 현재 상태를 나타냅니다.

항목 어셈블리 또는 `Startup` 클래스가 포함된 어셈블리에서는 `HostingStartup` 특성을 자동으로 검색합니다. `HostingStartup` 특성을 검색할 어셈블리 목록은 [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)의 구성에서 런타임에 로드됩니다. 검색에서 제외할 어셈블리 목록은 [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)에서 로드됩니다.

다음 예제에서는 호스팅 시작 어셈블리의 네임스페이스가 `StartupEnhancement`입니다. 호스팅 시작 코드를 포함하는 클래스는 `StartupEnhancementHostingStartup`입니다.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

`HostingStartup` 특성은 일반적으로 호스팅 시작 어셈블리의 `IHostingStartup` 구현 클래스 파일에 있습니다.

## <a name="discover-loaded-hosting-startup-assemblies"></a>로드된 호스팅 시작 어셈블리 검색

로드된 호스팅 시작 어셈블리를 검색하려면 로깅을 사용하도록 설정하고 앱의 로그를 확인합니다. 어셈블리를 로드할 때 발생하는 오류가 기록됩니다. 로드된 호스팅 시작 어셈블리는 디버그 수준에서 기록되고 모든 오류가 기록됩니다.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>호스팅 시작 어셈블리의 자동 로드 사용 안 함

호스팅 시작 어셈블리의 자동 로딩을 사용하지 않으려면 다음 방법 중 하나를 사용합니다.

* 모든 호스팅 시작 어셈블리가 로드되지 않도록 하려면 다음 중 하나를 `true` 또는 `1`로 설정합니다.

  * 호스팅 시작 방지 호스트 구성 설정:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * `ASPNETCORE_PREVENTHOSTINGSTARTUP` 환경 변수.

* 특정 호스팅 시작 어셈블리가 로드되지 않도록 하려면 시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열로 다음 중 하나를 설정합니다.

  * 호스팅 시작 어셈블리 제외 호스트 구성 설정:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 환경 변수.

호스트 구성 설정과 환경 변수가 모두 설정되면 호스트 설정이 동작을 제어합니다.

호스트 설정 또는 환경 변수를 사용하여 호스팅 시작 어셈블리를 사용하지 않도록 설정하면 어셈블리가 전역적으로 해제되고 앱의 여러 특징을 해제할 수 있습니다.

## <a name="project"></a>프로젝트

다음 프로젝트 형식 중 하나를 사용하여 호스팅 시작을 만듭니다.

* [클래스 라이브러리](#class-library)
* [진입점 없는 콘솔 앱](#console-app-without-an-entry-point)

### <a name="class-library"></a>클래스 라이브러리

클래스 라이브러리에서 호스팅 시작 기능 향상을 제공할 수 있습니다. 라이브러리에는 `HostingStartup` 특성이 포함되어 있습니다.

[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 Razor 페이지 앱, *HostingStartupApp* 및 클래스 라이브러리, *HostingStartupLibrary* 가 포함되어 있습니다. 클래스 라이브러리:

* `IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다. `ServiceKeyInjection`은 메모리 내 구성 공급자([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*))를 사용하여 앱의 구성에 서비스 문자열 쌍을 추가합니다.
* 호스팅 시작의 네임스페이스 및 클래스를 식별하는 `HostingStartup` 특성을 포함합니다.

`ServiceKeyInjection` 클래스의 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>를 사용하여 향상된 기능을 앱에 추가합니다.

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

앱의 인덱스 페이지는 클래스 라이브러리의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 별도의 호스팅 시작인 *HostingStartupPackage* 를 제공하는 NuGet 패키지 프로젝트도 포함되어 있습니다. 패키지는 앞에서 설명한 클래스 라이브러리와 같은 특징이 있습니다. 패키지:

* `IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다. `ServiceKeyInjection`은 앱의 구성에 서비스 문자열 쌍을 추가합니다.
* `HostingStartup` 특성을 포함합니다.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

앱의 인덱스 페이지는 패키지의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>진입점 없는 콘솔 앱

*이 방법은 .NET Framework가 아닌 .NET Core 앱에서만 사용할 수 있습니다.*

활성화에 대한 컴파일 시간 참조가 필요하지 않은 동적 호스팅 시작 기능 향상은 `HostingStartup` 특성이 포함되는 진입점 없는 콘솔 앱에서 제공될 수 있습니다. 콘솔 앱을 게시하면 런타임 저장소에서 사용할 수 있는 호스팅 시작 어셈블리가 생성됩니다.

다음과 같은 이유로 이 프로세스에서는 진입점 없는 콘솔 앱이 사용됩니다.

* 호스팅 시작 어셈블리에서 호스팅 시작을 사용하려면 종속성 파일이 필요합니다. 종속성 파일은 라이브러리가 아닌 앱을 게시하여 생성된 실행 가능한 앱 자산입니다.
* 라이브러리를 [런타임 패키지 저장소](/dotnet/core/deploying/runtime-store)에 직접 추가할 수 없습니다. 이 저장소에는 공유 런타임을 대상으로 하는 실행 가능한 프로젝트가 필요합니다.

동적 호스팅 시작 만들기:

* 호스팅 시작 어셈블리는 다음과 같은 진입점 없는 콘솔 앱에서 만들어집니다.
  * `IHostingStartup` 구현이 포함되는 클래스를 포함합니다.
  * `IHostingStartup` 구현 클래스를 식별하는 [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성을 포함합니다.
* 콘솔 앱은 호스팅 시작의 종속성을 얻기 위해 게시됩니다. 콘솔 앱을 게시하면 사용되지 않는 종속성이 종속성 파일에서 트리밍됩니다.
* 종속성 파일은 호스팅 시작 어셈블리의 런타임 위치를 설정하도록 수정됩니다.
* 호스팅 시작 어셈블리 및 해당 종속성 파일은 런타임 패키지 저장소에 저장됩니다. 호스팅 시작 어셈블리 및 해당 종속성 파일을 검색하기 위해 한 쌍의 환경 변수에 나열됩니다.

콘솔 앱은 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 패키지를 참조합니다.

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성은 <xref:Microsoft.AspNetCore.Hosting.IWebHost>를 빌드할 때 로드 및 실행을 위해 `IHostingStartup`의 구현으로 클래스를 식별합니다. 다음 예제에서 네임스페이스는 `StartupEnhancement`이고 클래스는 `StartupEnhancementHostingStartup`입니다.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

클래스는 `IHostingStartup`을 구현합니다. 클래스의 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>를 사용하여 향상된 기능을 앱에 추가합니다. 호스팅 시작 어셈블리의 `IHostingStartup.Configure`는 사용자 코드로 `Startup.Configure` 전에 런타임에서 호출됩니다. 그러면 사용자 코드가 호스팅 시작 어셈블리에서 제공하는 모든 구성을 덮어쓸 수 있습니다.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

`IHostingStartup` 프로젝트를 빌드할 때 종속성 파일( *.deps.json*)은 어셈블리의 `runtime` 위치를 *bin* 폴더로 설정합니다.

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

파일의 일부만 표시됩니다. 이 예제의 어셈블리 이름은 `StartupEnhancement`입니다.

## <a name="configuration-provided-by-the-hosting-startup"></a>호스팅 시작으로 제공하는 구성

호스팅 시작의 구성을 우선할지, 앱의 구성을 우선할지에 따라 두 가지 방법으로 구성을 처리할 수 있습니다.

1. 앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행된 후 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>을 사용하여 앱에 구성을 제공합니다. 이 방법을 사용하면 호스팅 시작 구성이 앱의 구성보다 우선 순위가 높습니다.
1. 앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행되기 전에 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 사용하여 앱에 구성을 제공합니다. 이 방법을 사용하면 호스팅 시작으로 제공된 값보다 앱의 구성 값이 우선 순위가 높습니다.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>호스팅 시작 어셈블리 지정

클래스 라이브러리 또는 콘솔 앱 제공 호스팅 시작의 경우 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 호스팅 시작 어셈블리 이름을 지정합니다. 환경 변수는 세미콜론으로 구분된 어셈블리 목록입니다.

호스팅 시작 어셈블리만 `HostingStartup` 특성을 검사합니다. 샘플 앱(*HostingStartupApp*)의 경우 앞에서 설명한 호스팅 시작을 검색하기 위해 환경 변수가 다음 값으로 설정됩니다.

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

호스팅 시작 어셈블리는 호스팅 시작 어셈블리 호스트 구성 설정을 사용하여 설정할 수도 있습니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

여러 개의 호스팅 시작 어셈블이 있는 경우 어셈블리가 나열되는 순서대로 해당 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드가 실행됩니다.

## <a name="activation"></a>활성화

호스팅 시작 활성화 옵션은 다음과 같습니다.

* [런타임 저장소](#runtime-store): 활성화에는 활성화를 위한 컴파일 시간 참조가 필요하지 않습니다. 샘플 앱은 호스팅 시작 어셈블리 및 종속성 파일을 *배포* 폴더 안에 배치하여 다중 머신 환경에서 호스팅 시작의 배포를 용이하게 합니다. *배포* 폴더에는 호스팅 시작을 사용하도록 배포 시스템에 환경 변수를 만들거나 수정하는 PowerShell 스크립트도 포함되어 있습니다.
* 활성화에 필요한 컴파일 시간 참조
  * [NuGet 패키지](#nuget-package)
  * [프로젝트 bin 폴더](#project-bin-folder)

### <a name="runtime-store"></a>런타임 저장소

호스팅 시작 구현은 [런타임 저장소](/dotnet/core/deploying/runtime-store)에 배치됩니다. 향상된 앱에서는 어셈블리에 대한 컴파일 시간 참조가 필요하지 않습니다.

호스팅 시작이 빌드된 후에는 매니페스트 프로젝트 파일과 [dotnet store](/dotnet/core/tools/dotnet-store) 명령을 사용하여 런타임 저장소가 생성됩니다.

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

샘플 앱(*RuntimeStore* 프로젝트)에서 다음 명령이 사용됩니다.

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

런타임에서 런타임 저장소를 검색하기 위해 런타임 저장소의 위치가 `DOTNET_SHARED_STORE` 환경 변수에 추가됩니다.

**호스팅 시작의 종속성 파일 수정 및 배치**

향상된 기능에 대한 패키지 참조 없이 향상된 기능을 활성화하려면 `additionalDeps`를 사용하여 런타임에 추가 종속성을 지정합니다. `additionalDeps`를 사용하면 다음을 수행할 수 있습니다.

* 시작 시 앱의 고유한 *.deps.json* 파일과 병합하기 위해 추가 *.deps.json* 파일 집합을 제공하여 앱의 라이브러리 그래프를 확장합니다.
* 호스팅 시작 어셈블리를 검색 가능하고 로드 가능하게 만듭니다.

추가 종속성 파일을 생성하기 위해 권장되는 방법은 다음과 같습니다.

 1. 이전 섹션에서 참조한 런타임 저장소 매니페스트 파일에서 `dotnet publish`를 실행합니다.
 1. 결과 *.deps.json* 파일의 `runtime` 섹션 및 라이브러리에서 매니페스트 참조를 제거합니다.

예제 프로젝트에서 `store.manifest/1.0.0` 속성은 `targets` 및 `libraries` 섹션에서 제거됩니다.

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

*.deps.json* 파일을 다음 위치에 배치합니다.

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`: `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가되는 위치입니다.
* `{SHARED FRAMEWORK NAME}`: 이 추가 종속성 파일에 대한 공유 프레임워크가 필요합니다.
* `{SHARED FRAMEWORK VERSION}`: 최소 공유 프레임워크 버전입니다.
* `{ENHANCEMENT ASSEMBLY NAME}`: 향상된 기능의 어셈블리 이름입니다.

샘플 앱(*RuntimeStore* 프로젝트)에서 추가 종속성 파일은 다음 위치에 배치됩니다.

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

런타임에서 런타임 저장소 위치를 검색하기 위해 추가 종속성 파일 위치가 `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가됩니다.

샘플 앱( *RuntimeStore* 프로젝트)에서 런타임 저장소 빌드 및 추가 종속성 파일 생성은 [PowerShell](/powershell/scripting/powershell-scripting) 스크립트를 사용하여 수행됩니다.

다양한 운영 체제에 대한 환경 변수를 설정하는 방법의 예제는 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.

**배포**

다중 머신 환경에서 호스팅 시작의 배포를 용이하게 하기 위해 샘플 앱은 다음을 포함하는 게시된 출력에 *배포* 폴더를 만듭니다.

* 호스팅 시작 런타임 저장소.
* 호스팅 시작 종속성 파일.
* 호스팅 시작 활성화를 지원하기 위해 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` 및 `DOTNET_ADDITIONAL_DEPS` 를 만들거나 수정하는 PowerShell 스크립트입니다. 배포 시스템의 관리 PowerShell 명령 프롬프트에서 스크립트를 실행합니다.

### <a name="nuget-package"></a>NuGet 패키지

NuGet 패키지에서 호스팅 시작 기능 향상을 제공할 수 있습니다. 패키지에 `HostingStartup` 특성이 있습니다. 패키지에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.

* 향상된 앱의 프로젝트 파일은 앱의 프로젝트 파일(컴파일 시간 참조)에서 호스팅 시작을 위한 패키지 참조를 만듭니다. 이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다. 이 방식은 [nuget.org](https://www.nuget.org/)에 게시된 호스팅 시작 어셈블리 패키지에 적용됩니다.
* 호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).

NuGet 패키지 및 런타임 저장소에 대한 자세한 내용은 다음 항목을 참조하세요.

* [플랫폼 간 도구로 NuGet 패키지를 만드는 방법](/dotnet/core/deploying/creating-nuget-packages)
* [패키지 게시](/nuget/create-packages/publish-a-package)
* [런타임 패키지 저장소](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>프로젝트 bin 폴더

호스팅 시작 향상 기능은 향상된 앱의 *bin* 배포 어셈블리에 의해 제공될 수 있습니다. 어셈블리에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.

* 향상된 앱의 프로젝트 파일은 호스팅 시작에 대한 어셈블리 참조를 만듭니다(컴파일 시간 참조). 이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다. 이 방법은 배포 시나리오에서 호스팅 시작 어셈블리( *.dll* 파일)에 대한 컴파일 시간 참조를 수행하고 어셈블리를 다음 중 하나로 이동해야 할 때 적용됩니다.
  * 사용하는 프로젝트입니다.
  * 사용하는 프로젝트에서 액세스할 수 있는 위치입니다.
* 호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).
* .NET Framework를 대상으로 지정하는 경우 어셈블리는 기본 로드 컨텍스트에 로드될 수 있습니다. 즉, .NET Framework는 어셈블리가 다음 위치 중 하나에 있는 것을 의미합니다.
  * 애플리케이션 기본 경로: 애플리케이션의 실행 파일( *.exe*)이 있는 *bin* 폴더입니다.
  * GAC(전역 어셈블리 캐시): GAC에는 여러 .NET Framework 앱이 공유하는 어셈블리가 저장됩니다. 자세한 내용은 [방법: .NET Framework 설명서의 전역 어셈블리 캐시](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)에 어셈블리를 설치합니다.

## <a name="sample-code"></a>샘플 코드

[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)([다운로드 방법](xref:index#how-to-download-a-sample))는 호스팅 시작 구현 시나리오를 보여 줍니다.

* 두 개의 호스팅 시작 어셈블리(클래스 라이브러리)는 각각 한 쌍의 메모리 내 구성 키-값 쌍을 설정합니다.
  * NuGet 패키지(*HostingStartupPackage*)
  * 클래스 라이브러리(*HostingStartupLibrary*)
* 호스팅 시작은 런타임 저장소 배포 어셈블리에서 활성화됩니다(*StartupDiagnostics*). 어셈블리는 시작 시 다음과 같은 진단 정보를 제공하는 두 개의 미들웨어를 앱에 추가합니다.
  * 등록된 서비스
  * 주소(구성표, 호스트, 기본 경로, 경로, 쿼리 문자열)
  * 연결(원격 IP, 원격 포트, 로컬 IP, 로컬 포트, 클라이언트 인증서)
  * 요청 헤더
  * 환경 변수

샘플을 실행하려면:

**NuGet 패키지에서 활성화**

1. [dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용하여 *HostingStartupPackage* 패키지를 컴파일합니다.
1. *HostingStartupPackage* 패키지의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.
1. 앱을 컴파일하고 실행합니다. 패키지 참조가 향상된 앱(컴파일 시간 참조)에 있습니다. 앱의 프로젝트 파일에 있는 `<PropertyGroup>`에서는 패키지 프로젝트의 출력( *../HostingStartupPackage/bin/Debug*)을 패키지 원본으로 지정합니다. 이렇게 하면 [nuget.org](https://www.nuget.org/)에 패키지를 업로드하지 않고 패키지를 사용할 수 있습니다. 자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. 인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 패키지의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.

*HostingStartupPackage* 프로젝트를 변경하고 다시 컴파일하는 경우, 로컬 NuGet 패키지 캐시의 선택을 취소하여 *HostingStartupApp* 이 로컬 캐시에서 부실 패키지가 아닌 업데이트된 패키지를 수신하는지 확인합니다. 로컬 NuGet 캐시를 지우려면 다음 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 명령을 실행합니다.

```dotnetcli
dotnet nuget locals all --clear
```

**클래스 라이브러리에서 활성화**

1. [dotnet build](/dotnet/core/tools/dotnet-build) 명령을 사용하여 *HostingStartupLibrary* 클래스 라이브러리를 컴파일합니다.
1. *HostingStartupLibrary* 클래스 라이브러리의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.
1. *bin* - *HostingStartupLibrary.dll* 파일을 클래스 라이브러리의 컴파일된 출력에서 앱의 *bin/Debug* 폴더로 복사하여 클래스 라이브러리의 어셈블리를 앱에 배포합니다.
1. 앱을 컴파일하고 실행합니다. 앱의 프로젝트 파일에 있는 `<ItemGroup>`은 클래스 라이브러리의 어셈블리( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*)(컴파일 시간 참조)를 참조합니다. 자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. 인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 클래스 라이브러리의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.

**런타임 저장소 배포 어셈블리에서 활성화**

1. *StartupDiagnostics* 프로젝트는 [PowerShell](/powershell/scripting/powershell-scripting)을 사용하여 해당 *StartupDiagnostics.deps.json* 파일을 수정합니다. PowerShell은 Windows 7 SP1 및 Windows Server 2008 R2 SP1부터 Windows에서 기본적으로 설치됩니다. 다른 플랫폼에서 PowerShell을 가져오려면 [다양한 버전의 PowerShell 설치](/powershell/scripting/install/installing-powershell)를 참조하세요.
1. *RuntimeStore* 폴더에서 *build.ps1* 스크립트를 실행합니다. 스크립트는 다음을 수행합니다.
   * *obj\packages* 폴더에 `StartupDiagnostics` 패키지를 생성합니다.
   * *store* 폴더에서 `StartupDiagnostics`의 런타임 저장소를 생성합니다. 해당 스크립트에서 `dotnet store` 명령은 Windows에 배포된 호스팅 시작에서 `win7-x64` [RID(런타임 식별자)](/dotnet/core/rid-catalog)를 사용합니다. 다른 런타임에 호스팅 시작을 제공할 때 스크립트의 줄 37에서 올바른 RID로 대체합니다. `StartupDiagnostics`의 런타임 저장소는 나중에 어셈블리가 사용될 컴퓨터의 사용자 또는 시스템의 런타임 저장소로 이동됩니다. `StartupDiagnostics` 어셈블리의 사용자 런타임 저장소 설치 위치는 *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll* 입니다.
   * *additionalDeps* 폴더에 `StartupDiagnostics`의 `additionalDeps`를 생성합니다. 추가 종속성은 나중에 사용자 또는 시스템의 추가 종속성으로 이동됩니다. 사용자 `StartupDiagnostics` 추가 종속성 설치 위치는 *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json* 입니다.
   * *deployment* 폴더에 *deploy.ps1* 파일을 배치합니다.
1. *배포* 폴더에서 *deploy.ps1* 스크립트를 실행합니다. 스크립트는 다음을 수행합니다.
   * `StartupDiagnostics`를 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.
   * `DOTNET_ADDITIONAL_DEPS` 환경 변수의 호스팅 시작 종속성 경로(RuntimeStore 프로젝트의 *deployment* 폴더에 있음).
   * `DOTNET_SHARED_STORE` 환경 변수의 런타임 저장소 경로(RuntimeStore 프로젝트의 *deployment* 폴더에 있음).
1. 샘플 앱을 실행합니다.
1. `/services` 엔드포인트가 앱의 등록된 서비스를 확인하도록 요청합니다. `/diag` 엔드포인트가 진단 정보를 확인하도록 요청합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup>(호스팅 시작) 구현에서는 외부 어셈블리에서 시작할 때 앱에 향상된 기능을 추가합니다. 예를 들어 외부 라이브러리는 호스팅 시작 구현을 사용하여 앱에 추가 구성 공급자 또는 서비스를 제공할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>HostingStartup 특성

[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성은 런타임에 활성화할 호스팅 시작 어셈블리의 현재 상태를 나타냅니다.

항목 어셈블리 또는 `Startup` 클래스가 포함된 어셈블리에서는 `HostingStartup` 특성을 자동으로 검색합니다. `HostingStartup` 특성을 검색할 어셈블리 목록은 [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)의 구성에서 런타임에 로드됩니다. 검색에서 제외할 어셈블리 목록은 [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)에서 로드됩니다. 자세한 내용은 [웹 호스트: 호스팅 시작 어셈블리](xref:fundamentals/host/web-host#hosting-startup-assemblies) 및 [웹 호스트: 호스팅 시작 어셈블리 제외](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)를 참조하세요.

다음 예제에서는 호스팅 시작 어셈블리의 네임스페이스가 `StartupEnhancement`입니다. 호스팅 시작 코드를 포함하는 클래스는 `StartupEnhancementHostingStartup`입니다.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

`HostingStartup` 특성은 일반적으로 호스팅 시작 어셈블리의 `IHostingStartup` 구현 클래스 파일에 있습니다.

## <a name="discover-loaded-hosting-startup-assemblies"></a>로드된 호스팅 시작 어셈블리 검색

로드된 호스팅 시작 어셈블리를 검색하려면 로깅을 사용하도록 설정하고 앱의 로그를 확인합니다. 어셈블리를 로드할 때 발생하는 오류가 기록됩니다. 로드된 호스팅 시작 어셈블리는 디버그 수준에서 기록되고 모든 오류가 기록됩니다.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>호스팅 시작 어셈블리의 자동 로드 사용 안 함

호스팅 시작 어셈블리의 자동 로딩을 사용하지 않으려면 다음 방법 중 하나를 사용합니다.

* 모든 호스팅 시작 어셈블리가 로드되지 않도록 하려면 다음 중 하나를 `true` 또는 `1`로 설정합니다.
  * [호스팅 시작 방지](xref:fundamentals/host/web-host#prevent-hosting-startup) 호스트 구성 설정.
  * `ASPNETCORE_PREVENTHOSTINGSTARTUP` 환경 변수.
* 특정 호스팅 시작 어셈블리가 로드되지 않도록 하려면 시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열로 다음 중 하나를 설정합니다.
  * [호스팅 시작 어셈블리 제외](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) 호스트 구성 설정.
  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 환경 변수.

호스트 구성 설정과 환경 변수가 모두 설정되면 호스트 설정이 동작을 제어합니다.

호스트 설정 또는 환경 변수를 사용하여 호스팅 시작 어셈블리를 사용하지 않도록 설정하면 어셈블리가 전역적으로 해제되고 앱의 여러 특징을 해제할 수 있습니다.

## <a name="project"></a>프로젝트

다음 프로젝트 형식 중 하나를 사용하여 호스팅 시작을 만듭니다.

* [클래스 라이브러리](#class-library)
* [진입점 없는 콘솔 앱](#console-app-without-an-entry-point)

### <a name="class-library"></a>클래스 라이브러리

클래스 라이브러리에서 호스팅 시작 기능 향상을 제공할 수 있습니다. 라이브러리에는 `HostingStartup` 특성이 포함되어 있습니다.

[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 Razor 페이지 앱, *HostingStartupApp* 및 클래스 라이브러리, *HostingStartupLibrary* 가 포함되어 있습니다. 클래스 라이브러리:

* `IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다. `ServiceKeyInjection`은 메모리 내 구성 공급자([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*))를 사용하여 앱의 구성에 서비스 문자열 쌍을 추가합니다.
* 호스팅 시작의 네임스페이스 및 클래스를 식별하는 `HostingStartup` 특성을 포함합니다.

`ServiceKeyInjection` 클래스의 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>를 사용하여 향상된 기능을 앱에 추가합니다.

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

앱의 인덱스 페이지는 클래스 라이브러리의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 별도의 호스팅 시작인 *HostingStartupPackage* 를 제공하는 NuGet 패키지 프로젝트도 포함되어 있습니다. 패키지는 앞에서 설명한 클래스 라이브러리와 같은 특징이 있습니다. 패키지:

* `IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다. `ServiceKeyInjection`은 앱의 구성에 서비스 문자열 쌍을 추가합니다.
* `HostingStartup` 특성을 포함합니다.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

앱의 인덱스 페이지는 패키지의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>진입점 없는 콘솔 앱

*이 방법은 .NET Framework가 아닌 .NET Core 앱에서만 사용할 수 있습니다.*

활성화에 대한 컴파일 시간 참조가 필요하지 않은 동적 호스팅 시작 기능 향상은 `HostingStartup` 특성이 포함되는 진입점 없는 콘솔 앱에서 제공될 수 있습니다. 콘솔 앱을 게시하면 런타임 저장소에서 사용할 수 있는 호스팅 시작 어셈블리가 생성됩니다.

다음과 같은 이유로 이 프로세스에서는 진입점 없는 콘솔 앱이 사용됩니다.

* 호스팅 시작 어셈블리에서 호스팅 시작을 사용하려면 종속성 파일이 필요합니다. 종속성 파일은 라이브러리가 아닌 앱을 게시하여 생성된 실행 가능한 앱 자산입니다.
* 라이브러리를 [런타임 패키지 저장소](/dotnet/core/deploying/runtime-store)에 직접 추가할 수 없습니다. 이 저장소에는 공유 런타임을 대상으로 하는 실행 가능한 프로젝트가 필요합니다.

동적 호스팅 시작 만들기:

* 호스팅 시작 어셈블리는 다음과 같은 진입점 없는 콘솔 앱에서 만들어집니다.
  * `IHostingStartup` 구현이 포함되는 클래스를 포함합니다.
  * `IHostingStartup` 구현 클래스를 식별하는 [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성을 포함합니다.
* 콘솔 앱은 호스팅 시작의 종속성을 얻기 위해 게시됩니다. 콘솔 앱을 게시하면 사용되지 않는 종속성이 종속성 파일에서 트리밍됩니다.
* 종속성 파일은 호스팅 시작 어셈블리의 런타임 위치를 설정하도록 수정됩니다.
* 호스팅 시작 어셈블리 및 해당 종속성 파일은 런타임 패키지 저장소에 저장됩니다. 호스팅 시작 어셈블리 및 해당 종속성 파일을 검색하기 위해 한 쌍의 환경 변수에 나열됩니다.

콘솔 앱은 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 패키지를 참조합니다.

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성은 <xref:Microsoft.AspNetCore.Hosting.IWebHost>를 빌드할 때 로드 및 실행을 위해 `IHostingStartup`의 구현으로 클래스를 식별합니다. 다음 예제에서 네임스페이스는 `StartupEnhancement`이고 클래스는 `StartupEnhancementHostingStartup`입니다.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

클래스는 `IHostingStartup`을 구현합니다. 클래스의 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>를 사용하여 향상된 기능을 앱에 추가합니다. 호스팅 시작 어셈블리의 `IHostingStartup.Configure`는 사용자 코드로 `Startup.Configure` 전에 런타임에서 호출됩니다. 그러면 사용자 코드가 호스팅 시작 어셈블리에서 제공하는 모든 구성을 덮어쓸 수 있습니다.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

`IHostingStartup` 프로젝트를 빌드할 때 종속성 파일( *.deps.json*)은 어셈블리의 `runtime` 위치를 *bin* 폴더로 설정합니다.

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

파일의 일부만 표시됩니다. 이 예제의 어셈블리 이름은 `StartupEnhancement`입니다.

## <a name="configuration-provided-by-the-hosting-startup"></a>호스팅 시작으로 제공하는 구성

호스팅 시작의 구성을 우선할지, 앱의 구성을 우선할지에 따라 두 가지 방법으로 구성을 처리할 수 있습니다.

1. 앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행된 후 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>을 사용하여 앱에 구성을 제공합니다. 이 방법을 사용하면 호스팅 시작 구성이 앱의 구성보다 우선 순위가 높습니다.
1. 앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행되기 전에 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 사용하여 앱에 구성을 제공합니다. 이 방법을 사용하면 호스팅 시작으로 제공된 값보다 앱의 구성 값이 우선 순위가 높습니다.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>호스팅 시작 어셈블리 지정

클래스 라이브러리 또는 콘솔 앱 제공 호스팅 시작의 경우 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 호스팅 시작 어셈블리 이름을 지정합니다. 환경 변수는 세미콜론으로 구분된 어셈블리 목록입니다.

호스팅 시작 어셈블리만 `HostingStartup` 특성을 검사합니다. 샘플 앱(*HostingStartupApp*)의 경우 앞에서 설명한 호스팅 시작을 검색하기 위해 환경 변수가 다음 값으로 설정됩니다.

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

호스팅 시작 어셈블리는 [호스팅 시작 어셈블리](xref:fundamentals/host/web-host#hosting-startup-assemblies) 호스트 구성 설정을 사용하여 설정할 수도 있습니다.

여러 개의 호스팅 시작 어셈블이 있는 경우 어셈블리가 나열되는 순서대로 해당 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드가 실행됩니다.

## <a name="activation"></a>활성화

호스팅 시작 활성화 옵션은 다음과 같습니다.

* [런타임 저장소](#runtime-store): 활성화에는 활성화를 위한 컴파일 시간 참조가 필요하지 않습니다. 샘플 앱은 호스팅 시작 어셈블리 및 종속성 파일을 *배포* 폴더 안에 배치하여 다중 머신 환경에서 호스팅 시작의 배포를 용이하게 합니다. *배포* 폴더에는 호스팅 시작을 사용하도록 배포 시스템에 환경 변수를 만들거나 수정하는 PowerShell 스크립트도 포함되어 있습니다.
* 활성화에 필요한 컴파일 시간 참조
  * [NuGet 패키지](#nuget-package)
  * [프로젝트 bin 폴더](#project-bin-folder)

### <a name="runtime-store"></a>런타임 저장소

호스팅 시작 구현은 [런타임 저장소](/dotnet/core/deploying/runtime-store)에 배치됩니다. 향상된 앱에서는 어셈블리에 대한 컴파일 시간 참조가 필요하지 않습니다.

호스팅 시작이 빌드된 후에는 매니페스트 프로젝트 파일과 [dotnet store](/dotnet/core/tools/dotnet-store) 명령을 사용하여 런타임 저장소가 생성됩니다.

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

샘플 앱(*RuntimeStore* 프로젝트)에서 다음 명령이 사용됩니다.

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

런타임에서 런타임 저장소를 검색하기 위해 런타임 저장소의 위치가 `DOTNET_SHARED_STORE` 환경 변수에 추가됩니다.

**호스팅 시작의 종속성 파일 수정 및 배치**

향상된 기능에 대한 패키지 참조 없이 향상된 기능을 활성화하려면 `additionalDeps`를 사용하여 런타임에 추가 종속성을 지정합니다. `additionalDeps`를 사용하면 다음을 수행할 수 있습니다.

* 시작 시 앱의 고유한 *.deps.json* 파일과 병합하기 위해 추가 *.deps.json* 파일 집합을 제공하여 앱의 라이브러리 그래프를 확장합니다.
* 호스팅 시작 어셈블리를 검색 가능하고 로드 가능하게 만듭니다.

추가 종속성 파일을 생성하기 위해 권장되는 방법은 다음과 같습니다.

 1. 이전 섹션에서 참조한 런타임 저장소 매니페스트 파일에서 `dotnet publish`를 실행합니다.
 1. 결과 *.deps.json* 파일의 `runtime` 섹션 및 라이브러리에서 매니페스트 참조를 제거합니다.

예제 프로젝트에서 `store.manifest/1.0.0` 속성은 `targets` 및 `libraries` 섹션에서 제거됩니다.

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

*.deps.json* 파일을 다음 위치에 배치합니다.

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`: `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가되는 위치입니다.
* `{SHARED FRAMEWORK NAME}`: 이 추가 종속성 파일에 대한 공유 프레임워크가 필요합니다.
* `{SHARED FRAMEWORK VERSION}`: 최소 공유 프레임워크 버전입니다.
* `{ENHANCEMENT ASSEMBLY NAME}`: 향상된 기능의 어셈블리 이름입니다.

샘플 앱(*RuntimeStore* 프로젝트)에서 추가 종속성 파일은 다음 위치에 배치됩니다.

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

런타임에서 런타임 저장소 위치를 검색하기 위해 추가 종속성 파일 위치가 `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가됩니다.

샘플 앱( *RuntimeStore* 프로젝트)에서 런타임 저장소 빌드 및 추가 종속성 파일 생성은 [PowerShell](/powershell/scripting/powershell-scripting) 스크립트를 사용하여 수행됩니다.

다양한 운영 체제에 대한 환경 변수를 설정하는 방법의 예제는 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.

**배포**

다중 머신 환경에서 호스팅 시작의 배포를 용이하게 하기 위해 샘플 앱은 다음을 포함하는 게시된 출력에 *배포* 폴더를 만듭니다.

* 호스팅 시작 런타임 저장소.
* 호스팅 시작 종속성 파일.
* 호스팅 시작 활성화를 지원하기 위해 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` 및 `DOTNET_ADDITIONAL_DEPS` 를 만들거나 수정하는 PowerShell 스크립트입니다. 배포 시스템의 관리 PowerShell 명령 프롬프트에서 스크립트를 실행합니다.

### <a name="nuget-package"></a>NuGet 패키지

NuGet 패키지에서 호스팅 시작 기능 향상을 제공할 수 있습니다. 패키지에 `HostingStartup` 특성이 있습니다. 패키지에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.

* 향상된 앱의 프로젝트 파일은 앱의 프로젝트 파일(컴파일 시간 참조)에서 호스팅 시작을 위한 패키지 참조를 만듭니다. 이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다. 이 방식은 [nuget.org](https://www.nuget.org/)에 게시된 호스팅 시작 어셈블리 패키지에 적용됩니다.
* 호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).

NuGet 패키지 및 런타임 저장소에 대한 자세한 내용은 다음 항목을 참조하세요.

* [플랫폼 간 도구로 NuGet 패키지를 만드는 방법](/dotnet/core/deploying/creating-nuget-packages)
* [패키지 게시](/nuget/create-packages/publish-a-package)
* [런타임 패키지 저장소](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>프로젝트 bin 폴더

호스팅 시작 향상 기능은 향상된 앱의 *bin* 배포 어셈블리에 의해 제공될 수 있습니다. 어셈블리에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.

* 향상된 앱의 프로젝트 파일은 호스팅 시작에 대한 어셈블리 참조를 만듭니다(컴파일 시간 참조). 이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다. 이 방법은 배포 시나리오에서 호스팅 시작 어셈블리( *.dll* 파일)에 대한 컴파일 시간 참조를 수행하고 어셈블리를 다음 중 하나로 이동해야 할 때 적용됩니다.
  * 사용하는 프로젝트입니다.
  * 사용하는 프로젝트에서 액세스할 수 있는 위치입니다.
* 호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).
* .NET Framework를 대상으로 지정하는 경우 어셈블리는 기본 로드 컨텍스트에 로드될 수 있습니다. 즉, .NET Framework는 어셈블리가 다음 위치 중 하나에 있는 것을 의미합니다.
  * 애플리케이션 기본 경로: 애플리케이션의 실행 파일( *.exe*)이 있는 *bin* 폴더입니다.
  * GAC(전역 어셈블리 캐시): GAC에는 여러 .NET Framework 앱이 공유하는 어셈블리가 저장됩니다. 자세한 내용은 [방법: .NET Framework 설명서의 전역 어셈블리 캐시](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)에 어셈블리를 설치합니다.

## <a name="sample-code"></a>샘플 코드

[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)([다운로드 방법](xref:index#how-to-download-a-sample))는 호스팅 시작 구현 시나리오를 보여 줍니다.

* 두 개의 호스팅 시작 어셈블리(클래스 라이브러리)는 각각 한 쌍의 메모리 내 구성 키-값 쌍을 설정합니다.
  * NuGet 패키지(*HostingStartupPackage*)
  * 클래스 라이브러리(*HostingStartupLibrary*)
* 호스팅 시작은 런타임 저장소 배포 어셈블리에서 활성화됩니다(*StartupDiagnostics*). 어셈블리는 시작 시 다음과 같은 진단 정보를 제공하는 두 개의 미들웨어를 앱에 추가합니다.
  * 등록된 서비스
  * 주소(구성표, 호스트, 기본 경로, 경로, 쿼리 문자열)
  * 연결(원격 IP, 원격 포트, 로컬 IP, 로컬 포트, 클라이언트 인증서)
  * 요청 헤더
  * 환경 변수

샘플을 실행하려면:

**NuGet 패키지에서 활성화**

1. [dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용하여 *HostingStartupPackage* 패키지를 컴파일합니다.
1. *HostingStartupPackage* 패키지의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.
1. 앱을 컴파일하고 실행합니다. 패키지 참조가 향상된 앱(컴파일 시간 참조)에 있습니다. 앱의 프로젝트 파일에 있는 `<PropertyGroup>`에서는 패키지 프로젝트의 출력( *../HostingStartupPackage/bin/Debug*)을 패키지 원본으로 지정합니다. 이렇게 하면 [nuget.org](https://www.nuget.org/)에 패키지를 업로드하지 않고 패키지를 사용할 수 있습니다. 자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. 인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 패키지의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.

*HostingStartupPackage* 프로젝트를 변경하고 다시 컴파일하는 경우, 로컬 NuGet 패키지 캐시의 선택을 취소하여 *HostingStartupApp* 이 로컬 캐시에서 부실 패키지가 아닌 업데이트된 패키지를 수신하는지 확인합니다. 로컬 NuGet 캐시를 지우려면 다음 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 명령을 실행합니다.

```dotnetcli
dotnet nuget locals all --clear
```

**클래스 라이브러리에서 활성화**

1. [dotnet build](/dotnet/core/tools/dotnet-build) 명령을 사용하여 *HostingStartupLibrary* 클래스 라이브러리를 컴파일합니다.
1. *HostingStartupLibrary* 클래스 라이브러리의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.
1. *bin* - *HostingStartupLibrary.dll* 파일을 클래스 라이브러리의 컴파일된 출력에서 앱의 *bin/Debug* 폴더로 복사하여 클래스 라이브러리의 어셈블리를 앱에 배포합니다.
1. 앱을 컴파일하고 실행합니다. 앱의 프로젝트 파일에 있는 `<ItemGroup>`는 클래스 라이브러리의 어셈블리( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (컴파일 시간 참조)를 참조하세요. 자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. 인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 클래스 라이브러리의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.

**런타임 저장소 배포 어셈블리에서 활성화**

1. *StartupDiagnostics* 프로젝트는 [PowerShell](/powershell/scripting/powershell-scripting)을 사용하여 해당 *StartupDiagnostics.deps.json* 파일을 수정합니다. PowerShell은 Windows 7 SP1 및 Windows Server 2008 R2 SP1부터 Windows에서 기본적으로 설치됩니다. 다른 플랫폼에서 PowerShell을 가져오려면 [다양한 버전의 PowerShell 설치](/powershell/scripting/install/installing-powershell)를 참조하세요.
1. *RuntimeStore* 폴더에서 *build.ps1* 스크립트를 실행합니다. 스크립트는 다음을 수행합니다.
   * *obj\packages* 폴더에 `StartupDiagnostics` 패키지를 생성합니다.
   * *store* 폴더에서 `StartupDiagnostics`의 런타임 저장소를 생성합니다. 해당 스크립트에서 `dotnet store` 명령은 Windows에 배포된 호스팅 시작에서 `win7-x64` [RID(런타임 식별자)](/dotnet/core/rid-catalog)를 사용합니다. 다른 런타임에 호스팅 시작을 제공할 때 스크립트의 줄 37에서 올바른 RID로 대체합니다. `StartupDiagnostics`의 런타임 저장소는 나중에 어셈블리가 사용될 컴퓨터의 사용자 또는 시스템의 런타임 저장소로 이동됩니다. `StartupDiagnostics` 어셈블리의 사용자 런타임 저장소 설치 위치는 *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll* 입니다.
   * *additionalDeps* 폴더에 `StartupDiagnostics`의 `additionalDeps`를 생성합니다. 추가 종속성은 나중에 사용자 또는 시스템의 추가 종속성으로 이동됩니다. 사용자 `StartupDiagnostics` 추가 종속성 설치 위치는 *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json* 입니다.
   * *deployment* 폴더에 *deploy.ps1* 파일을 배치합니다.
1. *배포* 폴더에서 *deploy.ps1* 스크립트를 실행합니다. 스크립트는 다음을 수행합니다.
   * `StartupDiagnostics`를 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.
   * `DOTNET_ADDITIONAL_DEPS` 환경 변수의 호스팅 시작 종속성 경로(RuntimeStore 프로젝트의 *deployment* 폴더에 있음).
   * `DOTNET_SHARED_STORE` 환경 변수의 런타임 저장소 경로(RuntimeStore 프로젝트의 *deployment* 폴더에 있음).
1. 샘플 앱을 실행합니다.
1. `/services` 엔드포인트가 앱의 등록된 서비스를 확인하도록 요청합니다. `/diag` 엔드포인트가 진단 정보를 확인하도록 요청합니다.

::: moniker-end
