---
title: ASP.NET Core 모듈
author: rick-anderson
description: IIS를 사용하여 ASP.NET Core 앱을 호스트하기 위한 ASP.NET Core 모듈에 관해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 8574bc06b7c27fbb8caf834754188dba67bcb4e3
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605699"
---
# <a name="aspnet-core-module"></a>ASP.NET Core 모듈

작성자: [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-5.0"

ASP.NET Core 모듈은 IIS 파이프라인에 연결되어 ASP.NET Core 애플리케이션이 IIS에서 작동할 수 있도록 하는 네이티브 IIS 모듈입니다. 다음 중 하나를 사용하여 IIS에서 ASP.NET Core 앱을 실행합니다. 

* IIS 작업자 프로세스(`w3wp.exe`) 내부에 ASP.NET Core 앱을 호스트하며 [In Process 호스팅 모델](xref:host-and-deploy/iis/in-process-hosting)이라고 합니다.
* Kestrel 서버를 실행하는 백 엔드 ASP.NET Core 앱으로 웹 요청을 전달하며 [Out of Process 호스팅 모델](xref:host-and-deploy/iis/out-of-process-hosting)이라고 합니다.

각 호스팅 모델에는 장단점이 있습니다. 기본적으로 더 나은 성능 및 진단 기능으로 인해 In Process 호스팅 모델이 사용됩니다.

## <a name="install-aspnet-core-module"></a>ASP.NET Core 모듈 설치

다음 링크를 사용하여 설치 관리자를 다운로드합니다.

[현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

ASP.NET Core 모듈을 설치하거나 다양한 버전의 모듈을 설치하는 방법에 관한 자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/hosting-bundle)를 참조하세요.

IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

ASP.NET Core 모듈은 다음을 위해 IIS 파이프라인에 연결되는 네이티브 IIS 모듈입니다.

* IIS 작업자 프로세스(`w3wp.exe`) 내부에 ASP.NET Core 앱을 호스트하며 [In-Process 호스팅 모델](#in-process-hosting-model)이라고 합니다.
* [Kestrel 서버](xref:fundamentals/servers/kestrel)를 실행하는 백 엔드 ASP.NET Core 앱으로 웹 요청을 전달하며 [Out-of-Process 호스팅 모델](#out-of-process-hosting-model)이라고 합니다.

지원되는 Windows 버전:

* Windows 7 이상
* Windows Server 2012 R2 이상

In Process를 호스트하는 경우 모듈에서는 IIS HTTP Server(`IISHttpServer`)라는 IIS용 In Process 서버 구현을 사용합니다.

Out-of-Process로 호스트하는 경우 모듈은 Kestrel에서만 작동합니다. 이 모듈은 [HTTP.sys](xref:fundamentals/servers/httpsys)와 작동하지 않습니다.

## <a name="hosting-models"></a>호스팅 모델

### <a name="in-process-hosting-model"></a>In-Process 호스팅 모델

ASP.NET Core 앱의 기본값은 In-process 호스팅 모델입니다.

다음 특성은 In-Process로 호스팅할 때 적용됩니다.

* IIS HTTP 서버(`IISHttpServer`)는 [Kestrel](xref:fundamentals/servers/kestrel) 서버 대신 사용됩니다. In Process의 경우 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>를 호출하여 다음을 수행합니다.

  * `IISHttpServer`를 등록합니다.
  * ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.
  * 시작 오류를 캡처하도록 호스트를 구성합니다.

* [requestTimeout 특성](#attributes-of-the-aspnetcore-element)이 In-Process 호스팅에 적용되지 않습니다.

* 앱 간의 앱 풀 공유는 지원되지 않습니다. 앱당 하나의 앱 풀을 사용합니다.

* [웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)를 사용하거나 [`app_offline.htm` 파일을 배포](xref:host-and-deploy/iis/index#locked-deployment-files)에 수동으로 배치할 경우 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다. 예를 들어 WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.

* 앱 및 설치된 런타임(x64 또는 x86)의 아키텍처(비트)는 앱 풀의 아키텍처와 일치해야 합니다.

* 클라이언트의 연결 끊김이 검색되었습니다. 클라이언트의 연결이 끊어지면 [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 취소 토큰이 취소됩니다.

* ASP.NET Core 2.2.1 이하에서 <xref:System.IO.Directory.GetCurrentDirectory*>는 앱 디렉터리가 아닌 IIS에 의해 시작된 프로세스의 작업자 디렉터리를 반환합니다(예: `w3wp.exe`에 대한 `C:\Windows\System32\inetsrv`).

  앱의 현재 디렉터리를 설정하는 샘플 코드는 [`CurrentDirectoryHelpers` 클래스](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)를 참조하세요. `SetCurrentDirectory` 메서드를 호출합니다. <xref:System.IO.Directory.GetCurrentDirectory*>에 대한 후속 호출은 앱의 디렉터리를 제공합니다.

* In-process로 호스팅하는 경우 사용자를 초기화하기 위해 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>를 내부적으로 호출하지 않습니다. 따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다. <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현으로 클레임을 변환할 때 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>을 호출하여 인증 서비스를 추가합니다.

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * [웹 패키지(단일 파일) 배포](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)는 지원되지 않습니다.

### <a name="out-of-process-hosting-model"></a>Out-of-Process 호스팅 모델

Out-of-Process 호스팅을 위해 앱을 구성하려면 프로젝트 파일( `.csproj`)에서 `<AspNetCoreHostingModel>` 속성의 값을 `OutOfProcess`로 설정합니다.

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

In-Process 호스팅은 기본값 `InProcess`로 설정됩니다.

`<AspNetCoreHostingModel>`의 값은 대/소문자를 구분하지 않으므로 `inprocess`와 `outofprocess`는 유효한 값입니다.

[Kestrel](xref:fundamentals/servers/kestrel) 서버는 IIS HTTP 서버(`IISHttpServer`) 대신 사용됩니다.

Out of Process의 경우 [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>을 호출합니다.

* ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.
* 시작 오류를 캡처하도록 호스트를 구성합니다.

### <a name="hosting-model-changes"></a>호스팅 모델 변경

`hostingModel` 설정이 `web.config` 파일에서 변경되면([`web.config`로 구성](#configuration-with-webconfig) 섹션에 설명되어 있음) 모듈은 IIS에 대한 작업자 프로세스를 재순환합니다.

IIS Express의 경우 모듈은 작업자 프로세스를 재순환하지 않고, 대신 현재 IIS Express 프로세스의 정상 종료를 트리거합니다. 앱에 대한 다음 요청은 새 IIS Express 프로세스를 생성합니다.

### <a name="process-name"></a>프로세스 이름

`Process.GetCurrentProcess().ProcessName`은 `w3wp`/`iisexpress`(In-Process) 또는 `dotnet`(Out-of-Process)을 보고합니다.

Windows 인증 등의 많은 네이티브 모듈이 활성 상태로 유지됩니다. ASP.NET Core 모듈을 사용하여 활성화된 IIS 모듈에 대해 자세히 알아보려면 <xref:host-and-deploy/iis/modules>를 참조하세요.

ASP.NET Core 모듈은 다음 작업을 수행할 수도 있습니다.

* 작업자 프로세스의 환경 변수를 설정합니다.
* 시작 문제를 해결하기 위해 stdout 출력을 파일 스토리지에 기록합니다.
* Windows 인증 토큰을 전달합니다.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Core 모듈을 설치하고 사용하는 방법

ASP.NET Core 모듈을 설치하는 방법에 대한 지침은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.

## <a name="configuration-with-webconfig"></a>web.config를 사용한 구성

ASP.NET Core 모듈은 사이트의 *web.config* 파일에 있는 `system.webServer` 노드의 `aspNetCore` 섹션으로 구성됩니다.

다음 `web.config` 파일은 [프레임워크 종속 배포](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)를 위해 게시되고 사이트 요청을 처리하도록 ASP.NET Core 모듈을 구성합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

다음 *web.config* 는 [자체 포함 배포](/dotnet/articles/core/deploying/#self-contained-deployments-scd)를 위해 게시됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<xref:System.Configuration.SectionInformation.InheritInChildApplications*> 속성이 `false`로 설정되어 [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 요소 내에서 지정된 설정이 하위 디렉터리에 있는 앱에 상속되지 않음을 나타냅니다.

앱이 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포되면 `stdoutLogFile` 경로가 `\\?\%home%\LogFiles\stdout`로 설정됩니다. 이 경로는 서비스에서 자동으로 만들어진 위치인 `LogFiles` 폴더에 stdout 로그를 저장합니다.

IIS 하위 애플리케이션 구성에 대한 자세한 내용은 <xref:host-and-deploy/iis/index#sub-applications>를 참조하세요.

### <a name="attributes-of-the-aspnetcore-element"></a>aspNetCore 요소의 특성

| 특성 | 설명 | 기본값 |
| --------- | ----------- | :-----: |
| `arguments` | <p>선택적 문자열 특성입니다.</p><p>**processPath** 에 지정된 실행 파일에 대한 인수입니다.</p> | |
| `disableStartUpErrorPage` | <p>선택적 부울 특성입니다.</p><p>true인 경우 **502.5 - 프로세스 실패** 페이지가 표시되지 않고 *web.config* 에 구성된 502 상태 코드 페이지가 우선 적용됩니다.</p> | `false` |
| `forwardWindowsAuthToken` | <p>선택적 부울 특성입니다.</p><p>true인 경우 토큰은 `%ASPNETCORE_PORT%`에서 수신 대기하는 자식 프로세스에 요청별 헤더 `'MS-ASPNETCORE-WINAUTHTOKEN'`으로 전달됩니다. 이 프로세스는 요청별로 이 토큰에서 CloseHandle을 호출합니다.</p> | `true` |
| `hostingModel` | <p>선택적 문자열 특성입니다.</p><p>호스팅 모델을 In-Process(`InProcess`/`inprocess`) 또는 Out-of-Process(`OutOfProcess`/`outofprocess`)로 지정합니다.</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>선택적 정수 특성입니다.</p><p>앱별로 스핀 업할 수 있는 **processPath** 설정에 지정된 프로세스의 인스턴스 수를 지정합니다.</p><p>&dagger;In-Process 호스팅의 경우 이 값은 `1`로 제한됩니다.</p><p>설정 `processesPerApplication`은 권장되지 않습니다. 이 특성은 이후 릴리스에서 제거됩니다.</p> | 기본값: `1`<br>최소: `1`<br>최대: `100`&dagger; |
| `processPath` | <p>필수 문자열 특성입니다.</p><p>HTTP 요청을 수신 대기하는 프로세스를 시작하는 실행 파일의 경로입니다. 상대 경로가 지원됩니다. 경로가 `.`로 시작되면 경로는 사이트 루트의 상대 경로로 간주됩니다.</p> | |
| `rapidFailsPerMinute` | <p>선택적 정수 특성입니다.</p><p>**processPath** 에 지정된 프로세스의 분당 크래시 허용 횟수를 지정합니다. 이 제한을 초과하면 모듈은 남은 시간 동안 프로세스 시작을 중지합니다.</p><p>In-Process 호스팅에서는 지원되지 않습니다.</p> | 기본값: `10`<br>최소: `0`<br>최대: `100` |
| `requestTimeout` | <p>선택적 시간 간격 특성입니다.</p><p>ASP.NET Core 모듈이 %ASPNETCORE_PORT%에서 수신 대기하는 프로세스의 응답을 기다리는 기간을 지정합니다.</p><p>ASP.NET Core 2.1 이상 릴리스와 함께 제공되는 ASP.NET Core 모듈 버전에서는 `requestTimeout`이 전체 시간, 분, 초로 지정됩니다.</p><p>In-Process 호스팅에는 적용되지 않습니다. In-Process 호스팅의 경우 모듈은 앱이 요청을 처리할 때까지 기다립니다.</p><p>문자열의 분 및 초 세그먼트에 유효한 값은 0-59 범위입니다. 분 또는 초의 값에 **60** 을 사용하면 *500 - 내부 서버 오류* 가 발생됩니다.</p> | 기본값: `00:02:00`<br>최소: `00:00:00`<br>최대: `360:00:00` |
| `shutdownTimeLimit` | <p>선택적 정수 특성입니다.</p><p>*app_offline.htm* 파일이 검색될 때 실행 파일이 정상적으로 종료될 때까지 모듈이 기다리는 기간(초)입니다.</p> | 기본값: `10`<br>최소: `0`<br>최대: `600` |
| `startupTimeLimit` | <p>선택적 정수 특성입니다.</p><p>실행 파일이 포트에서 수신 대기하는 프로세스를 시작할 때까지 모듈이 기다리는 기간(초)입니다. 이 시간 제한을 초과하면 모듈이 프로세스를 종료합니다.</p><p>*In Process* 를 호스트하는 경우: 프로세스가 다시 시작되지 **않고** **rapidFailsPerMinute** 설정을 사용하지 **않습니다**.</p><p>*Out-of-Process* 를 호스트하는 경우: 모듈은 새 요청을 수신할 때 프로세스를 다시 시작하려고 하고, 마지막 롤링 기간(분)에 앱이 **rapidFailsPerMinute** 번 시작에 실패한 경우가 아니면 이후 요청이 들어올 때 프로세스를 계속 다시 시작하려고 합니다.</p><p>값 0은 무한 시간 제한으로 간주되지 **않습니다**.</p> | 기본값: `120`<br>최소: `0`<br>최대: `3600` |
| `stdoutLogEnabled` | <p>선택적 부울 특성입니다.</p><p>true인 경우 **processPath** 에 지정된 프로세스에 대한 **stdout** 및 **stderr** 이 **stdoutLogFile** 에 지정된 파일로 리디렉션됩니다.</p> | `false` |
| `stdoutLogFile` | <p>선택적 문자열 특성입니다.</p><p>**processPath** 에 지정된 프로세스에서 **stdout** 및 **stderr** 이 기록되는 상대 또는 절대 파일 경로를 지정합니다. 상대 경로는 사이트 루트에 상대적인 경로입니다. `.`로 시작하는 모든 경로는 사이트 루트에 상대적인 경로이고 다른 모든 경로는 절대 경로로 처리됩니다. 경로에 제공된 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다. 타임스탬프, 프로세스 ID 및 파일 확장명( `.log`)은 밑줄 구분 기호를 사용하여 **stdoutLogFile** 경로의 마지막 세그먼트에 추가됩니다. `.\logs\stdout`이 값으로 제공되는 경우 예제 stdout 로그는 2018년 2월 5일 19시 41분 32초에 프로세스 ID 1934를 사용하여 저장될 경우 `logs` 폴더에 `stdout_20180205194132_1934.log`로 저장됩니다.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>환경 변수 설정

`processPath` 특성에서 프로세스에 대한 환경 변수를 지정할 수 있습니다. `<environmentVariables>` 컬렉션 요소의 `<environmentVariable>` 자식 요소를 사용하여 환경 변수를 지정합니다. 이 섹션에 설정된 환경 변수가 시스템 환경 변수보다 우선 적용됩니다.

다음 예제에서는 `web.config`에서 두 개의 환경 변수를 설정합니다. `ASPNETCORE_ENVIRONMENT`는 앱의 환경을 `Development`로 구성합니다. 앱 예외를 디버그할 때 [개발자 예외 페이지](xref:fundamentals/error-handling)를 강제로 로드하기 위해 개발자가 `web.config` 파일에서 이 값을 일시적으로 설정할 수 있습니다. `CONFIG_DIR`은 개발자가 앱 구성 파일을 로드할 경로를 생성하기 위해 시작 시 값을 읽는 코드를 작성한 사용자 정의 환경 변수의 예입니다.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> `web.config`에서 환경을 직접 설정하는 대안으로 [게시 프로필(`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 `<EnvironmentName>` 속성을 포함합니다. 이 방법은 프로젝트가 게시될 때 `web.config`에 환경을 설정합니다.
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> 인터넷과 같은 신뢰할 수 없는 네트워크에 액세스할 수 없는 스테이징 및 테스트 서버에서는 `ASPNETCORE_ENVIRONMENT` 환경 변수를 `Development`로 설정하면 됩니다.

## `app_offline.htm`

응용 프로그램의 루트 디렉터리에서 이름이 `app_offline.htm`인 파일이 검색되면 ASP.NET Core 모듈은 앱을 자동으로 종료하고 들어오는 요청 처리를 중지하려고 합니다. `shutdownTimeLimit`에 정의된 시간(초) 후에도 앱이 계속 실행되면 ASP.NET Core 모듈은 실행 중인 프로세스를 종료합니다.

`app_offline.htm` 파일이 있는 동안 ASP.NET Core 모듈은 `app_offline.htm` 파일의 콘텐츠를 다시 보내 요청에 응답합니다. `app_offline.htm` 파일이 제거되면 다음 요청이 앱을 시작합니다.

Out-of-Process 호스팅 모델을 사용할 때 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다. 예를 들어 WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.

## <a name="start-up-error-page"></a>시작 오류 페이지

In process 및 out of process 호스팅은 모두 앱을 시작하지 못할 때 사용자 지정 오류 페이지를 생성합니다.

ASP.NET Core 모듈이 in-process 또는 out-of-process 요청 처리기를 찾지 못하면 *500.0 - In-Process/Out-of-process 처리기 로드 실패* 상태 코드 페이지가 나타납니다.

in-process 호스팅의 경우 ASP.NET Core 모듈이 앱을 시작하지 못하면 *500.30 - 시작 실패* 상태 코드 페이지가 나타납니다.

out-of-process 호스팅의 경우 ASP.NET Core 모듈이 백 엔드 프로세스를 시작하지 못하거나 백 엔드 프로세스가 시작되지만 구성된 포트에서 수신 대기하지 못하면 *502.5 - 프로세스 실패* 상태 코드 페이지가 나타납니다.

이 페이지를 표시하지 않고 기본 IIS 5xx 상태 코드 페이지로 되돌리려면 `disableStartUpErrorPage` 특성을 사용합니다. 사용자 지정 오류 메시지 구성에 대한 자세한 내용은 [HTTP 오류`<httpErrors>`](/iis/configuration/system.webServer/httpErrors/)를 참조하세요.

## <a name="log-creation-and-redirection"></a>로그 만들기 및 리디렉션

ASP.NET Core 모듈은 `aspNetCore` 요소의 `stdoutLogEnabled` 및 `stdoutLogFile` 특성이 설정된 경우 stdout 및 stderr 콘솔 출력을 디스크로 리디렉션합니다. `stdoutLogFile` 경로의 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다. 앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\<app_pool_name>`을 사용하여 쓰기 권한 제공).

프로세스 재생/다시 시작이 발생하지 않는 한 로그는 회전되지 않습니다. 로그에서 사용하는 디스크 공간을 제한하는 것은 호스터의 책임입니다.

stdout 로그는 IIS에서 호스팅할 때 또는 [Visual Studio에서 IIS를 위한 개발 시간 지원](xref:host-and-deploy/iis/development-time-iis-support)을 사용할 때 앱 시작 문제를 해결하기 위해서만 사용하는 것이 좋으며, 로컬에서 디버깅하면서 IIS Express를 사용하여 앱을 실행할 때는 권장되지 않습니다.

일반 앱 로깅을 위해 stdout 로그를 사용하지 마세요. ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

로그 파일이 만들어질 때 타임스탬프 및 파일 확장명이 자동으로 추가됩니다. 로그 파일 이름은 타임스탬프, 프로세스 ID 및 파일 확장명( `.log`)을 밑줄로 구분된 `stdoutLogFile` 경로의 마지막 세그먼트(일반적으로 `stdout`)에 추가하여 작성됩니다. `stdoutLogFile` 경로가 `stdout`으로 끝나는 경우 2018년 2월 5일 19시 42분 32초에 만들어진 PID 1934를 사용하는 앱에 대한 로그의 파일 이름은 `stdout_20180205194132_1934.log`입니다.

`stdoutLogEnabled`가 false이면 앱 시작 시 발생하는 오류가 캡처되어 최대 30KB의 이벤트 로그로 내보냅니다. 시작 후에는 모든 추가 로그가 삭제됩니다.

다음 샘플 `aspNetCore` 요소는 상대 경로 `.\log\`에서 stdout 로깅을 구성합니다. AppPool 사용자 ID에 제공된 경로에 쓸 수 있는 권한이 있는지 확인합니다.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Azure App Service 배포를 위해 앱을 게시할 때는 웹 SDK가 `stdoutLogFile` 값을 `\\?\%home%\LogFiles\stdout`으로 설정합니다. `%home` 환경 변수는 Azure App Service에 의해 호스팅되는 앱에 대해 미리 정의됩니다.

로깅 필터 규칙을 만들려면 ASP.NET Core 로깅 설명서의 [구성](xref:fundamentals/logging/index#log-filtering) 섹션과 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 섹션을 참조하세요.

경로 형식에 대한 자세한 내용은 [Windows 시스템의 파일 경로 형식](/dotnet/standard/io/file-path-formats)을 참조하세요.

## <a name="enhanced-diagnostic-logs"></a>개선된 진단 로그

ASP.NET Core 모듈은 개선된 진단 로그를 제공하도록 구성할 수 있습니다. `web.config`에서 `<handlerSettings>` 요소를 `<aspNetCore>` 요소에 추가합니다. `debugLevel`을 `TRACE`으로 설정하면 진단 정보의 충실도가 높아집니다.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

경로에 있는 모든 폴더(위 예제의 `logs`)가 로그 파일을 만들 때 모듈에서 생성됩니다. 앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\{APP POOL NAME}`를 사용하여 쓰기 권한 제공, 여기서 자리 표시자 `{APP POOL NAME}`는 앱 풀 이름임).

디버그 수준 (`debugLevel`) 값은 수준과 위치를 모두 포함할 수 있습니다.

수준(최소한에서 가장 자세한 정보까지 순서대로 ):

* 오류
* 경고
* 정보
* TRACE

위치(여러 위치가 허용됨):

* CONSOLE
* EVENTLOG
* FILE

처리기 설정은 환경 변수를 통해서도 제공할 수 있습니다.

* `ASPNETCORE_MODULE_DEBUG_FILE`: 디버그 로그 파일의 경로입니다. (기본값: `aspnetcore-debug.log`)
* `ASPNETCORE_MODULE_DEBUG`: 디버그 수준 설정입니다.

> [!WARNING]
> 배포에서 문제를 해결하는 데 필요한 시간보다 오래 디버그 로깅을 사용하도록 설정하지 **마세요**. 로그의 크기는 제한되지 않습니다. 디버그 로그를 사용하도록 설정한 대로 두면 사용 가능한 디스크 공간이 소진되어 서버 또는 앱 서비스가 크래시될 수 있습니다.

`web.config` 파일에 있는 `aspNetCore` 요소의 예제에 대해서는 [web.config를 사용한 구성](#configuration-with-webconfig)을 참조하세요.

## <a name="modify-the-stack-size"></a>스택 크기 수정

*In-process 호스팅 모델을 사용하는 경우에만 적용됩니다.*

`web.config`에서 `stackSize` 설정을 사용하여 관리형 스택 크기(바이트)를 구성합니다. 기본 크기는 1,048,576 bytes(1MB)입니다.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>프록시 구성은 HTTP 프로토콜 및 페어링 토큰을 사용합니다.

*호스팅에만 적용됩니다.*

ASP.NET Core 모듈과 Kestrel 사이에 만들어진 프록시는 HTTP 프로토콜을 사용합니다. 서버에서 분리된 위치에서 모듈과 Kestrel 간 트래픽 도청의 위험은 없습니다.

페어링 토큰은 Kestrel에서 받은 요청이 IIS에서 프록시되었으며 다른 원본에서 오지 않았다는 것을 보장하는 데 사용됩니다. 페어링 토큰은 모듈에 의해 생성되며 환경 변수(`ASPNETCORE_TOKEN`)로 설정됩니다. 페어링 토큰은 프록시된 모든 요청에서 헤더(`MS-ASPNETCORE-TOKEN`)로도 설정됩니다. IIS 미들웨어는 수신하는 각 요청을 검사하여 페어링 토큰 헤더 값이 환경 변수 값과 일치하는지 확인합니다. 토큰 값이 일치하지 않는 경우 요청이 기록되고 거부됩니다. 페어링 토큰 환경 변수와 모듈과 Kestrel 간의 트래픽은 서버에서 분리된 위치에서 액세스할 수 없습니다. 페어링 토큰 값을 알지 못하면 공격자는 IIS 미들웨어에서 검사를 무시하는 요청을 전송할 수 없습니다.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS 공유 구성이 포함된 ASP.NET Core 모듈

ASP.NET Core 모듈 설치 관리자는 **TrustedInstaller** 계정의 권한으로 실행됩니다. 로컬 시스템 계정에는 IIS 공유 구성에서 사용하는 공유 경로에 대한 수정 권한이 없으므로 공유의 `applicationHost.config`에서 모듈 설정을 구성하려고 하면 설치 관리자에서 액세스 거부 오류가 throw됩니다.

IIS 설치와 동일한 머신에서 IIS 공유 구성을 사용하는 경우 `OPT_NO_SHARED_CONFIG_CHECK` 매개 변수를 `1`로 설정한 상태에서 ASP.NET Core Hosting Bundle 설치 관리자를 실행합니다

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

공유 구성에 대한 경로가 IIS 설치와 동일한 머신에 있지 않으면 다음 단계를 수행합니다.

1. IIS 공유 구성을 사용하지 않도록 설정합니다.
1. 설치 관리자를 실행합니다.
1. 업데이트된 `applicationHost.config` 파일을 공유로 내보냅니다.
1. IIS 공유 구성을 다시 사용하도록 설정합니다.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>모듈 버전 및 호스팅 번들 설치 관리자 로그

설치된 ASP.NET Core 모듈의 버전을 확인하려면:

1. 호스팅 시스템에서 `%windir%\System32\inetsrv`로 이동합니다.
1. `aspnetcore.dll` 파일을 찾습니다.
1. 파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성** 을 선택합니다.
1. **세부 정보** 탭을 선택합니다. **파일 버전** 및 **제품 버전** 은 설치된 모듈 버전을 나타냅니다.

모듈의 호스팅 번들 설치 관리자 로그는 `C:\Users\%UserName%\AppData\Local\Temp`에 있습니다. 파일 이름은 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`로 지정됩니다.

## <a name="module-schema-and-configuration-file-locations"></a>모듈, 스키마 및 구성 파일 위치

### <a name="module"></a>Module

**IIS(x86/amd64):**

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express(x86/amd64):**

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>스키마

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>Configuration

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* *iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

`applicationHost.config` 파일에서 `aspnetcore`를 검색하여 파일을 찾을 수 있습니다.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

ASP.NET Core 모듈은 다음을 위해 IIS 파이프라인에 연결되는 네이티브 IIS 모듈입니다.

* IIS 작업자 프로세스(`w3wp.exe`) 내부에 ASP.NET Core 앱을 호스트하며 [In-Process 호스팅 모델](#in-process-hosting-model)이라고 합니다.
* [Kestrel 서버](xref:fundamentals/servers/kestrel)를 실행하는 백 엔드 ASP.NET Core 앱으로 웹 요청을 전달하며 [Out-of-Process 호스팅 모델](#out-of-process-hosting-model)이라고 합니다.

지원되는 Windows 버전:

* Windows 7 이상
* Windows Server 2008 R2 이상

In Process를 호스트하는 경우 모듈에서는 IIS HTTP Server(`IISHttpServer`)라는 IIS용 In Process 서버 구현을 사용합니다.

Out-of-Process로 호스트하는 경우 모듈은 Kestrel에서만 작동합니다. 이 모듈은 [HTTP.sys](xref:fundamentals/servers/httpsys)와 작동하지 않습니다.

## <a name="hosting-models"></a>호스팅 모델

### <a name="in-process-hosting-model"></a>In-Process 호스팅 모델

In-Process 호스팅용 앱을 구성하려면 `<AspNetCoreHostingModel>` 속성을 `InProcess` 값의 앱 프로젝트 파일에 추가합니다(Out-of-Process 호스팅은 `OutOfProcess`로 설정됨).

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

In-process 호스팅 모델은 .NET Framework를 대상으로 하는 ASP.NET Core 앱을 지원하지 않습니다.

`<AspNetCoreHostingModel>`의 값은 대/소문자를 구분하지 않으므로 `inprocess`와 `outofprocess`는 유효한 값입니다.

파일에 `<AspNetCoreHostingModel>` 속성이 없으면 기본값은 `OutOfProcess`입니다.

다음 특성은 In-Process로 호스팅할 때 적용됩니다.

* IIS HTTP 서버(`IISHttpServer`)는 [Kestrel](xref:fundamentals/servers/kestrel) 서버 대신 사용됩니다. In Process의 경우 [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>를 호출하여 다음을 수행합니다.

  * `IISHttpServer`를 등록합니다.
  * ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.
  * 시작 오류를 캡처하도록 호스트를 구성합니다.

* [requestTimeout 특성](#attributes-of-the-aspnetcore-element)이 In-Process 호스팅에 적용되지 않습니다.

* 앱 간의 앱 풀 공유는 지원되지 않습니다. 앱당 하나의 앱 풀을 사용합니다.

* [웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)를 사용하거나 [app_offline.htm 파일을 배포에](xref:host-and-deploy/iis/index#locked-deployment-files) 수동으로 배치할 경우 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다. 예를 들어, WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.

* 앱 및 설치된 런타임(x64 또는 x86)의 아키텍처(비트)는 앱 풀의 아키텍처와 일치해야 합니다.

* 클라이언트의 연결 끊김이 검색되었습니다. 클라이언트의 연결이 끊어지면 [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 취소 토큰이 취소됩니다.

* ASP.NET Core 2.2.1 이하에서 <xref:System.IO.Directory.GetCurrentDirectory*>는 앱 디렉터리가 아닌 IIS에 의해 시작된 프로세스의 작업자 디렉터리를 반환합니다(예: *w3wp.exe* 에 대한 *C:\Windows\System32\inetsrv*).

  앱의 현재 디렉터리를 설정하는 샘플 코드는 [CurrentDirectoryHelpers 클래스](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)를 참조하세요. `SetCurrentDirectory` 메서드를 호출합니다. <xref:System.IO.Directory.GetCurrentDirectory*>에 대한 후속 호출은 앱의 디렉터리를 제공합니다.

* In-process로 호스팅하는 경우 사용자를 초기화하기 위해 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>를 내부적으로 호출하지 않습니다. 따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다. <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현으로 클레임을 변환할 때 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>을 호출하여 인증 서비스를 추가합니다.

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a>Out-of-Process 호스팅 모델

Out of Process 호스팅을 위한 앱을 구성하려면 프로젝트 파일에서 다음 방법 중 하나를 사용합니다.

* `<AspNetCoreHostingModel>` 속성을 지정하지 마세요. 파일에 `<AspNetCoreHostingModel>` 속성이 없으면 기본값은 `OutOfProcess`입니다.
* `<AspNetCoreHostingModel>` 속성 값을 `OutOfProcess`로 설정합니다(In Process 호스트팅은 `InProcess`로 설정됨).

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

이 값은 대/소문자를 구분하지 않으므로 `inprocess`와 `outofprocess`는 유효한 값입니다.

[Kestrel](xref:fundamentals/servers/kestrel) 서버는 IIS HTTP 서버(`IISHttpServer`) 대신 사용됩니다.

Out of Process의 경우 [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>를 호출하여 다음을 수행합니다.

* ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.
* 시작 오류를 캡처하도록 호스트를 구성합니다.

### <a name="hosting-model-changes"></a>호스팅 모델 변경

`hostingModel` 설정이 *web.config* 파일에서 변경되면([web.config로 구성](#configuration-with-webconfig) 섹션에 설명되어 있음) 모듈은 IIS에 대한 작업자 프로세스를 재순환합니다.

IIS Express의 경우 모듈은 작업자 프로세스를 재순환하지 않고, 대신 현재 IIS Express 프로세스의 정상 종료를 트리거합니다. 앱에 대한 다음 요청은 새 IIS Express 프로세스를 생성합니다.

### <a name="process-name"></a>프로세스 이름

`Process.GetCurrentProcess().ProcessName`은 `w3wp`/`iisexpress`(In-Process) 또는 `dotnet`(Out-of-Process)을 보고합니다.

Windows 인증 등의 많은 네이티브 모듈이 활성 상태로 유지됩니다. ASP.NET Core 모듈을 사용하여 활성화된 IIS 모듈에 대해 자세히 알아보려면 <xref:host-and-deploy/iis/modules>를 참조하세요.

ASP.NET Core 모듈은 다음 작업을 수행할 수도 있습니다.

* 작업자 프로세스의 환경 변수를 설정합니다.
* 시작 문제를 해결하기 위해 stdout 출력을 파일 스토리지에 기록합니다.
* Windows 인증 토큰을 전달합니다.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Core 모듈을 설치하고 사용하는 방법

ASP.NET Core 모듈을 설치하는 방법에 대한 지침은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.

## <a name="configuration-with-webconfig"></a>web.config를 사용한 구성

ASP.NET Core 모듈은 사이트의 *web.config* 파일에 있는 `system.webServer` 노드의 `aspNetCore` 섹션으로 구성됩니다.

다음 *web.config* 파일은 [프레임워크 종속 배포](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)를 위해 게시되고 사이트 요청을 처리하도록 ASP.NET Core 모듈을 구성합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

다음 *web.config* 는 [자체 포함 배포](/dotnet/articles/core/deploying/#self-contained-deployments-scd)를 위해 게시됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<xref:System.Configuration.SectionInformation.InheritInChildApplications*> 속성이 `false`로 설정되어 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 요소 내에서 지정된 설정이 하위 디렉터리에 있는 앱에 상속되지 않음을 나타냅니다.

앱이 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포되면 `stdoutLogFile` 경로가 `\\?\%home%\LogFiles\stdout`로 설정됩니다. 이 경로는 서비스에서 자동으로 만들어진 위치인 *LogFiles* 폴더에 stdout 로그를 저장합니다.

IIS 하위 애플리케이션 구성에 대한 자세한 내용은 <xref:host-and-deploy/iis/index#sub-applications>를 참조하세요.

### <a name="attributes-of-the-aspnetcore-element"></a>aspNetCore 요소의 특성

| 특성 | 설명 | 기본값 |
| --------- | ----------- | :-----: |
| `arguments` | <p>선택적 문자열 특성입니다.</p><p>`processPath`에 지정된 실행 파일에 대한 인수입니다.</p> | |
| `disableStartUpErrorPage` | <p>선택적 부울 특성입니다.</p><p>true인 경우 **502.5 - 프로세스 실패** 페이지가 표시되지 않고 *web.config* 에 구성된 502 상태 코드 페이지가 우선 적용됩니다.</p> | `false` |
| `forwardWindowsAuthToken` | <p>선택적 부울 특성입니다.</p><p>true인 경우 토큰은 %ASPNETCORE_PORT%에서 수신 대기하는 자식 프로세스에 요청별 헤더 'MS-ASPNETCORE-WINAUTHTOKEN'으로 전달됩니다. 이 프로세스는 요청별로 이 토큰에서 CloseHandle을 호출합니다.</p> | `true` |
| `hostingModel` | <p>선택적 문자열 특성입니다.</p><p>호스팅 모델을 In-Process(`InProcess`/`inprocess`) 또는 Out-of-Process(`OutOfProcess`/`outofprocess`)로 지정합니다.</p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p>선택적 정수 특성입니다.</p><p>앱별로 스핀 업할 수 있는 `processPath` 설정에 지정된 프로세스의 인스턴스 수를 지정합니다.</p><p>&dagger;In-Process 호스팅의 경우 이 값은 `1`로 제한됩니다.</p><p>설정 `processesPerApplication`은 권장되지 않습니다. 이 특성은 이후 릴리스에서 제거됩니다.</p> | 기본값: `1`<br>최소: `1`<br>최대: `100`&dagger; |
| `processPath` | <p>필수 문자열 특성입니다.</p><p>HTTP 요청을 수신 대기하는 프로세스를 시작하는 실행 파일의 경로입니다. 상대 경로가 지원됩니다. 경로가 `.`로 시작되면 경로는 사이트 루트의 상대 경로로 간주됩니다.</p> | |
| `rapidFailsPerMinute` | <p>선택적 정수 특성입니다.</p><p>`processPath`에 지정된 프로세스의 분당 크래시 허용 횟수를 지정합니다. 이 제한을 초과하면 모듈은 남은 시간 동안 프로세스 시작을 중지합니다.</p><p>In-Process 호스팅에서는 지원되지 않습니다.</p> | 기본값: `10`<br>최소: `0`<br>최대: `100` |
| `requestTimeout` | <p>선택적 시간 간격 특성입니다.</p><p>ASP.NET Core 모듈이 %ASPNETCORE_PORT%에서 수신 대기하는 프로세스의 응답을 기다리는 기간을 지정합니다.</p><p>ASP.NET Core 2.1 이상 릴리스와 함께 제공되는 ASP.NET Core 모듈 버전에서는 `requestTimeout`이 전체 시간, 분, 초로 지정됩니다.</p><p>In-Process 호스팅에는 적용되지 않습니다. In-Process 호스팅의 경우 모듈은 앱이 요청을 처리할 때까지 기다립니다.</p><p>문자열의 분 및 초 세그먼트에 유효한 값은 0-59 범위입니다. 분 또는 초의 값에 **60** 을 사용하면 *500 - 내부 서버 오류* 가 발생됩니다.</p> | 기본값: `00:02:00`<br>최소: `00:00:00`<br>최대: `360:00:00` |
| `shutdownTimeLimit` | <p>선택적 정수 특성입니다.</p><p>`app_offline.htm` 파일이 검색될 때 실행 파일이 정상적으로 종료될 때까지 모듈이 기다리는 기간(초)입니다.</p> | 기본값: `10`<br>최소: `0`<br>최대: `600` |
| `startupTimeLimit` | <p>선택적 정수 특성입니다.</p><p>실행 파일이 포트에서 수신 대기하는 프로세스를 시작할 때까지 모듈이 기다리는 기간(초)입니다. 이 시간 제한을 초과하면 모듈이 프로세스를 종료합니다.</p><p>*In Process* 를 호스트하는 경우: 프로세스가 다시 시작되지 **않고** `rapidFailsPerMinute` 설정을 사용하지 **않습니다**.</p><p>*Out-of-Process* 를 호스트하는 경우: 모듈은 새 요청을 수신할 때 프로세스를 다시 시작하려고 하고, 마지막 롤링 기간(분)에 앱이 `rapidFailsPerMinute`번 시작에 실패한 경우가 아니면 이후 요청이 들어올 때 프로세스를 계속 다시 시작하려고 합니다.</p><p>값 0은 무한 시간 제한으로 간주되지 **않습니다**.</p> | 기본값: `120`<br>최소: `0`<br>최대: `3600` |
| `stdoutLogEnabled` | <p>선택적 부울 특성입니다.</p><p>true인 경우 `processPath`에 지정된 프로세스에 대한 **stdout** 및 **stderr** 이 **stdoutLogFile** 에 지정된 파일로 리디렉션됩니다.</p> | `false` |
| `stdoutLogFile` | <p>선택적 문자열 특성입니다.</p><p>`processPath`에 지정된 프로세스에서 `stdout` 및 `stderr`이 기록되는 상대 또는 절대 파일 경로를 지정합니다. 상대 경로는 사이트 루트에 상대적인 경로입니다. `.`로 시작하는 모든 경로는 사이트 루트에 상대적인 경로이고 다른 모든 경로는 절대 경로로 처리됩니다. 경로에 제공된 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다. 타임스탬프, 프로세스 ID 및 파일 확장명( `.log`)은 밑줄 구분 기호를 사용하여 `stdoutLogFile` 경로의 마지막 세그먼트에 추가됩니다. `.\logs\stdout`이 값으로 제공되는 경우 예제 stdout 로그는 2018년 2월 5일 19시 41분 32초에 프로세스 ID 1934를 사용하여 저장될 경우 `logs` 폴더에 `stdout_20180205194132_1934.log`로 저장됩니다.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>환경 변수 설정

`processPath` 특성에서 프로세스에 대한 환경 변수를 지정할 수 있습니다. `<environmentVariables>` 컬렉션 요소의 `<environmentVariable>` 자식 요소를 사용하여 환경 변수를 지정합니다. 이 섹션에 설정된 환경 변수가 시스템 환경 변수보다 우선 적용됩니다.

다음 예제에서는 두 개의 환경 변수를 설정합니다. `ASPNETCORE_ENVIRONMENT`는 앱의 환경을 `Development`로 구성합니다. 앱 예외를 디버그할 때 [개발자 예외 페이지](xref:fundamentals/error-handling)를 강제로 로드하기 위해 개발자가 `web.config` 파일에서 이 값을 일시적으로 설정할 수 있습니다. `CONFIG_DIR`은 개발자가 앱 구성 파일을 로드할 경로를 생성하기 위해 시작 시 값을 읽는 코드를 작성한 사용자 정의 환경 변수의 예입니다.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> `web.config`에서 환경을 직접 설정하는 대안으로 [게시 프로필(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 `<EnvironmentName>` 속성을 포함합니다. 이 방법은 프로젝트가 게시될 때 `web.config`에 환경을 설정합니다.
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> 인터넷과 같은 신뢰할 수 없는 네트워크에 액세스할 수 없는 스테이징 및 테스트 서버에서는 `ASPNETCORE_ENVIRONMENT` 환경 변수를 `Development`로 설정하면 됩니다.

## <a name="app_offlinehtm"></a>app_offline.htm

응용 프로그램의 루트 디렉터리에서 이름이 `app_offline.htm`인 파일이 검색되면 ASP.NET Core 모듈은 앱을 자동으로 종료하고 들어오는 요청 처리를 중지하려고 합니다. `shutdownTimeLimit`에 정의된 시간(초) 후에도 앱이 계속 실행되면 ASP.NET Core 모듈은 실행 중인 프로세스를 종료합니다.

`app_offline.htm` 파일이 있는 동안 ASP.NET Core 모듈은 `app_offline.htm` 파일의 콘텐츠를 다시 보내 요청에 응답합니다. `app_offline.htm` 파일이 제거되면 다음 요청이 앱을 시작합니다.

Out-of-Process 호스팅 모델을 사용할 때 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다. 예를 들어, WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.

## <a name="start-up-error-page"></a>시작 오류 페이지

In process 및 out of process 호스팅은 모두 앱을 시작하지 못할 때 사용자 지정 오류 페이지를 생성합니다.

ASP.NET Core 모듈이 in-process 또는 out-of-process 요청 처리기를 찾지 못하면 *500.0 - In-Process/Out-of-process 처리기 로드 실패* 상태 코드 페이지가 나타납니다.

in-process 호스팅의 경우 ASP.NET Core 모듈이 앱을 시작하지 못하면 *500.30 - 시작 실패* 상태 코드 페이지가 나타납니다.

out-of-process 호스팅의 경우 ASP.NET Core 모듈이 백 엔드 프로세스를 시작하지 못하거나 백 엔드 프로세스가 시작되지만 구성된 포트에서 수신 대기하지 못하면 *502.5 - 프로세스 실패* 상태 코드 페이지가 나타납니다.

이 페이지를 표시하지 않고 기본 IIS 5xx 상태 코드 페이지로 되돌리려면 `disableStartUpErrorPage` 특성을 사용합니다. 사용자 지정 오류 메시지 구성에 대한 자세한 내용은 [HTTP 오류\<httpErrors>](/iis/configuration/system.webServer/httpErrors/)를 참조하세요.

## <a name="log-creation-and-redirection"></a>로그 만들기 및 리디렉션

ASP.NET Core 모듈은 `aspNetCore` 요소의 `stdoutLogEnabled` 및 `stdoutLogFile` 특성이 설정된 경우 stdout 및 stderr 콘솔 출력을 디스크로 리디렉션합니다. `stdoutLogFile` 경로의 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다. 앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\{APP POOL NAME}`를 사용하여 쓰기 권한 제공, 여기서 자리 표시자 `{APP POOL NAME}`는 앱 풀 이름임).

프로세스 재생/다시 시작이 발생하지 않는 한 로그는 회전되지 않습니다. 로그에서 사용하는 디스크 공간을 제한하는 것은 호스터의 책임입니다.

stdout 로그는 IIS에서 호스팅할 때 또는 [Visual Studio에서 IIS를 위한 개발 시간 지원](xref:host-and-deploy/iis/development-time-iis-support)을 사용할 때 앱 시작 문제를 해결하기 위해서만 사용하는 것이 좋으며, 로컬에서 디버깅하면서 IIS Express를 사용하여 앱을 실행할 때는 권장되지 않습니다.

일반 앱 로깅을 위해 stdout 로그를 사용하지 마세요. ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

로그 파일이 만들어질 때 타임스탬프 및 파일 확장명이 자동으로 추가됩니다. 로그 파일 이름은 타임스탬프, 프로세스 ID 및 파일 확장명( `.log`)을 밑줄로 구분된 `stdoutLogFile` 경로의 마지막 세그먼트(일반적으로 `stdout`)에 추가하여 작성됩니다. `stdoutLogFile` 경로가 `stdout`으로 끝나는 경우 2018년 2월 5일 19시 42분 32초에 만들어진 PID 1934를 사용하는 앱에 대한 로그의 파일 이름은 `stdout_20180205194132_1934.log`입니다.

`stdoutLogEnabled`가 false이면 앱 시작 시 발생하는 오류가 캡처되어 최대 30KB의 이벤트 로그로 내보냅니다. 시작 후에는 모든 추가 로그가 삭제됩니다.

다음 샘플 `aspNetCore` 요소는 상대 경로 `.\log\`에서 stdout 로깅을 구성합니다. 앱 풀 사용자 ID에 제공된 경로에 쓸 수 있는 권한이 있는지 확인합니다.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Azure App Service 배포를 위해 앱을 게시할 때는 웹 SDK가 `stdoutLogFile` 값을 `\\?\%home%\LogFiles\stdout`으로 설정합니다. `%home` 환경 변수는 Azure App Service에 의해 호스팅되는 앱에 대해 미리 정의됩니다.

경로 형식에 대한 자세한 내용은 [Windows 시스템의 파일 경로 형식](/dotnet/standard/io/file-path-formats)을 참조하세요.

## <a name="enhanced-diagnostic-logs"></a>개선된 진단 로그

ASP.NET Core 모듈은 개선된 진단 로그를 제공하도록 구성할 수 있습니다. `web.config`에서 `<handlerSettings>` 요소를 `<aspNetCore>` 요소에 추가합니다. `debugLevel`을 `TRACE`으로 설정하면 진단 정보의 충실도가 높아집니다.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

`<handlerSetting>` 값에 제공한 경로에 있는 폴더(위 예제의 `logs`)가 모듈에서 자동으로 생성되지 않으며, 배포에 있는 기존 폴더여야 합니다. 앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\{APP POOL NAME}`를 사용하여 쓰기 권한 제공, 여기서 자리 표시자 `{APP POOL NAME}`는 앱 풀 이름임).

디버그 수준 (`debugLevel`) 값은 수준과 위치를 모두 포함할 수 있습니다.

수준(최소한에서 가장 자세한 정보까지 순서대로 ):

* 오류
* 경고
* 정보
* TRACE

위치(여러 위치가 허용됨):

* CONSOLE
* EVENTLOG
* FILE

처리기 설정은 환경 변수를 통해서도 제공할 수 있습니다.

* `ASPNETCORE_MODULE_DEBUG_FILE`: 디버그 로그 파일의 경로입니다. (기본값: `aspnetcore-debug.log`)
* `ASPNETCORE_MODULE_DEBUG`: 디버그 수준 설정입니다.

> [!WARNING]
> 배포에서 문제를 해결하는 데 필요한 시간보다 오래 디버그 로깅을 사용하도록 설정하지 **마세요**. 로그의 크기는 제한되지 않습니다. 디버그 로그를 사용하도록 설정한 대로 두면 사용 가능한 디스크 공간이 소진되어 서버 또는 앱 서비스가 크래시될 수 있습니다.

`web.config` 파일에 있는 `aspNetCore` 요소의 예제에 대해서는 [web.config를 사용한 구성](#configuration-with-webconfig)을 참조하세요.

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>프록시 구성은 HTTP 프로토콜 및 페어링 토큰을 사용합니다.

*호스팅에만 적용됩니다.*

ASP.NET Core 모듈과 Kestrel 사이에 만들어진 프록시는 HTTP 프로토콜을 사용합니다. 서버에서 분리된 위치에서 모듈과 Kestrel 간 트래픽 도청의 위험은 없습니다.

페어링 토큰은 Kestrel에서 받은 요청이 IIS에서 프록시되었으며 다른 원본에서 오지 않았다는 것을 보장하는 데 사용됩니다. 페어링 토큰은 모듈에 의해 생성되며 환경 변수(`ASPNETCORE_TOKEN`)로 설정됩니다. 페어링 토큰은 프록시된 모든 요청에서 헤더(`MS-ASPNETCORE-TOKEN`)로도 설정됩니다. IIS 미들웨어는 수신하는 각 요청을 검사하여 페어링 토큰 헤더 값이 환경 변수 값과 일치하는지 확인합니다. 토큰 값이 일치하지 않는 경우 요청이 기록되고 거부됩니다. 페어링 토큰 환경 변수와 모듈과 Kestrel 간의 트래픽은 서버에서 분리된 위치에서 액세스할 수 없습니다. 페어링 토큰 값을 알지 못하면 공격자는 IIS 미들웨어에서 검사를 무시하는 요청을 전송할 수 없습니다.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS 공유 구성이 포함된 ASP.NET Core 모듈

ASP.NET Core 모듈 설치 관리자는 `TrustedInstaller` 계정의 권한으로 실행됩니다. 로컬 시스템 계정에는 IIS 공유 구성에서 사용하는 공유 경로에 대한 수정 권한이 없으므로 공유의 `applicationHost.config`에서 모듈 설정을 구성하려고 하면 설치 관리자에서 액세스 거부 오류가 throw됩니다.

IIS 설치와 동일한 머신에서 IIS 공유 구성을 사용하는 경우 `OPT_NO_SHARED_CONFIG_CHECK` 매개 변수를 `1`로 설정한 상태에서 ASP.NET Core Hosting Bundle 설치 관리자를 실행합니다

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

공유 구성에 대한 경로가 IIS 설치와 동일한 머신에 있지 않으면 다음 단계를 수행합니다.

1. IIS 공유 구성을 사용하지 않도록 설정합니다.
1. 설치 관리자를 실행합니다.
1. 업데이트된 `applicationHost.config` 파일을 공유로 내보냅니다.
1. IIS 공유 구성을 다시 사용하도록 설정합니다.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>모듈 버전 및 호스팅 번들 설치 관리자 로그

설치된 ASP.NET Core 모듈의 버전을 확인하려면:

1. 호스팅 시스템에서 `%windir%\System32\inetsrv`로 이동합니다.
1. `aspnetcore.dll` 파일을 찾습니다.
1. 파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성** 을 선택합니다.
1. **세부 정보** 탭을 선택합니다. **파일 버전** 및 **제품 버전** 은 설치된 모듈 버전을 나타냅니다.

모듈의 호스팅 번들 설치 관리자 로그는 `C:\\Users\\%UserName%\\AppData\\Local\\Temp`에 있습니다. 파일 이름은 `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`입니다. 여기서 자리 표시자 `{TIMESTAMP}`는 타임스탬프입니다.

## <a name="module-schema-and-configuration-file-locations"></a>모듈, 스키마 및 구성 파일 위치

### <a name="module"></a>Module

**IIS(x86/amd64)** :

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express(x86/amd64)** :

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>스키마

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>Configuration

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* *iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

`applicationHost.config` 파일에서 `aspnetcore`를 검색하여 파일을 찾을 수 있습니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

ASP.NET Core 모듈은 백 엔드 ASP.NET Core 앱으로 웹 요청을 전달하는 IIS 파이프라인에 연결되는 네이티브 IIS 모듈입니다.

지원되는 Windows 버전:

* Windows 7 이상
* Windows Server 2008 R2 이상

모듈은 Kestrel에서만 작동합니다. 모듈이 [HTTP.sys](xref:fundamentals/servers/httpsys)와 호환되지 않습니다.

ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 이 모듈은 프로세스 관리도 수행합니다. 이 모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 충돌이 발생하면 앱을 다시 시작합니다. 이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 IIS에서 In Process로 실행되는 ASP.NET 4.x 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.

다음 다이어그램은 IIS, ASP.NET Core 모듈 및 앱 간의 관계를 보여 줍니다.

![ASP.NET Core 모듈](iis/index/_static/ancm-outofprocess.png)

요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다. 드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다. 모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.

모듈은 시작 시 환경 변수를 통해 포트를 지정하고 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)는 `http://localhost:{port}`에서 수신 대기하도록 서버를 구성합니다. 추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다. 모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.

Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다. 미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다. IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다. 앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.

Windows 인증 등의 많은 네이티브 모듈이 활성 상태로 유지됩니다. ASP.NET Core 모듈을 사용하여 활성화된 IIS 모듈에 대해 자세히 알아보려면 <xref:host-and-deploy/iis/modules>를 참조하세요.

ASP.NET Core 모듈은 다음 작업을 수행할 수도 있습니다.

* 작업자 프로세스의 환경 변수를 설정합니다.
* 시작 문제를 해결하기 위해 stdout 출력을 파일 스토리지에 기록합니다.
* Windows 인증 토큰을 전달합니다.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Core 모듈을 설치하고 사용하는 방법

ASP.NET Core 모듈을 설치하는 방법에 대한 지침은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.

## <a name="configuration-with-webconfig"></a>web.config를 사용한 구성

ASP.NET Core 모듈은 사이트의 *web.config* 파일에 있는 `system.webServer` 노드의 `aspNetCore` 섹션으로 구성됩니다.

다음 *web.config* 파일은 [프레임워크 종속 배포](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)를 위해 게시되고 사이트 요청을 처리하도록 ASP.NET Core 모듈을 구성합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

다음 *web.config* 는 [자체 포함 배포](/dotnet/articles/core/deploying/#self-contained-deployments-scd)를 위해 게시됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

앱이 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포되면 `stdoutLogFile` 경로가 `\\?\%home%\LogFiles\stdout`로 설정됩니다. 이 경로는 서비스에서 자동으로 만들어진 위치인 *LogFiles* 폴더에 stdout 로그를 저장합니다.

IIS 하위 애플리케이션 구성에 대한 자세한 내용은 <xref:host-and-deploy/iis/index#sub-applications>를 참조하세요.

### <a name="attributes-of-the-aspnetcore-element"></a>aspNetCore 요소의 특성

| 특성 | 설명 | 기본값 |
| --------- | ----------- | :-----: |
| `arguments` | <p>선택적 문자열 특성입니다.</p><p>**processPath** 에 지정된 실행 파일에 대한 인수입니다.</p>| |
| `disableStartUpErrorPage` | <p>선택적 부울 특성입니다.</p><p>true인 경우 **502.5 - 프로세스 실패** 페이지가 표시되지 않고 *web.config* 에 구성된 502 상태 코드 페이지가 우선 적용됩니다.</p> | `false` |
| `forwardWindowsAuthToken` | <p>선택적 부울 특성입니다.</p><p>true인 경우 토큰은 %ASPNETCORE_PORT%에서 수신 대기하는 자식 프로세스에 요청별 헤더 'MS-ASPNETCORE-WINAUTHTOKEN'으로 전달됩니다. 이 프로세스는 요청별로 이 토큰에서 CloseHandle을 호출합니다.</p> | `true` |
| `processesPerApplication` | <p>선택적 정수 특성입니다.</p><p>앱별로 스핀 업할 수 있는 **processPath** 설정에 지정된 프로세스의 인스턴스 수를 지정합니다.</p><p>설정 `processesPerApplication`은 권장되지 않습니다. 이 특성은 이후 릴리스에서 제거됩니다.</p> | 기본값: `1`<br>최소: `1`<br>최대: `100` |
| `processPath` | <p>필수 문자열 특성입니다.</p><p>HTTP 요청을 수신 대기하는 프로세스를 시작하는 실행 파일의 경로입니다. 상대 경로가 지원됩니다. 경로가 `.`로 시작되면 경로는 사이트 루트의 상대 경로로 간주됩니다.</p> | |
| `rapidFailsPerMinute` | <p>선택적 정수 특성입니다.</p><p>**processPath** 에 지정된 프로세스의 분당 크래시 허용 횟수를 지정합니다. 이 제한을 초과하면 모듈은 남은 시간 동안 프로세스 시작을 중지합니다.</p> | 기본값: `10`<br>최소: `0`<br>최대: `100` |
| `requestTimeout` | <p>선택적 시간 간격 특성입니다.</p><p>ASP.NET Core 모듈이 %ASPNETCORE_PORT%에서 수신 대기하는 프로세스의 응답을 기다리는 기간을 지정합니다.</p><p>ASP.NET Core 2.1 이상 릴리스와 함께 제공되는 ASP.NET Core 모듈 버전에서는 `requestTimeout`이 전체 시간, 분, 초로 지정됩니다.</p> | 기본값: `00:02:00`<br>최소: `00:00:00`<br>최대: `360:00:00` |
| `shutdownTimeLimit` | <p>선택적 정수 특성입니다.</p><p>*app_offline.htm* 파일이 검색될 때 실행 파일이 정상적으로 종료될 때까지 모듈이 기다리는 기간(초)입니다.</p> | 기본값: `10`<br>최소: `0`<br>최대: `600` |
| `startupTimeLimit` | <p>선택적 정수 특성입니다.</p><p>실행 파일이 포트에서 수신 대기하는 프로세스를 시작할 때까지 모듈이 기다리는 기간(초)입니다. 이 시간 제한을 초과하면 모듈이 프로세스를 종료합니다. 모듈은 새 요청을 수신할 때 프로세스를 다시 시작하려고 하고, 마지막 롤링 기간(분)에 앱이 **rapidFailsPerMinute** 번 시작에 실패한 경우가 아니면 이후 요청이 들어올 때 프로세스를 계속 다시 시작하려고 합니다.</p><p>값 0은 무한 시간 제한으로 간주되지 **않습니다**.</p> | 기본값: `120`<br>최소: `0`<br>최대: `3600` |
| `stdoutLogEnabled` | <p>선택적 부울 특성입니다.</p><p>true인 경우 **processPath** 에 지정된 프로세스에 대한 **stdout** 및 **stderr** 이 **stdoutLogFile** 에 지정된 파일로 리디렉션됩니다.</p> | `false` |
| `stdoutLogFile` | <p>선택적 문자열 특성입니다.</p><p>**processPath** 에 지정된 프로세스에서 **stdout** 및 **stderr** 이 기록되는 상대 또는 절대 파일 경로를 지정합니다. 상대 경로는 사이트 루트에 상대적인 경로입니다. `.`로 시작하는 모든 경로는 사이트 루트에 상대적인 경로이고 다른 모든 경로는 절대 경로로 처리됩니다. 모듈이 로그 파일을 만들려면 경로에 제공된 모든 폴더가 있어야 합니다. 타임스탬프, 프로세스 ID 및 파일 확장명( *.log*)은 밑줄 구분 기호를 사용하여 **stdoutLogFile** 경로의 마지막 세그먼트에 추가됩니다. `.\logs\stdout`이 값으로 제공되는 경우 예제 stdout 로그는 2018년 2월 5일 19시 41분 32초에 프로세스 ID 1934를 사용하여 저장될 경우 *logs* 폴더에 *stdout_20180205194132_1934.log* 로 저장됩니다.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>환경 변수 설정

`processPath` 특성에서 프로세스에 대한 환경 변수를 지정할 수 있습니다. `<environmentVariables>` 컬렉션 요소의 `<environmentVariable>` 자식 요소를 사용하여 환경 변수를 지정합니다.

> [!WARNING]
> 이 섹션에 설정된 환경 변수가 동일한 이름으로 설정된 시스템 환경 변수와 충돌합니다. 환경 변수가 *web.config* 파일과 Windows의 시스템 수준에 모두 설정된 경우 *web.config* 파일의 값은 시스템 환경 변수 값(예: `ASPNETCORE_ENVIRONMENT: Development;Development`)에 추가되어 앱이 시작되지 않습니다.

다음 예제에서는 두 개의 환경 변수를 설정합니다. `ASPNETCORE_ENVIRONMENT`는 앱의 환경을 `Development`로 구성합니다. 앱 예외를 디버그할 때 [개발자 예외 페이지](xref:fundamentals/error-handling)를 강제로 로드하기 위해 개발자가 *web.config* 파일에서 이 값을 일시적으로 설정할 수 있습니다. `CONFIG_DIR`은 개발자가 앱 구성 파일을 로드할 경로를 생성하기 위해 시작 시 값을 읽는 코드를 작성한 사용자 정의 환경 변수의 예입니다.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> 인터넷과 같은 신뢰할 수 없는 네트워크에 액세스할 수 없는 스테이징 및 테스트 서버에서는 `ASPNETCORE_ENVIRONMENT` 환경 변수를 `Development`로 설정하면 됩니다.

## <a name="app_offlinehtm"></a>app_offline.htm

응용 프로그램의 루트 디렉터리에서 이름이 *app_offline.htm* 인 파일이 검색되면 ASP.NET Core 모듈은 앱을 자동으로 종료하고 들어오는 요청 처리를 중지하려고 합니다. `shutdownTimeLimit`에 정의된 시간(초) 후에도 앱이 계속 실행되면 ASP.NET Core 모듈은 실행 중인 프로세스를 종료합니다.

*app_offline.htm* 파일이 있는 동안 ASP.NET Core 모듈은 *app_offline.htm* 파일의 콘텐츠를 다시 보내 요청에 응답합니다. *app_offline.htm* 파일이 제거되면 다음 요청이 앱을 시작합니다.

## <a name="start-up-error-page"></a>시작 오류 페이지

ASP.NET Core 모듈이 백 엔드 프로세스를 시작하지 못하거나 백 엔드 프로세스가 시작되지만 구성된 포트에서 수신 대기하지 못하면 *502.5 - 프로세스 실패* 상태 코드 페이지가 나타납니다. 이 페이지를 표시하지 않고 기본 IIS 502 상태 코드 페이지로 되돌리려면 `disableStartUpErrorPage` 특성을 사용합니다. 사용자 지정 오류 메시지 구성에 대한 자세한 내용은 [HTTP 오류\<httpErrors>](/iis/configuration/system.webServer/httpErrors/)를 참조하세요.

## <a name="log-creation-and-redirection"></a>로그 만들기 및 리디렉션

ASP.NET Core 모듈은 `aspNetCore` 요소의 `stdoutLogEnabled` 및 `stdoutLogFile` 특성이 설정된 경우 stdout 및 stderr 콘솔 출력을 디스크로 리디렉션합니다. `stdoutLogFile` 경로의 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다. 앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\<app_pool_name>`을 사용하여 쓰기 권한 제공).

프로세스 재생/다시 시작이 발생하지 않는 한 로그는 회전되지 않습니다. 로그에서 사용하는 디스크 공간을 제한하는 것은 호스터의 책임입니다.

stdout 로그는 IIS에서 호스팅할 때 또는 [Visual Studio에서 IIS를 위한 개발 시간 지원](xref:host-and-deploy/iis/development-time-iis-support)을 사용할 때 앱 시작 문제를 해결하기 위해서만 사용하는 것이 좋으며, 로컬에서 디버깅하면서 IIS Express를 사용하여 앱을 실행할 때는 권장되지 않습니다.

일반 앱 로깅을 위해 stdout 로그를 사용하지 마세요. ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

로그 파일이 만들어질 때 타임스탬프 및 파일 확장명이 자동으로 추가됩니다. 로그 파일 이름은 타임스탬프, 프로세스 ID 및 파일 확장명( *.log*)을 밑줄로 구분된 `stdoutLogFile` 경로의 마지막 세그먼트(일반적으로 *stdout*)에 추가하여 작성됩니다. `stdoutLogFile` 경로가 *stdout* 으로 끝나는 경우 2018년 2월 5일 19시 42분 32초에 만들어진 PID 1934를 사용하는 앱에 대한 로그의 파일 이름은 *stdout_20180205194132_1934.log* 입니다.

다음 샘플 `aspNetCore` 요소는 상대 경로 `.\log\`에서 stdout 로깅을 구성합니다. AppPool 사용자 ID에 제공된 경로에 쓸 수 있는 권한이 있는지 확인합니다.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

Azure App Service 배포를 위해 앱을 게시할 때는 웹 SDK가 `stdoutLogFile` 값을 `\\?\%home%\LogFiles\stdout`으로 설정합니다. `%home` 환경 변수는 Azure App Service에 의해 호스팅되는 앱에 대해 미리 정의됩니다.

로깅 필터 규칙을 만들려면 ASP.NET Core 로깅 설명서의 [구성](xref:fundamentals/logging/index#log-filtering) 섹션과 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 섹션을 참조하세요.

경로 형식에 대한 자세한 내용은 [Windows 시스템의 파일 경로 형식](/dotnet/standard/io/file-path-formats)을 참조하세요.

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>프록시 구성은 HTTP 프로토콜 및 페어링 토큰을 사용합니다.

ASP.NET Core 모듈과 Kestrel 사이에 만들어진 프록시는 HTTP 프로토콜을 사용합니다. 서버에서 분리된 위치에서 모듈과 Kestrel 간 트래픽 도청의 위험은 없습니다.

페어링 토큰은 Kestrel에서 받은 요청이 IIS에서 프록시되었으며 다른 원본에서 오지 않았다는 것을 보장하는 데 사용됩니다. 페어링 토큰은 모듈에 의해 생성되며 환경 변수(`ASPNETCORE_TOKEN`)로 설정됩니다. 페어링 토큰은 프록시된 모든 요청에서 헤더(`MS-ASPNETCORE-TOKEN`)로도 설정됩니다. IIS 미들웨어는 수신하는 각 요청을 검사하여 페어링 토큰 헤더 값이 환경 변수 값과 일치하는지 확인합니다. 토큰 값이 일치하지 않는 경우 요청이 기록되고 거부됩니다. 페어링 토큰 환경 변수와 모듈과 Kestrel 간의 트래픽은 서버에서 분리된 위치에서 액세스할 수 없습니다. 페어링 토큰 값을 알지 못하면 공격자는 IIS 미들웨어에서 검사를 무시하는 요청을 전송할 수 없습니다.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS 공유 구성이 포함된 ASP.NET Core 모듈

ASP.NET Core 모듈 설치 관리자는 **TrustedInstaller** 계정의 권한으로 실행됩니다. 로컬 시스템 계정에는 IIS 공유 구성에서 사용하는 공유 경로에 대한 수정 권한이 없으므로 공유의 *applicationHost.config* 에서 모듈 설정을 구성하려고 하면 설치 관리자에서 액세스 거부 오류가 throw됩니다.

IIS 공유 구성을 사용할 경우 다음 단계를 수행합니다.

1. IIS 공유 구성을 사용하지 않도록 설정합니다.
1. 설치 관리자를 실행합니다.
1. 업데이트된 *applicationHost.config* 파일을 공유로 내보냅니다.
1. IIS 공유 구성을 다시 사용하도록 설정합니다.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>모듈 버전 및 호스팅 번들 설치 관리자 로그

설치된 ASP.NET Core 모듈의 버전을 확인하려면:

1. 호스팅 시스템에서 *%windir%\System32\inetsrv* 로 이동합니다.
1. *aspnetcore.dll* 파일을 찾습니다.
1. 파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성** 을 선택합니다.
1. **세부 정보** 탭을 선택합니다. **파일 버전** 및 **제품 버전** 은 설치된 모듈 버전을 나타냅니다.

모듈에 대한 호스팅 번들 설치 관리자 로그는 *C:\\Users\\%UserName%\\AppData\\Local\\Temp* 에 있습니다. 파일 이름은 *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log* 로 지정됩니다.

## <a name="module-schema-and-configuration-file-locations"></a>모듈, 스키마 및 구성 파일 위치

### <a name="module"></a>Module

**IIS(x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express(x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

### <a name="schema"></a>스키마

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>Configuration

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

*applicationHost.config* 파일에서 *aspnetcore* 를 검색하여 파일을 찾을 수 있습니다.

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* [ASP.NET Core 모듈 참조 원본[기본 분기(main)]](https://github.com/dotnet/aspnetcore/tree/main/src/Servers/IIS/AspNetCoreModuleV2): **분기** 드롭다운 목록을 사용하여 특정 릴리스를 선택할 수 있습니다(예: `release/3.1`).
* <xref:host-and-deploy/iis/modules>
