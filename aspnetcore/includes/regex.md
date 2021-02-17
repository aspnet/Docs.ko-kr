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
ms.openlocfilehash: 73395ab632f38fef1348b4657770eb52db5778d9
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551396"
---
> [!WARNING]
> <span data-ttu-id="9bc40-101"><xref:System.Text.RegularExpressions>를 사용하여 신뢰할 수 없는 입력을 처리하는 경우 시간 제한을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="9bc40-101">When using <xref:System.Text.RegularExpressions> to process untrusted input, pass a timeout.</span></span> <span data-ttu-id="9bc40-102">악의적인 사용자가 `RegularExpressions`에 대한 입력을 제공하여 [서비스 거부 공격](https://www.us-cert.gov/ncas/tips/ST04-015)을 일으킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9bc40-102">A malicious user can provide input to `RegularExpressions` causing a [Denial-of-Service attack](https://www.us-cert.gov/ncas/tips/ST04-015).</span></span> <span data-ttu-id="9bc40-103">`RegularExpressions`를 사용하는 ASP.NET Core Framework API는 시간 제한을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="9bc40-103">ASP.NET Core framework APIs that use `RegularExpressions` pass a timeout.</span></span>