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
ms.openlocfilehash: c6880852f63b1e91789667506f01680608933226
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552547"
---
> [!WARNING]
> <span data-ttu-id="ef6a0-101">보안상의 이유로 페이지 모델 속성에 `GET` 요청 데이터를 바인딩하기 위해 옵트인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6a0-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="ef6a0-102">속성에 매핑하기 전에 사용자 입력을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6a0-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="ef6a0-103">쿼리 문자열이나 경로 값을 사용하는 시나리오를 지정할 때 `GET` 바인딩을 옵트인하면 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6a0-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="ef6a0-104">`GET` 요청에 속성을 바인딩하려면 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 특성의 `SupportsGet` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6a0-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="ef6a0-105">자세한 내용은 [ASP.NET Core Community 스탠드업을 참조하세요. 토론 가져오기(YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s)에서 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6a0-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
