---
title: ASP.NET Core Blazor용 도구
author: guardrex
description: Blazor 앱을 빌드하는 데 사용할 수 있는 도구에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 19270bb74326dccfee9466b7c1fa61daeab805a2
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394462"
---
# <a name="tooling-for-aspnet-core-blazor"></a>ASP.NET Core Blazor용 도구

::: zone pivot="windows"

1. **ASP.NET 및 웹 개발** 워크로드가 있는 최신 버전의 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)를 설치합니다.

1. 새 프로젝트를 만듭니다.

1. **Blazor 앱** 을 선택합니다. **새로 만들기** 를 선택합니다.

1. **프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다. **위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다. **만들기** 를 선택합니다.

1. Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다. Blazor Server 환경의 경우 **Blazor Server 앱** 템플릿을 선택합니다. **만들기** 를 선택합니다.

   호스트된 Blazor WebAssembly 환경의 경우 **ASP.NET Core 호스팅** 확인란을 선택합니다.

   두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* (독립 실행형 및 호스팅) 및 *Blazor Server* 에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.

1. <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.

ASP.NET Core HTTPS 개발 인증서 신뢰에 대한 자세한 내용은 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>를 참조하세요.

호스트된 Blazor WebAssembly 앱을 실행하는 경우 솔루션의 **`Server`** 프로젝트에서 앱을 실행합니다.

::: zone-end

::: zone pivot="linux"

