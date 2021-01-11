---
title: web.config 변환
author: rick-anderson
description: ASP.NET Core 앱을 게시할 때 web.config 파일을 변환하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
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
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: d264aaee7889ec1c8ee0fe6b1f52ccc4cf355745
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854628"
---
# <a name="transform-webconfig"></a>web.config 변환

작성자: [Vijay Ramakrishnan](https://github.com/vijayrkn)

다음을 기반으로 앱을 게시할 때 *web.config* 파일의 변환을 자동으로 적용할 수 있습니다.

* [빌드 구성](#build-configuration)
* [Profile](#profile)
* [환경](#environment)
* [사용자 지정](#custom)

이 변환은 다음 *web.config* 생성 시나리오 중 하나에 대해 발생합니다.

* `Microsoft.NET.Sdk.Web` SDK에서 자동으로 생성되는 경우
* 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root)에서 개발자가 제공하는 경우

## <a name="build-configuration"></a>빌드 구성

빌드 구성 변환이 먼저 실행됩니다.

*web.config* 변환이 필요한 각 [빌드 구성(Debug|Release)](/dotnet/core/tools/dotnet-publish#options)에 사용할 *web.{CONFIGURATION}.config* 파일을 포함합니다.

다음 예제에서는 구성별 환경 변수가 *web.Release.config* 에서 설정됩니다.

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

구성이 *Release* 로 설정되면 변환이 적용됩니다.

```dotnetcli
dotnet publish --configuration Release
```

구성의 MSBuild 속성은 `$(Configuration)`입니다.

## <a name="profile"></a>프로필

프로필 변환은 [빌드 구성](#build-configuration) 변환 후에 두 번째로 실행됩니다.

*web.config* 변환이 필요한 각 프로필 구성에 사용할 *web.{PROFILE}.config* 파일을 포함합니다.

다음 예제에서는 프로필별 환경 변수가 폴더 게시 프로필에 대한 *web.FolderProfile.config* 에서 설정됩니다.

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

프로필이 *FolderProfile* 인 경우 변환이 적용됩니다.

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

프로필 이름의 MSBuild 속성은 `$(PublishProfile)`입니다.

프로필이 전달되지 않으면 기본 프로필 이름은 **FileSystem** 이고, 파일이 앱의 콘텐츠 루트에 있으면 *web.FileSystem.config* 가 적용됩니다.

## <a name="environment"></a>환경

환경 변환은 [빌드 구성](#build-configuration) 및 [프로필](#profile) 변환 후에 세 번째로 실행됩니다.

*web.config* 변환이 필요한 각 [환경](xref:fundamentals/environments)에 사용할 *web.{ENVIRONMENT}.config* 파일을 포함합니다.

다음 예제에서는 환경별 환경 변수가 프로덕션 환경의 *web.Production.config* 에서 설정됩니다.

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

환경이 *Production* 인 경우 변환이 적용됩니다.

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

환경의 MSBuild 속성은 `$(EnvironmentName)`입니다.

Visual Studio에서 게시하고 게시 프로필을 사용하는 경우 <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>을 참조하세요.

환경 이름이 지정되면 `ASPNETCORE_ENVIRONMENT` 환경 변수가 *web.config* 파일에 자동으로 추가됩니다.

## <a name="custom"></a>사용자 지정

사용자 지정 변환은 [빌드 구성](#build-configuration), [프로필](#profile) 및 [환경](#environment) 변환 후에 마지막으로 실행됩니다.

*web.config* 변환이 필요한 각 사용자 지정 구성에 사용할 *{CUSTOM_NAME}.transform* 파일을 포함합니다.

다음 예제에서는 사용자 지정 변환 환경 변수가 *custom.transform* 에서 설정됩니다.

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

`CustomTransformFileName` 속성이 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령에 전달되면 변환이 적용됩니다.

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

프로필 이름의 MSBuild 속성은 `$(CustomTransformFileName)`입니다.

## <a name="prevent-webconfig-transformation"></a>web.config 변환 방지

*web.config* 파일의 변환을 방지하려면 MSBuild 속성 `$(IsWebConfigTransformDisabled)`를 설정합니다.

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>추가 자료

* [웹 애플리케이션 프로젝트 배포를 위한 Web.config 변환 구문](/previous-versions/dd465326(v=vs.100))
* [Visual Studio를 사용하여 웹 프로젝트 배포를 위한 Web.config 변환 구문](/previous-versions/aspnet/dd465326(v=vs.110))
