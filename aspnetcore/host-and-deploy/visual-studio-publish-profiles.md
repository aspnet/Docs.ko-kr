---
title: ASP.NET Core 앱 배포용 Visual Studio 게시 프로필(.pubxml)
author: rick-anderson
description: Visual Studio에서 게시 프로필을 만들고 다양한 대상에 대한 ASP.NET Core 앱 배포를 관리하는 데 사용하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
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
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 98dfd539171807cbf94d4ac8746458152c809495
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057572"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>ASP.NET Core 앱 배포용 Visual Studio 게시 프로필(.pubxml)

작성자: [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 문서에서는 Visual Studio 2019 이상을 사용하여 게시 프로필을 만들고 사용하는 방법에 초점을 맞춥니다. Visual Studio로 만들어진 게시 프로필은 MSBuild 및 Visual Studio와 함께 사용될 수 있습니다. Azure에 게시하는 방법은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.

`dotnet new mvc` 명령은 다음 루트 수준 [\<Project> 요소](/visualstudio/msbuild/project-element-msbuild)가 포함된 프로젝트 파일을 생성합니다.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

위 `<Project>` 요소의 `Sdk` 특성은 각각 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* 및 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* 에서 MSBuild [속성](/visualstudio/msbuild/msbuild-properties) 및 [대상](/visualstudio/msbuild/msbuild-targets)을 가져옵니다. `$(MSBuildSDKsPath)`(Visual Studio 2019 Enterprise 사용)의 기본 위치는 *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* 폴더입니다.

`Microsoft.NET.Sdk.Web`([웹 SDK](xref:razor-pages/web-sdk))은 `Microsoft.NET.Sdk`([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) 및 `Microsoft.NET.Sdk.Razor`([Razor SDK](xref:razor-pages/sdk))를 비롯한 다른 SDK에 종속됩니다. 각 종속 SDK와 연결된 MSBuild 속성과 대상을 가져옵니다. 게시 대상은 사용된 게시 방법에 따라 해당 대상 집합을 가져옵니다.

MSBuild 또는 Visual Studio가 프로젝트를 로드하면 다음 높은 수준의 작업이 수행됩니다.

* 프로젝트 빌드
* 게시할 파일 컴퓨팅
* 대상에 파일 게시

## <a name="compute-project-items"></a>프로젝트 항목 컴퓨팅

프로젝트가 로드되면 [MSBuild 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items)(파일)이 컴퓨팅됩니다. 항목 종류에 따라 파일 처리 방법이 결정됩니다. 기본적으로 *.cs* 파일은 `Compile` 항목 목록에 포함됩니다. `Compile` 항목 목록의 파일이 컴파일됩니다.

`Content` 항목 목록에는 빌드 출력 이외에 게시될 파일이 포함됩니다. 기본적으로 `wwwroot\**`, `**\*.json` 및 `Content` 패턴과 일치하는 파일이 `**\*.config` 항목 목록에 포함됩니다. 예를 들어 `wwwroot\**` [와일드카드 사용 패턴](https://gruntjs.com/configuring-tasks#globbing-patterns)은 *wwwroot* 폴더 및 하위 폴더의 모든 파일과 일치합니다.

::: moniker range=">= aspnetcore-3.0"

[웹 SDK](xref:razor-pages/web-sdk)는 [Razor SDK](xref:razor-pages/sdk)를 가져옵니다. 따라서 `**\*.cshtml` 및 `**\*.razor` 패턴과 일치하는 파일도 `Content` 항목 목록에 포함됩니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[웹 SDK](xref:razor-pages/web-sdk)는 [Razor SDK](xref:razor-pages/sdk)를 가져옵니다. 따라서 `**\*.cshtml` 패턴과 일치하는 파일도 `Content` 항목 목록에 포함됩니다.

::: moniker-end

게시 목록에 파일을 명시적으로 추가하려면 [포함 파일](#include-files) 섹션에 표시된 대로 *.csproj* 파일에서 직접 파일을 추가합니다.

Visual Studio에서 **게시** 단추를 선택하거나 명령줄에서 게시할 경우:

* 속성/항목이 계산됩니다(빌드하는 데 필요한 파일).
* **Visual Studio 전용** : NuGet 패키지가 복원됩니다. (CLI에서 사용자가 명시적으로 복원해야 합니다.)
* 프로젝트가 빌드됩니다.
* 게시 항목이 계산됩니다(게시하는 데 필요한 파일).
* 프로젝트가 게시됩니다(계산된 파일이 게시 대상에 복사됨).

ASP.NET Core 프로젝트가 프로젝트 파일의 `Microsoft.NET.Sdk.Web`을 참조하면 *app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다. 파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다. 자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.

## <a name="basic-command-line-publishing"></a>기본 명령줄 게시

명령줄 게시는 모든 .NET Core 지원 플랫폼에 적용되며 Visual Studio가 필요하지 않습니다. 다음 예제에서 .NET COre CLI의 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 *.csproj* 파일이 포함된 프로젝트 디렉터리에서 실행됩니다. 프로젝트 폴더가 현재 작업 디렉터리가 아닌 경우 프로젝트 파일 경로에서 명시적으로 전달합니다. 예를 들어:

```dotnetcli
dotnet publish C:\Webs\Web1
```

다음 명령을 실행하여 웹앱을 만들고 게시합니다.

```dotnetcli
dotnet new mvc
dotnet publish
```

`dotnet publish` 명령은 다음 출력의 변형을 생성합니다.

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

기본 게시 폴더 형식은 *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\* 입니다. 예를 들어 *bin\Debug\netcoreapp2.2\publish\\* 입니다.

다음 명령은 `Release` 빌드 및 게시 디렉터리를 지정합니다.

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

`dotnet publish` 명령은 `Publish` 대상을 불러오는 MSBuild를 호출합니다. `dotnet publish` 및 MSBuild에 전달된 모든 매개 변수. `-c` 및 `-o` 매개 변수는 각각 MSBuild의 `Configuration` 및 `OutputPath` 속성에 매핑됩니다.

다음 형식 중 하나를 사용하여 MSBuild 속성을 전달할 수 있습니다.

* `-p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

예를 들어 다음 명령은 `Release` 빌드를 네트워크 공유에 게시합니다. 네트워크 공유는 슬래시( *//r8/* )를 사용하여 지정하고 모든 .NET Core 지원 플랫폼에서 작동합니다.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

배포할 게시된 앱이 실행되고 있지 않은지 확인합니다. 앱이 실행 중이면 *publish* 폴더의 파일이 잠겨 있습니다. 잠긴 파일을 복사할 수 없으므로 배포를 수행할 수 없습니다.

## <a name="publish-profiles"></a>게시 프로필

이 섹션에서는 Visual Studio 2019 이상을 사용하여 게시 프로필을 만듭니다. 프로필이 만들어지면 Visual Studio 또는 명령줄에서 게시할 수 있습니다. 게시 프로필을 사용하면 게시 프로세스를 간소화할 수 있고 제한 없이 프로필을 사용할 수 있습니다.

다음 경로 중 하나를 선택하여 Visual Studio에서 게시 프로필을 만듭니다.

* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.
* **빌드** 메뉴에서 **게시 {PROJECT NAME}** 를 선택합니다.

앱 기능 페이지의 **게시** 탭이 표시됩니다. 프로젝트에 게시 프로필에 없는 경우 **게시 대상 선택** 페이지가 표시됩니다. 다음 게시 대상 중 하나를 선택하라는 메시지가 나타납니다.

* Azure App Service
* Linux의 Azure App Service
* Azure Virtual Machines
* 폴더
* IIS, FTP, 웹 배포(모든 웹 서버에 해당)
* 프로필 가져오기

가장 적합한 게시 대상을 결정하려면 [내게 적합한 게시 옵션](/visualstudio/ide/not-in-toc/web-publish-options)을 참조하세요.

**폴더** 게시 대상을 선택하는 경우 게시된 자산을 저장할 폴더 경로를 지정합니다. 기본 폴더 경로는 *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\* 입니다. 예를 들어 *bin\Release\netcoreapp2.2\publish\\* 입니다. **프로필 만들기** 단추를 선택하여 완료합니다.

게시 프로필이 만들어지면 **게시** 탭의 콘텐츠가 변경됩니다. 새로 만들어진 프로필이 드롭다운 목록에 표시됩니다. 드롭다운 목록 아래에서 **새 프로필 만들기** 를 선택하여 다른 새 프로필을 만듭니다.

Visual Studio의 게시 도구는 게시 프로필을 설명하는 *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild 파일을 생성합니다. *.pubxml* 파일:

* 게시 구성 설정을 포함하며 게시 프로세스에서 사용됩니다.
* 수정하여 빌드 및 게시 프로세스를 사용자 지정할 수 있습니다.

Azure 대상에 게시하는 경우 *.pubxml* 파일에는 Azure 구독 식별자가 포함됩니다. 대상 유형을 사용할 경우 이 파일을 소스 제어에 추가하지 않는 것이 좋습니다. Azure 이외 대상에 게시하는 경우 *.pubxml* 파일을 체크 인해도 안전합니다.

중요 정보(예: 게시 암호)는 사용자/컴퓨터 수준별로 암호화됩니다. 이 정보는 *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* 파일에 저장됩니다. 중요 정보가 저장될 수 있는 파일이므로 소스 제어에 체크 인하면 안 됩니다.

ASP.NET Core 웹앱을 게시하는 방법에 대한 개요를 보려면 <xref:host-and-deploy/index>를 참조하세요. ASP.NET Core 웹앱을 게시하는 데 필요한 MSBuild 작업 및 대상은 [dotnet/websdk 리포지토리](https://github.com/dotnet/websdk)에 있는 오픈 소스입니다.

다음 명령은 MSDeploy 폴더와 [Kudu](https://github.com/projectkudu/kudu/wiki) 게시 프로필을 사용할 수 있습니다. MSDeploy는 플랫폼 간 지원을 하지 않으므로 다음 MSDeploy 옵션은 Windows에서만 지원됩니다.

**폴더(플랫폼 간에 작동):**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

**MSDeploy 패키지:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

앞의 예에서

* `dotnet publish` 및 `dotnet build`는 모든 플랫폼에서 Azure에 게시할 Kudu API를 지원합니다. Visual Studio 게시는 Kudu API를 지원하지만 플랫폼 간 Azure에 게시에 대해 WebSDK에 의해 지원됩니다.
* `dotnet publish` 명령에 `DeployOnBuild`를 전달하면 안 됩니다.

자세한 내용은 [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish)를 참조하세요.

다음 콘텐츠와 함께 프로젝트의 *Properties/PublishProfiles* 폴더에 게시 프로필을 추가합니다.

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a>폴더 게시 예

*FolderProfile* 이라는 프로필을 사용하여 게시할 경우 다음 명령 중 하나를 사용하세요.

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

.NET Core CLI의 [dotnet build](/dotnet/core/tools/dotnet-build) 명령은 `msbuild`를 호출하여 빌드 및 게시 프로세스를 실행합니다. 폴더 프로필을 전달할 경우 `dotnet build` 및 `msbuild` 명령은 동일합니다. Windows에서 `msbuild`를 직접 호출하면 MSBuild의 .NET Framework 버전이 사용됩니다. 폴더가 아닌 프로필에서 `dotnet build` 호출:

* MSDeploy를 사용하는 `msbuild`를 호출합니다.
* 오류가 발생합니다(Windows에서 실행하는 경우에도). 폴더가 아닌 프로필을 사용하여 게시하려면 `msbuild`를 직접 호출합니다.

다음 폴더 게시 프로필은 Visual Studio를 사용하여 만들어졌고 네트워크 공유에 게시됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

앞의 예제에서:

* `<ExcludeApp_Data>` 속성은 XML 스키마 요구 사항을 충족하기 위해서만 존재합니다. `<ExcludeApp_Data>` 속성은 프로젝트 루트에 *App_Data* 폴더가 있는 경우에도 게시 프로세스에 영향을 주지 않습니다. *App_Data* 폴더는 ASP.NET 4.x 프로젝트처럼 특별 처리를 받지 않습니다.
* `<LastUsedBuildConfiguration>` 속성은 `Release`로 설정됩니다. Visual Studio에서 게시할 경우 `<LastUsedBuildConfiguration>`의 값은 게시 프로세스가 시작될 때의 값을 사용하여 설정됩니다. `<LastUsedBuildConfiguration>`은 특별하고 가져온 MSBuild 파일에서 재정의되면 안 됩니다. 그러나 이 속성은 다음 중 한 가지 접근 방법을 사용하여 명령줄에서 재정의할 수 있습니다.
  * .NET Core CLI 사용:

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * MSBuild 사용:

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  자세한 내용은 [MSBuild: 구성 속성을 설정하는 방법](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)을 참조하세요.

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>명령줄에서 MSDeploy 엔드포인트에 게시

다음 예제에서는 *AzureWebApp* 라는 Visual Studio에서 만든 ASP.NET Core 웹앱을 사용합니다. Azure 게시 프로필이 Visual Studio에 추가되었습니다. 프로필을 만드는 방법에 대한 자세한 내용은 [게시 프로필](#publish-profiles) 섹션을 참조하세요.

게시 프로필을 사용하여 앱을 배포하려면 Visual Studio **개발자 명령 프롬프트** 에서 `msbuild` 명령을 실행합니다. 명령 프롬프트는 Windows 작업 표시줄의 **시작** 메뉴에 있는 *Visual Studio* 폴더에서 사용할 수 있습니다. 쉽게 액세스할 수 있도록 Visual Studio의 **도구** 메뉴에 명령 프롬프트를 추가할 수 있습니다. 자세한 내용은 [Visual Studio용 개발자 명령 프롬프트](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio)를 참조하세요.

MSBuild는 다음 명령 구문을 사용합니다.

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* `{PATH}`: 앱의 프로젝트 파일 경로입니다.
* `{PROFILE}`: 게시 프로필의 이름입니다.
* `{USERNAME}`: MSDeploy 사용자 이름입니다. `{USERNAME}`은 게시 프로필에서 찾을 수 있습니다.
* `{PASSWORD}`: MSDeploy 암호입니다. *{PROFILE}.PublishSettings* 파일에서 `{PASSWORD}`를 가져옵니다. 다음 위치에서 *.PublishSettings* 파일을 다운로드합니다.
  * **솔루션 탐색기** : **보기** > **클라우드 탐색기** 를 선택합니다. Azure 구독으로 연결합니다. **App Services** 를 엽니다. 앱을 마우스 오른쪽 단추로 클릭합니다. **게시 프로필 다운로드** 를 선택합니다.
  * Azure Portal: 웹앱의 **개요** 패널에서 **게시 프로필 가져오기** 를 선택합니다.

다음 예제에서는 *AzureWebApp - 웹 배포* 라는 게시 프로필을 사용합니다.

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

게시 프로필은 Windows 명령 셸에서 .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 명령과 함께 사용할 수도 있습니다.

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> `dotnet msbuild` 명령은 플랫폼 간 명령이며 macOS 및 Linux에서 ASP.NET Core 앱을 컴파일할 수 있습니다. 그러나 macOS 및 Linux의 MSBuild는 Azure 또는 다른 MSDeploy 엔드포인트에 앱을 배포할 수 없습니다.

## <a name="set-the-environment"></a>환경 설정

`<EnvironmentName>` 속성을 게시 프로필( *.pubxml* ) 또는 프로젝트 파일에 포함하여 앱의 [환경](xref:fundamentals/environments)을 설정합니다.

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

*web.config* 변환(예: 구성, 프로필 또는 환경에 따라 환경 변수 설정)이 필요한 경우 <xref:host-and-deploy/iis/transform-webconfig>을 참조하세요.

## <a name="exclude-files"></a>파일 제외

ASP.NET Core 웹앱을 게시하는 경우 다음 자산이 포함됩니다.

* 빌드 아티팩트
* 다음 와일드카드 사용 패턴과 일치하는 폴더 및 파일:
  * `**\*.config`(예: *web.config* )
  * `**\*.json`(예를 들어 *appsettings.json* )
  * `wwwroot\**`

MSBuild는 [와일드카드 사용 패턴](https://gruntjs.com/configuring-tasks#globbing-patterns)을 지원합니다. 예를 들어 다음 `<Content>` 요소는 *wwwroot\content* 폴더와 모든 하위 폴더에 있는 텍스트( *.txt* ) 파일의 복사를 표시하지 않습니다.

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

앞의 태그는 게시 프로필 또는 *.csproj* 파일에 추가될 수 있습니다. *.csproj* 파일에 추가된 규칙은 프로젝트의 모든 게시 프로필에 추가됩니다.

다음 `<MsDeploySkipRules>` 요소는 *wwwroot\content* 폴더의 모든 파일을 제외합니다.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>`는 배포 사이트에서 ‘건너뛰기’ 대상을 삭제하지 않습니다. `<Content>` 대상 파일 및 폴더는 배포 사이트에서 삭제됩니다. 예를 들어 배포된 웹앱에 다음 파일이 포함되었다고 가정합니다.

* *Views/Home/About1.cshtml*
* *Views/Home/About2.cshtml*
* *Views/Home/About3.cshtml*

다음 `<MsDeploySkipRules>` 요소가 추가되면 해당 파일은 배포 사이트에서 삭제되지 않습니다.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

앞의 `<MsDeploySkipRules>` 요소는 ‘건너뛴’ 파일이 배포되지 않도록 합니다. 해당 파일은 배포된 후에 삭제되지 않습니다.

다음 `<Content>` 요소는 배포 사이트에서 대상 파일을 삭제합니다.

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

앞의 `<Content>` 요소와 함께 명령줄 배포를 사용하면 다음 출력의 변형이 생성됩니다.

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a>포함 파일

다음 섹션에서는 게시 시점에서의 파일 포함을 위한 여러 가지 방법을 설명합니다. [일반 파일 포함](#general-file-inclusion) 섹션에서는 [웹 SDK](xref:razor-pages/web-sdk)의 게시 대상 파일이 제공하는 `DotNetPublishFiles` 항목을 사용합니다. [선택적 파일 포함](#selective-file-inclusion) 섹션에서는 [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)의 게시 대상 파일이 제공하는 `ResolvedFileToPublish` 항목을 사용합니다. Web SDK는 .NET Core SDK를 사용하기 때문에 ASP.NET Core 프로젝트에서 항목 중 하나를 사용할 수 있습니다.

### <a name="general-file-inclusion"></a>일반 파일 포함

다음 예제의 `<ItemGroup>` 요소는 프로젝트 디렉터리 밖에 위치한 폴더를 게시된 사이트의 폴더로 복사하는 방법을 보여줍니다. 다음 변경 내용의 `<ItemGroup>`에 추가된 모든 파일이 기본적으로 포함됩니다.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

위의 표시는:

* *.csproj* 파일 또는 게시 프로필에 추가할 수 있습니다. 태그가 *.csproj* 파일에 추가되면 프로젝트의 각 게시 프로필에 포함됩니다.
* `Include` 특성의 GLOB 패턴과 일치하는 파일을 저장할 `_CustomFiles` 항목을 선언합니다. 패턴에서 참조된 *images* 폴더는 프로젝트 디렉터리 밖에 위치합니다. 이름이 `$(MSBuildProjectDirectory)`인 [예약된 속성](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)은 프로젝트 파일의 절대 경로로 확인됩니다.
* 파일 목록을 `DotNetPublishFiles` 항목에 제공합니다. 기본적으로 항목의 `<DestinationRelativePath>` 요소는 비어 있습니다. 기본값은 변경 내용에서 재정의되며 `%(RecursiveDir)`과 같이 [잘 알려진 항목 메타데이터](/visualstudio/msbuild/msbuild-well-known-item-metadata)를 사용합니다. 내부 텍스트는 게시된 사이트의 *wwwroot/images* 폴더를 나타냅니다.

### <a name="selective-file-inclusion"></a>선택적 파일 포함

다음 예제에서 강조 표시된 변경 내용은 보여 줍니다.

* 프로젝트 밖에 위치한 파일을 게시된 사이트의 *wwwroot* 폴더로 복사. *ReadMe2.md* 의 파일 이름은 유지됩니다.
* *wwwroot\Content* 폴더 제외.
* *Views\Home\About2.cshtml* 제외.

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

앞의 예제에서는 `Include` 특성에 제공된 파일을 항상 게시된 사이트로 복사하는 것이 기본 동작인 `ResolvedFileToPublish` 항목을 사용합니다. `Never` 또는 `PreserveNewest`의 내부 텍스트와 함께 `<CopyToPublishDirectory>` 자식 요소를 포함하여 기본 동작을 재정의합니다. 예를 들어:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

더 많은 배포 샘플을 보려면 [웹 SDK 추가 정보 파일](https://github.com/dotnet/sdk/tree/master/src/WebSdk)을 참조하세요.

## <a name="run-a-target-before-or-after-publishing"></a>게시 이전 또는 이후 대상 실행

기본 제공 `BeforePublish` 및 `AfterPublish` 대상은 게시 대상 이전 또는 이후에 대상을 실행합니다. 다음 요소를 게시 프로필에 추가하여 게시 이전 및 이후에 콘솔 메시지를 기록합니다.

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>신뢰할 수 없는 인증서를 사용하여 서버에 게시

`True` 값을 사용하는 `<AllowUntrustedCertificate>` 속성을 게시 프로필에 추가합니다.

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Kudu 서비스

Azure App Service 웹앱 배포에 있는 파일을 보려면 [Kudu 서비스](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)를 사용합니다. `scm` 토큰을 웹앱 이름에 추가합니다. 예를 들어:

| URL                                    | 결과       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | 웹앱      |
| `http://mysite.scm.azurewebsites.net/` | Kudu 서비스 |

[디버그 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console) 메뉴 항목을 선택하여 파일을 표시, 편집, 삭제 또는 추가합니다.

## <a name="additional-resources"></a>추가 자료

* [웹 배포](https://www.iis.net/downloads/microsoft/web-deploy)(MSDeploy)는 IIS 서버에 대한 웹앱 및 웹 사이트 배포를 간소화합니다.
* [웹 SDK GitHub 리포지토리](https://github.com/dotnet/websdk/issues): 배포에 대한 파일 문제 및 요청 기능.
* [Visual Studio에서 Azure VM에 ASP.NET 웹앱 게시](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
