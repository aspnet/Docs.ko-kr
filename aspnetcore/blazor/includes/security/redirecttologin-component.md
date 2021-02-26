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
ms.openlocfilehash: 9b7b302485a5c9ab7d1b1da6ce4d8ab7d1c26f9c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551253"
---
`RedirectToLogin` 구성 요소(`Shared/RedirectToLogin.razor`)는 다음을 수행합니다.

* 로그인 페이지로의 권한 없는 사용자 리디렉션을 관리합니다.
* 사용자가 인증에 성공하면 해당 페이지로 되돌아올 수 있도록 사용자가 액세스하려고 하는 현재 URL을 유지합니다.

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
