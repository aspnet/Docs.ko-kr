---
title: 도구 및 다운로드 - ASP.NET Core 및 Azure와 DevOps
author: CamSoper
description: ASP.NET Core 및 Azure를 사용하는 DevOps에 필요한 도구 및 다운로드입니다.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 8c7f10a6b6f8504efaba6054533aba034982820c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056571"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="e14b7-103">도구 및 다운로드</span><span class="sxs-lookup"><span data-stu-id="e14b7-103">Tools and downloads</span></span>

<span data-ttu-id="e14b7-104">Azure에는 [Azure Portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash) 및 Visual Studio와 같이 리소스를 프로비저닝하고 관리하기 위한 몇 가지 인터페이스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="e14b7-105">이 가이드에서는 미니멀리스트 방법을 사용하며, 필요한 단계를 줄이기 위해 가능한 경우 항상 Azure Cloud Shell을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="e14b7-106">그러나 Azure Portal을 사용해야 하는 부분도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e14b7-107">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="e14b7-107">Prerequisites</span></span>

<span data-ttu-id="e14b7-108">다음 구독이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="e14b7-109">Azure - 계정이 없는 경우 [평가판을 다운로드](https://azure.microsoft.com/free/dotnet/)합니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/dotnet/).</span></span>
* <span data-ttu-id="e14b7-110">Azure DevOps Services - 4장에서 Azure DevOps 구독과 조직을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="e14b7-111">GitHub - 계정이 없는 경우 [체험 등록](https://github.com/join)합니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="e14b7-112">다음 도구가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-112">The following tools are required:</span></span>

* <span data-ttu-id="e14b7-113">[Git](https://git-scm.com/downloads) - 이 가이드를 수행하려면 Git의 기본 사항을 알아두는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="e14b7-114">[Git 설명서](https://git-scm.com/doc), 특히 [git remote](https://git-scm.com/docs/git-remote) 및 [git push](https://git-scm.com/docs/git-push)를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="e14b7-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) - 샘플 앱을 빌드하고 실행하려면 버전 2.1.300 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="e14b7-116">**.NET Core 플랫폼 간 개발** 워크로드를 사용하여 Visual Studio를 설치한 경우에는 .NET Core SDK가 이미 설치되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="e14b7-117">.NET Core SDK 설치를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="e14b7-118">명령 셸을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="e14b7-119">권장 도구(Windows만 해당)</span><span class="sxs-lookup"><span data-stu-id="e14b7-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="e14b7-120">[Visual Studio](https://visualstudio.microsoft.com)의 강력한 Azure 도구는 이 가이드에서 설명하는 대부분의 기능에 대한 GUI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="e14b7-121">체험 Visual Studio Community Edition을 포함하여 모든 Visual Studio 버전이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="e14b7-122">이 자습서는 Visual Studio를 사용하는 경우와 사용하지 않는 경우의 개발, 배포 및 DevOps를 보여 주기 위해 작성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="e14b7-123">Visual Studio에 다음 [워크로드](/visualstudio/install/modify-visual-studio)가 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e14b7-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="e14b7-124">ASP.NET 및 웹 개발</span><span class="sxs-lookup"><span data-stu-id="e14b7-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="e14b7-125">Azure 개발</span><span class="sxs-lookup"><span data-stu-id="e14b7-125">Azure development</span></span>
  * <span data-ttu-id="e14b7-126">.NET Core 플랫폼 간 개발</span><span class="sxs-lookup"><span data-stu-id="e14b7-126">.NET Core cross-platform development</span></span>
