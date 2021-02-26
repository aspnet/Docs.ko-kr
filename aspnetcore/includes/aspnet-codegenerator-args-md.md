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
ms.openlocfilehash: 41d4fd2e746e08d32d9f666faab55acc56817be2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552957"
---
<!-- Options common to Razor Pages and Controller -->
| <span data-ttu-id="76599-101">옵션</span><span class="sxs-lookup"><span data-stu-id="76599-101">Option</span></span>               | <span data-ttu-id="76599-102">Description</span><span class="sxs-lookup"><span data-stu-id="76599-102">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="76599-103">--model 또는 -m</span><span class="sxs-lookup"><span data-stu-id="76599-103">--model or -m</span></span>  | <span data-ttu-id="76599-104">사용할 모델 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="76599-104">Model class to use.</span></span> |
| <span data-ttu-id="76599-105">--dataContext 또는 -dc</span><span class="sxs-lookup"><span data-stu-id="76599-105">--dataContext or -dc</span></span>  | <span data-ttu-id="76599-106">사용할 `DbContext` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="76599-106">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="76599-107">--bootstrapVersion 또는 -b</span><span class="sxs-lookup"><span data-stu-id="76599-107">--bootstrapVersion or -b</span></span>  | <span data-ttu-id="76599-108">부트스트랩 버전을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="76599-108">Specifies the bootstrap version.</span></span> <span data-ttu-id="76599-109">유효한 값은 `3` 또는 `4`입니다.</span><span class="sxs-lookup"><span data-stu-id="76599-109">Valid values are `3` or `4`.</span></span> <span data-ttu-id="76599-110">기본값은 `4`입니다.</span><span class="sxs-lookup"><span data-stu-id="76599-110">Default is `4`.</span></span> <span data-ttu-id="76599-111">필요하지만 없는 경우에는 지정된 버전의 부트스트랩 파일을 포함하는 *wwwroot* 디렉터리가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="76599-111">If needed and not present, a *wwwroot* directory is created that includes the bootstrap files of the specified version.</span></span> |
| <span data-ttu-id="76599-112">--referenceScriptLibraries 또는 -scripts</span><span class="sxs-lookup"><span data-stu-id="76599-112">--referenceScriptLibraries or -scripts</span></span> |  <span data-ttu-id="76599-113">생성된 뷰에서 스크립트 라이브러리를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="76599-113">Reference script libraries in the generated views.</span></span> <span data-ttu-id="76599-114">편집 및 만들기 페이지에 `_ValidationScriptsPartial`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="76599-114">Adds `_ValidationScriptsPartial` to Edit and Create pages.</span></span> |
| <span data-ttu-id="76599-115">--layout 또는 -l</span><span class="sxs-lookup"><span data-stu-id="76599-115">--layout or -l</span></span> | <span data-ttu-id="76599-116">사용할 사용자 지정 레이아웃 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="76599-116">Custom Layout page to use.</span></span> |
| <span data-ttu-id="76599-117">--useDefaultLayout 또는-udl</span><span class="sxs-lookup"><span data-stu-id="76599-117">--useDefaultLayout or -udl</span></span> | <span data-ttu-id="76599-118">뷰의 기본 레이아웃을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="76599-118">Use the default layout for the views.</span></span> |
| <span data-ttu-id="76599-119">--force 또는 -f</span><span class="sxs-lookup"><span data-stu-id="76599-119">--force or -f</span></span> | <span data-ttu-id="76599-120">기존 파일을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="76599-120">Overwrite existing files.</span></span> |
| <span data-ttu-id="76599-121">--relativeFolderPath 또는-outDir</span><span class="sxs-lookup"><span data-stu-id="76599-121">--relativeFolderPath or -outDir</span></span> | <span data-ttu-id="76599-122">파일이 생성되는 프로젝트의 상대 출력 폴더 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="76599-122">The relative output folder path from project where the file are generated.</span></span> <span data-ttu-id="76599-123">지정하지 않으면 파일이 프로젝트 폴더에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="76599-123">If not specified, files are generated in the project folder.</span></span> |