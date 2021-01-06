---
title: 도구 및 다운로드 - ASP.NET Core 및 Azure와 DevOps
author: CamSoper
description: ASP.NET Core 및 Azure를 사용하는 DevOps에 필요한 도구 및 다운로드입니다.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 8c7f10a6b6f8504efaba6054533aba034982820c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056571"
---
# <a name="tools-and-downloads"></a>도구 및 다운로드

Azure에는 [Azure Portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash) 및 Visual Studio와 같이 리소스를 프로비저닝하고 관리하기 위한 몇 가지 인터페이스가 있습니다. 이 가이드에서는 미니멀리스트 방법을 사용하며, 필요한 단계를 줄이기 위해 가능한 경우 항상 Azure Cloud Shell을 사용합니다. 그러나 Azure Portal을 사용해야 하는 부분도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 구독이 필요합니다.

* Azure - 계정이 없는 경우 [평가판을 다운로드](https://azure.microsoft.com/free/dotnet/)합니다.
* Azure DevOps Services - 4장에서 Azure DevOps 구독과 조직을 만듭니다.
* GitHub - 계정이 없는 경우 [체험 등록](https://github.com/join)합니다.

다음 도구가 필요합니다.

* [Git](https://git-scm.com/downloads) - 이 가이드를 수행하려면 Git의 기본 사항을 알아두는 것이 좋습니다. [Git 설명서](https://git-scm.com/doc), 특히 [git remote](https://git-scm.com/docs/git-remote) 및 [git push](https://git-scm.com/docs/git-push)를 검토합니다.
* [.NET Core SDK](https://dotnet.microsoft.com/download/) - 샘플 앱을 빌드하고 실행하려면 버전 2.1.300 이상이 필요합니다. **.NET Core 플랫폼 간 개발** 워크로드를 사용하여 Visual Studio를 설치한 경우에는 .NET Core SDK가 이미 설치되어 있습니다.

    .NET Core SDK 설치를 확인합니다. 명령 셸을 열고 다음 명령을 실행합니다.

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>권장 도구(Windows만 해당)

* [Visual Studio](https://visualstudio.microsoft.com)의 강력한 Azure 도구는 이 가이드에서 설명하는 대부분의 기능에 대한 GUI를 제공합니다. 체험 Visual Studio Community Edition을 포함하여 모든 Visual Studio 버전이 작동합니다. 이 자습서는 Visual Studio를 사용하는 경우와 사용하지 않는 경우의 개발, 배포 및 DevOps를 보여 주기 위해 작성되었습니다.

  Visual Studio에 다음 [워크로드](/visualstudio/install/modify-visual-studio)가 설치되어 있는지 확인합니다.

  * ASP.NET 및 웹 개발
  * Azure 개발
  * .NET Core 플랫폼 간 개발