1. [.NET Core SDK](https://dotnet.microsoft.com/download)의 최신 버전을 설치합니다. 이전에 SDK를 설치한 경우 명령 셸에서 다음 명령을 실행하여 설치된 버전을 확인할 수 있습니다.

   ```dotnetcli
   dotnet --version
   ```

1. 최신 버전의 [Visual Studio Code](https://code.visualstudio.com)를 설치합니다.

1. 최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치합니다.

1. Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   호스트된 Blazor WebAssembly 환경의 경우 호스트된 옵션(`-ho` 또는 `--hosted`)을 명령에 추가합니다.

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   Blazor Server 환경의 경우 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* (독립 실행형 및 호스팅) 및 *Blazor Server* 에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.

1. Visual Studio Code에서 `WebApplication1` 폴더를 엽니다.

1. IDE에서 프로젝트를 빌드 및 디버그하기 위한 자산을 추가하도록 요청합니다. **Yes** 를 선택합니다.

   **호스트된 Blazor WebAssembly 시작 및 작업 구성**

   호스트된 Blazor WebAssembly 솔루션의 경우 `launch.json` 및 `tasks.json` 파일이 포함된 `.vscode` 폴더를 솔루션의 부모 폴더에 추가(또는 이동)합니다. 이 폴더는 `Client`, `Server` 및 `Shared`의 일반적인 프로젝트 폴더 이름을 포함하는 폴더입니다. `launch.json` 및 `tasks.json` 파일의 구성이 **`Server`** 프로젝트에서 호스트된 Blazor WebAssembly 앱을 실행하는지 확인하거나 업데이트합니다.

   **`.vscode/launch.json`** (`launch` 구성):

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   현재 작업 디렉터리(`cwd`)에 대한 위 구성에서 `{SERVER APP FOLDER}` 자리 표시자는 **`Server`** 프로젝트의 폴더(일반적으로 “`Server`”)입니다.

   Microsoft Edge를 사용하고 Google Chrome이 시스템에 설치되지 않은 경우 `"browser": "edge"`의 추가 속성을 구성에 추가합니다.

   `Server`의 프로젝트 폴더 및 기본 브라우저 Google Chrome 대신 디버그 실행용 브라우저로 Microsoft Edge를 생성하는 예제:

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   **`.vscode/tasks.json`** ([`dotnet` 명령](/dotnet/core/tools/dotnet) 인수):

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   위의 인수는 다음과 같습니다.

   * `{SERVER APP FOLDER}` 자리 표시자는 **`Server`** 프로젝트의 폴더(일반적으로 “`Server`”)입니다.
   * `{PROJECT NAME}` 자리 표시자는 앱의 이름이며 일반적으로 [Blazor 프로젝트 템플릿](xref:blazor/project-structure)에서 생성된 앱에서 솔루션 이름과 그 뒤에 오는 “`.Server`”를 기반으로 합니다.

   [Blazor WebAssembly 앱에서 SignalR 사용에 대한 자습서](xref:tutorials/signalr-blazor)의 다음 예제에서는 `Server`의 프로젝트 폴더 이름과 `BlazorWebAssemblySignalRApp.Server`의 프로젝트 이름을 사용합니다.

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.

## <a name="trust-a-development-certificate"></a>개발 인증서 신뢰

Linux에서 인증서를 신뢰할 수 있는 중앙 집중식 방법은 없습니다. 일반적으로 다음 방법 중 하나를 채택합니다.

* 브라우저의 제외 목록에서 애플리케이션의 URL을 제외합니다.
* `localhost`에 대한 모든 자체 서명된 인증서를 신뢰합니다.
* 브라우저의 신뢰할 수 있는 인증서 목록에 인증서를 추가합니다.

자세한 내용은 브라우저 제조업체 및 Linux 배포판에서 제공하는 지침을 참조하세요.

::: zone-end

::: zone pivot="macos"

1. [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치합니다.

1. **파일** > **새 솔루션** 을 선택하거나 **시작 창** 에서 **새** 프로젝트를 만듭니다.

1. 사이드바에서 **웹 및 콘솔** > **앱** 을 선택합니다.

   Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다. Blazor Server 환경의 경우 **Blazor Server 앱** 템플릿을 선택합니다. **새로 만들기** 를 선택합니다.

   두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* (독립 실행형 및 호스팅) 및 *Blazor Server* 에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.

1. **인증** 이 **인증 없음** 으로 설정되었는지 확인합니다. **새로 만들기** 를 선택합니다.

1. 호스트된 Blazor WebAssembly 환경의 경우 **ASP.NET Core 호스팅** 확인란을 선택합니다.

1. **프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 지정합니다. **만들기** 를 선택합니다.

1. **실행** > **디버깅하지 않고 시작** 을 선택하여 ‘디버거 없이’ 앱을 실행합니다. **실행** > **디버깅 시작** 으로 앱을 실행하거나 실행(&#9654;) 단추를 사용하여 ‘디버거 없이’ 앱을 실행합니다.

개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다. 인증서를 신뢰하려면 사용자 및 키 집합 암호가 필요합니다. ASP.NET Core HTTPS 개발 인증서 신뢰에 대한 자세한 내용은 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>를 참조하세요.

호스트된 Blazor WebAssembly 앱을 실행하는 경우 솔루션의 **`Server`** 프로젝트에서 앱을 실행합니다.

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a>플랫폼 간 Blazor 개발용 Visual Studio Code 사용

[Visual Studio Code](https://code.visualstudio.com/)는 Blazor 앱을 개발하는 데 사용할 수 있는 오픈 소스 플랫폼 간 IDE(통합 개발 환경)입니다. .NET CLI를 사용하여 Visual Studio Code를 사용하여 개발할 새 Blazor 앱을 만들 수 있습니다. 자세한 내용은 [이 문서의 Linux 버전](?pivots=linux)을 참조하세요.

## <a name="blazor-template-options"></a>Blazor 템플릿 옵션

Blazor 프레임워크는 두 개의 각 Blazor 호스팅 모델용 새 앱을 만들기 위한 템플릿을 제공합니다. 템플릿은 Blazor 개발을 위해 선택하는 도구(Visual Studio, Mac용 Visual Studio, Visual Studio Code, .NET CLI 등)에 관계없이 새 Blazor 프로젝트와 솔루션을 만드는 데 사용됩니다.

* Blazor WebAssembly 프로젝트 템플릿: `blazorwasm`
* Blazor Server 프로젝트 템플릿: `blazorserver`

Blazor의 호스팅 모델에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요. Blazor 프로젝트 템플릿에 대한 자세한 내용은 <xref:blazor/project-structure>를 참조하세요.

템플릿 옵션은 명령 셸에서 도움말 옵션(`-h` 또는 `--help`)을 [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI 명령에 전달하여 사용할 수 있습니다.

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```

## <a name="additional-resources"></a>추가 리소스

* <xref:blazor/hosting-models>
* <xref:blazor/project-structure>
