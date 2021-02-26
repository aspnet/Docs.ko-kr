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
ms.openlocfilehash: 8db90c457f77dd9addbddaf21228651dda3b3729
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551252"
---
<span data-ttu-id="bb36e-101"><xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>를 사용하여 `User.Read` 권한에 대한 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bb36e-101">Add a <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> for `User.Read` permission with <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
