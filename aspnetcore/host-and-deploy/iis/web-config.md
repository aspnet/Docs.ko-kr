---
title: web.config 파일
author: rick-anderson
description: web.config 파일의 내부에 있는 내용과 다양한 ASP.NET Core 모듈 옵션을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: 4d7305f7184745b66c5de6c86b907d419183cb3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755192"
---
# <a name="webconfig-file"></a>`web.config` 파일:

`web.config`는 IIS에서 호스트되는 앱을 구성하기 위해 IIS 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 읽는 파일입니다.

## <a name="webconfig-file-location"></a>`web.config` 파일 위치

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 `web.config` 파일이 있어야 합니다. IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다. 웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 `web.config` 파일이 있어야 합니다.

중요한 파일은 `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml`(XML 문서 주석) 및 `{ASSEMBLY}.deps.json`과 같은 앱의 실제 경로에 있습니다. 여기서 자리 표시자 `{ASSEMBLY}`는 어셈플리 이름입니다. `web.config` 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다. `web.config` 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.

**`web.config` 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 `web.config` 파일을 제거하지 마세요.**

프로젝트에 `web.config` 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 `processPath` 및 `arguments` 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.

프로젝트에 `web.config` 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 `processPath` 및 `arguments`로 파일이 변환되어 게시된 출력으로 이동됩니다. 변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.

`web.config` 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다. ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.

`web.config` 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다. 이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다. SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

웹 SDK가 `web.config` 파일을 변환하지 못하도록 하려면 프로젝트 파일의 `<IsTransformWebConfigDisabled>` 속성을 사용합니다.

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 `processPath` 및 `arguments`를 수동으로 설정해야 합니다. 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a>`web.config`를 사용하여 ASP.NET Core 모듈 구성

ASP.NET Core 모듈은 사이트의 `web.config` 파일에 있는 `system.webServer` 노드의 `aspNetCore` 섹션으로 구성됩니다.

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

