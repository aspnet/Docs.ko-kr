---
title: 앱 오프라인 파일(app_offline.htm)
author: rick-anderson
description: 앱 오프라인 파일(`app_offline.htm`)이 ASP.NET Core 모듈에서 작동하는 방식을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 29f3fc5ecd18196d914a46629bc9eb50b183bf61
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94431032"
---
# <a name="app-offline-file-app_offlinehtm"></a>앱 오프라인 파일(`app_offline.htm`)

앱 오프라인 파일(`app_offline.htm`)은 ASP.NET Core 모듈에서 앱을 종료하는 데 사용됩니다.

애플리케이션의 루트 디렉터리에서 이름이 `app_offline.htm`인 파일이 검색되면 ASP.NET Core 모듈은 앱을 자동으로 종료하고 들어오는 요청 처리를 중지하려고 합니다. `shutdownTimeLimit`에 정의된 시간(초) 후에도 앱이 계속 실행되면 ASP.NET Core 모듈은 실행 중인 프로세스를 중지합니다.

`app_offline.htm` 파일이 있는 동안 ASP.NET Core 모듈은 `app_offline.htm` 파일의 콘텐츠를 다시 보내 요청에 응답합니다. `app_offline.htm` 파일이 제거되면 다음 요청이 앱을 시작합니다.

Out-of-Process 호스팅 모델을 사용할 때 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다. 예를 들어 WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.

## <a name="locked-deployment-files"></a>배포 파일이 잠겨 있음

앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다. 잠긴 파일은 배포 중에 덮어쓸 수 없습니다.

`app_offline.htm`은 잠긴 파일을 릴리스하는 기본 메커니즘입니다. `app_offline.htm`은 웹 배포에서 앱을 제대로 중지 및 시작하는 데 사용됩니다.

`app_offline.htm`을 수동으로 사용하여 앱을 시작 및 중지할 수 있습니다(PowerShell 5 이상 필요).

```powershell
$pathToApp = '{PATH TO APP}'


New-Item -Path $pathToApp -Name "app_offline.htm" -ItemType "file"

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp\app_offline.htm
```

이전 PowerShell 스크립트에서:

* 자리 표시자 `{PATH TO APP}`은 앱의 경로입니다.
* `New-Item` 명령은 앱 풀을 중지합니다.
* `Remove-Item` 명령은 앱 풀을 시작합니다.
* `New-Item` 명령과 `Remove-Item` 명령 사이에 있는 명령은 개발자가 앱을 배포하기 위해 제공합니다.

서버의 IIS 관리자에서 앱 풀을 수동으로 중지하여 파일의 잠금을 해제할 수도 있습니다. IIS 관리자를 사용하여 앱 풀을 중지하고 다시 시작하는 경우 `app_offine.htm` 파일을 사용하지 마세요.
