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
ms.openlocfilehash: 1b045d437d1a16eabc0ab41573c8b66d9c4bb77e
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551832"
---
<span data-ttu-id="dc3c6-101">프레임워크는 기본적으로 팝업 로그인 모드로 설정되며 팝업을 열 수 없는 경우 리디렉션 로그인 모드로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc3c6-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="dc3c6-102"><xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>의 `LoginMode` 속성을 `redirect`로 설정하여 리디렉션 로그인 모드를 사용하도록 MSAL을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="dc3c6-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="dc3c6-103">기본 설정은 `popup`이며 문자열 값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dc3c6-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