다음 `web.config`는 [자체 포함 배포](/dotnet/articles/core/deploying/#self-contained-deployments-scd)를 위해 게시됩니다.

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

<xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> 속성이 `false`로 설정되어 [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 요소 내에서 지정된 설정이 하위 디렉터리에 있는 앱에 상속되지 않음을 나타냅니다.

앱이 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포되면 `stdoutLogFile` 경로가 `\\?\%home%\LogFiles\stdout`로 설정됩니다. 이 경로는 서비스에서 자동으로 만들어진 위치인 `LogFiles` 폴더에 stdout 로그를 저장합니다.

IIS 하위 애플리케이션 구성에 대한 자세한 내용은 <xref:host-and-deploy/iis/index#sub-applications>를 참조하세요.

### <a name="attributes-of-the-aspnetcore-element"></a>`aspNetCore` 요소의 특성

| attribute | 설명 | 기본값 |
| --------- | ----------- | :-----: |
| `arguments` | <p>선택적 문자열 특성입니다.</p><p>`processPath`에 지정된 실행 파일에 대한 인수입니다.</p> | |
| `disableStartUpErrorPage` | <p>선택적 부울 특성입니다.</p><p>true인 경우 **502.5 - 프로세스 실패** 페이지가 표시되지 않고 `web.config`에 구성된 502 상태 코드 페이지가 우선 적용됩니다.</p> | `false` |
| `forwardWindowsAuthToken` | <p>선택적 부울 특성입니다.</p><p>true인 경우 토큰은 `%ASPNETCORE_PORT%`에서 수신 대기하는 자식 프로세스에 요청별 헤더 ‘MS-ASPNETCORE-WINAUTHTOKEN’으로 전달됩니다. 이 프로세스는 요청별로 이 토큰에서 CloseHandle을 호출합니다.</p> | `true` |
| `hostingModel` | <p>선택적 문자열 특성입니다.</p><p>호스팅 모델을 In-Process(`InProcess`/`inprocess`) 또는 Out-of-Process(`OutOfProcess`/`outofprocess`)로 지정합니다.</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>선택적 정수 특성입니다.</p><p>앱별로 스핀 업할 수 있는 `processPath` 설정에 지정된 프로세스의 인스턴스 수를 지정합니다.</p><p>&dagger;In-Process 호스팅의 경우 이 값은 `1`로 제한됩니다.</p><p>설정 `processesPerApplication`은 권장되지 않습니다. 이 특성은 이후 릴리스에서 제거됩니다.</p> | 기본값: `1`<br>최소: `1`<br>최대: `100`&dagger; |
| `processPath` | <p>필수 문자열 특성입니다.</p><p>HTTP 요청을 수신 대기하는 프로세스를 시작하는 실행 파일의 경로입니다. 상대 경로가 지원됩니다. 경로가 `.`로 시작되면 경로는 사이트 루트의 상대 경로로 간주됩니다.</p> | |
| `rapidFailsPerMinute` | <p>선택적 정수 특성입니다.</p><p>`processPath`에 지정된 프로세스의 분당 크래시 허용 횟수를 지정합니다. 이 제한을 초과하면 모듈은 남은 시간 동안 프로세스 시작을 중지합니다.</p><p>In-Process 호스팅에서는 지원되지 않습니다.</p> | 기본값: `10`<br>최소: `0`<br>최대: `100` |
| `requestTimeout` | <p>선택적 시간 간격 특성입니다.</p><p>ASP.NET Core 모듈이 %ASPNETCORE_PORT%에서 수신 대기하는 프로세스의 응답을 기다리는 기간을 지정합니다.</p><p>ASP.NET Core 2.1 이상 릴리스와 함께 제공되는 ASP.NET Core 모듈 버전에서는 `requestTimeout`이 전체 시간, 분, 초로 지정됩니다.</p><p>In-Process 호스팅에는 적용되지 않습니다. In-Process 호스팅의 경우 모듈은 앱이 요청을 처리할 때까지 기다립니다.</p><p>문자열의 분 및 초 세그먼트에 유효한 값은 0-59 범위입니다. 분 또는 초의 값에 `60`을 사용하면 *500 - 내부 서버 오류*가 발생됩니다.</p> | 기본값: `00:02:00`<br>최소: `00:00:00`<br>최대: `360:00:00` |
| `shutdownTimeLimit` | <p>선택적 정수 특성입니다.</p><p>`app_offline.htm` 파일이 검색될 때 실행 파일이 정상적으로 종료될 때까지 모듈이 기다리는 기간(초)입니다.</p> | 기본값: `10`<br>최소: `0`<br>최대: `600` |
| `startupTimeLimit` | <p>선택적 정수 특성입니다.</p><p>실행 파일이 포트에서 수신 대기하는 프로세스를 시작할 때까지 모듈이 기다리는 기간(초)입니다. 이 시간 제한을 초과하면 모듈이 프로세스를 종료합니다.</p><p>*In Process*를 호스트하는 경우: 프로세스가 다시 시작되지 **않고** `rapidFailsPerMinute` 설정을 사용하지 **않습니다**.</p><p>*Out-of-Process*를 호스트하는 경우: 모듈은 새 요청을 수신할 때 프로세스를 다시 시작하려고 하고, 마지막 롤링 기간(분)에 앱이 `rapidFailsPerMinute`번 시작에 실패한 경우가 아니면 이후 요청이 들어올 때 프로세스를 계속 다시 시작하려고 합니다.</p><p>값 0은 무한 시간 제한으로 간주되지 **않습니다**.</p> | 기본값: `120`<br>최소: `0`<br>최대: `3600` |
| `stdoutLogEnabled` | <p>선택적 부울 특성입니다.</p><p>true인 경우 `processPath`에 지정된 프로세스에 대한 `stdout` 및 `stderr`이 `stdoutLogFile`에 지정된 파일로 리디렉션됩니다.</p> | `false` |
| `stdoutLogFile` | <p>선택적 문자열 특성입니다.</p><p>`processPath`에 지정된 프로세스에서 `stdout` 및 `stderr`이 기록되는 상대 또는 절대 파일 경로를 지정합니다. 상대 경로는 사이트 루트에 상대적인 경로입니다. `.`로 시작하는 모든 경로는 사이트 루트에 상대적인 경로이고 다른 모든 경로는 절대 경로로 처리됩니다. 경로에 제공된 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다. 타임스탬프, 프로세스 ID 및 파일 확장명( `.log`)은 밑줄 구분 기호를 사용하여 `stdoutLogFile` 경로의 마지막 세그먼트에 추가됩니다. `.\logs\stdout`이 값으로 제공되는 경우 예제 stdout 로그는 2018년 2월 5일 19시 41분 32초에 프로세스 ID 1934를 사용하여 저장될 경우 `logs` 폴더에 `stdout_20180205194132_1934.log`로 저장됩니다.</p> | `aspnetcore-stdout` |

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

## <a name="configuration-of-iis-with-webconfig"></a>`web.config`를 사용하여 IIS 구성

IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 `web.config`에 포함된 `<system.webServer>` 섹션의 영향을 받습니다. 예를 들어, IIS 구성은 동적 압축에 대해 작동합니다. IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 `web.config` 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.

자세한 내용은 다음 항목을 참조하세요.

* [`<system.webServer>`의 구성 참조](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 `<environmentVariables>`](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘`AppCmd.exe` 명령’ 섹션을 참조하세요.

## <a name="configuration-sections-of-webconfig"></a>`web.config`의 구성 섹션

`web.config`에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다. 자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.

## <a name="transform-webconfig"></a>web.config 변환

게시할 때 `web.config`를 변환해야 하는 경우 <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요. 구성, 프로필 또는 환경을 기반으로 하는 환경 변수를 설정하려면 게시할 때 `web.config`를 변환해야 할 수도 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [IIS \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
