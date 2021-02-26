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
ms.openlocfilehash: b2519eaab7a83d66e60be05b1c2deeb8094b016f
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551840"
---
인덱스 페이지(`wwwroot/index.html`)는 JavaScript로 `AuthenticationService`를 정의하는 스크립트를 포함합니다. `AuthenticationService`는 OIDC 프로토콜의 하위 수준 세부 정보를 처리합니다. 앱은 내부적으로 스크립트에 정의된 메서드를 호출하여 인증 작업을 수행합니다.

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
