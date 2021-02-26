---
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
ms.openlocfilehash: 34e12afd6bc7015e4609fbdf773259ed5545809b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552101"
---
# <a name="visual-studio"></a>[<span data-ttu-id="4cbf2-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cbf2-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4cbf2-102">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-102">Create a new project.</span></span>
1. <span data-ttu-id="4cbf2-103">**작업자 서비스** 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-103">Select **Worker Service**.</span></span> <span data-ttu-id="4cbf2-104">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-104">Select **Next**.</span></span>
1. <span data-ttu-id="4cbf2-105">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="4cbf2-106">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-106">Select **Create**.</span></span>
1. <span data-ttu-id="4cbf2-107">**작업자 서비스 만들기** 대화 상자에서 **만들기** 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4cbf2-108">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cbf2-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4cbf2-109">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-109">Create a new project.</span></span>
1. <span data-ttu-id="4cbf2-110">사이드바에서 **.NET Core** 아래에 있는 **앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="4cbf2-111">**ASP.NET Core** 아래에 있는 **작업자** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="4cbf2-112">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-112">Select **Next**.</span></span>
1. <span data-ttu-id="4cbf2-113">**대상 프레임워크** 로 **.NET Core 3.0** 이상을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="4cbf2-114">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-114">Select **Next**.</span></span>
1. <span data-ttu-id="4cbf2-115">**프로젝트 이름** 필드에 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="4cbf2-116">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4cbf2-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4cbf2-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4cbf2-118">명령 셸에서 [dotnet new](/dotnet/core/tools/dotnet-new) 명령과 함께 Worker Service(`worker`) 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="4cbf2-119">다음 예제에서는 `ContosoWorker`라는 Worker Service 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="4cbf2-120">명령이 실행될 때 `ContosoWorker` 앱의 폴더가 자동으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="4cbf2-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
