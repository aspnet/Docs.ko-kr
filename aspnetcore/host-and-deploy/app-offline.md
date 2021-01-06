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
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="36805-103">앱 오프라인 파일(`app_offline.htm`)</span><span class="sxs-lookup"><span data-stu-id="36805-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="36805-104">앱 오프라인 파일(`app_offline.htm`)은 ASP.NET Core 모듈에서 앱을 종료하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="36805-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="36805-105">애플리케이션의 루트 디렉터리에서 이름이 `app_offline.htm`인 파일이 검색되면 ASP.NET Core 모듈은 앱을 자동으로 종료하고 들어오는 요청 처리를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="36805-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="36805-106">`shutdownTimeLimit`에 정의된 시간(초) 후에도 앱이 계속 실행되면 ASP.NET Core 모듈은 실행 중인 프로세스를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="36805-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="36805-107">`app_offline.htm` 파일이 있는 동안 ASP.NET Core 모듈은 `app_offline.htm` 파일의 콘텐츠를 다시 보내 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="36805-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="36805-108">`app_offline.htm` 파일이 제거되면 다음 요청이 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="36805-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="36805-109">Out-of-Process 호스팅 모델을 사용할 때 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36805-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="36805-110">예를 들어 WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36805-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="36805-111">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="36805-111">Locked deployment files</span></span>

<span data-ttu-id="36805-112">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36805-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="36805-113">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="36805-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="36805-114">`app_offline.htm`은 잠긴 파일을 릴리스하는 기본 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="36805-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="36805-115">`app_offline.htm`은 웹 배포에서 앱을 제대로 중지 및 시작하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="36805-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="36805-116">`app_offline.htm`을 수동으로 사용하여 앱을 시작 및 중지할 수 있습니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="36805-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'


New-Item -Path $pathToApp -Name "app_offline.htm" -ItemType "file"

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp\app_offline.htm
```

<span data-ttu-id="36805-117">이전 PowerShell 스크립트에서:</span><span class="sxs-lookup"><span data-stu-id="36805-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="36805-118">자리 표시자 `{PATH TO APP}`은 앱의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="36805-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="36805-119">`New-Item` 명령은 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="36805-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="36805-120">`Remove-Item` 명령은 앱 풀을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="36805-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="36805-121">`New-Item` 명령과 `Remove-Item` 명령 사이에 있는 명령은 개발자가 앱을 배포하기 위해 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="36805-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="36805-122">서버의 IIS 관리자에서 앱 풀을 수동으로 중지하여 파일의 잠금을 해제할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36805-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="36805-123">IIS 관리자를 사용하여 앱 풀을 중지하고 다시 시작하는 경우 `app_offine.htm` 파일을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="36805-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>
