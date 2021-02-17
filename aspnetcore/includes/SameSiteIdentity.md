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
ms.openlocfilehash: b6f6bc2e094c9070e0ea57b507f558313f19bc15
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552033"
---
<span data-ttu-id="0bd4c-101">[Identity](xref:security/authentication/identity)또는 통합과 같은 고급 시나리오를 제외 하 고 ASP.NET Core는 [SameSite cookie s](xref:security/samesite) 의 영향을 받지 않습니다 `IFrames` `OpenIdConnect` .</span><span class="sxs-lookup"><span data-stu-id="0bd4c-101">ASP.NET Core [Identity](xref:security/authentication/identity) is largely unaffected by [SameSite cookies](xref:security/samesite) except for advanced scenarios like `IFrames` or `OpenIdConnect` integration.</span></span>

<span data-ttu-id="0bd4c-102">를 사용 하 `Identity` 는 경우 공급자를 추가 하거나 호출 ***하지*** 마십시오 cookie ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` `Identity` .</span><span class="sxs-lookup"><span data-stu-id="0bd4c-102">When using `Identity`, do ***not*** add any cookie providers or call ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)`, `Identity` takes care of that.</span></span>