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
[Identity](xref:security/authentication/identity)또는 통합과 같은 고급 시나리오를 제외 하 고 ASP.NET Core는 [SameSite cookie s](xref:security/samesite) 의 영향을 받지 않습니다 `IFrames` `OpenIdConnect` .

를 사용 하 `Identity` 는 경우 공급자를 추가 하거나 호출 ***하지*** 마십시오 cookie ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` `Identity` .