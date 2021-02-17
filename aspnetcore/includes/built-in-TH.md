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
ms.openlocfilehash: 1cc2a01725a2af8f3dbfe1b23ea81e99bab9ef8e
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551230"
---
## <a name="built-in-aspnet-core-tag-helpers"></a><span data-ttu-id="db66e-101">기본 제공 ASP.NET Core 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="db66e-101">Built-in ASP.NET Core Tag Helpers</span></span>

<span data-ttu-id="db66e-102">**[앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-102">**[Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)**</span></span>

<span data-ttu-id="db66e-103">**[캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-103">**[Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)**</span></span>

<span data-ttu-id="db66e-104">**[구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-104">**[Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)**</span></span>

<span data-ttu-id="db66e-105">**[분산 캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-105">**[Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)**</span></span>

<span data-ttu-id="db66e-106">**[환경 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-106">**[Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)**</span></span>

<span data-ttu-id="db66e-107">**[폼 태그 도우미](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-107">**[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span></span>

<span data-ttu-id="db66e-108">**[양식 작업 태그 도우미](xref:mvc/views/working-with-forms#the-form-action-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-108">**[Form Action Tag Helper](xref:mvc/views/working-with-forms#the-form-action-tag-helper)**</span></span>

<span data-ttu-id="db66e-109">**[이미지 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/image-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-109">**[Image Tag Helper](xref:mvc/views/tag-helpers/builtin-th/image-tag-helper)**</span></span>

<span data-ttu-id="db66e-110">**[입력 태그 도우미](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-110">**[Input Tag Helper](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span></span>

<span data-ttu-id="db66e-111">**[레이블 태그 도우미](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-111">**[Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span></span>

<span data-ttu-id="db66e-112">**[링크 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/link-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-112">**[Link Tag Helper](xref:mvc/views/tag-helpers/builtin-th/link-tag-helper)**</span></span>

<span data-ttu-id="db66e-113">**[부분 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-113">**[Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)**</span></span>

<span data-ttu-id="db66e-114">**[스크립트 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/script-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-114">**[Script Tag Helper](xref:mvc/views/tag-helpers/builtin-th/script-tag-helper)**</span></span>

<span data-ttu-id="db66e-115">**[선택 태그 도우미](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-115">**[Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span></span>

<span data-ttu-id="db66e-116">**[텍스트 영역 태그 도우미](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-116">**[Textarea Tag Helper](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span></span>

<span data-ttu-id="db66e-117">**[유효성 검사 메시지 태그 도우미](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-117">**[Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span></span>

<span data-ttu-id="db66e-118">**[유효성 검사 요약 태그 도우미](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="db66e-118">**[Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span></span>
