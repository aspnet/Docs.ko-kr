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
ms.openlocfilehash: ce0a993093812c9a477d85d363827988ae9bd536
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551514"
---
<span data-ttu-id="7d3e6-101">인덱스 페이지(`wwwroot/index.html`)는 JavaScript로 `AuthenticationService`를 정의하는 스크립트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7d3e6-101">The Index page (`wwwroot/index.html`) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="7d3e6-102">`AuthenticationService`는 OIDC 프로토콜의 하위 수준 세부 정보를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7d3e6-102">`AuthenticationService` handles the low-level details of the OIDC protocol.</span></span> <span data-ttu-id="7d3e6-103">앱은 내부적으로 스크립트에 정의된 메서드를 호출하여 인증 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7d3e6-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
