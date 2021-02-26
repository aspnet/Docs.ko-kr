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
ms.openlocfilehash: 37dc79b7db7c1fe500ebb4ebaf7f07c9142a31db
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551920"
---
## <a name="custom-user-flows"></a><span data-ttu-id="89e28-101">사용자 지정 사용자 흐름</span><span class="sxs-lookup"><span data-stu-id="89e28-101">Custom user flows</span></span>

<span data-ttu-id="89e28-102">Microsoft 인증 라이브러리(<xref:Microsoft.Authentication.WebAssembly.Msal>, [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/))는 기본적으로 [AAD B2C 사용자 흐름](/azure/active-directory-b2c/user-flow-overview)을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="89e28-102">The Microsoft Authentication Library (<xref:Microsoft.Authentication.WebAssembly.Msal>, [NuGet package](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)) doesn't support [AAD B2C user flows](/azure/active-directory-b2c/user-flow-overview) by default.</span></span> <span data-ttu-id="89e28-103">개발자 코드에서 사용자 지정 사용자 흐름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="89e28-103">Create custom user flows in developer code.</span></span>

<span data-ttu-id="89e28-104">사용자 지정 사용자 흐름의 챌린지를 작성하는 방법에 대한 자세한 내용은 [Azure Active Directory B2C의 사용자 흐름](/azure/active-directory-b2c/user-flow-overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="89e28-104">For more information on how to build a challenge for a custom user flow, see [User flows in Azure Active Directory B2C](/azure/active-directory-b2c/user-flow-overview).</span></span>
