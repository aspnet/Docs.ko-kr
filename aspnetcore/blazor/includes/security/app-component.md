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
ms.openlocfilehash: d632ab0604f81f7b6067d4535b0f5da0afe2e0ad
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551672"
---
<span data-ttu-id="27a42-101">`App` 구성 요소(`App.razor`)는 Blazor Server 앱에 있는 `App` 구성 요소와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="27a42-101">The `App` component (`App.razor`) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="27a42-102"><xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> 구성 요소는 앱의 나머지 부분에 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>를 노출하는 것을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="27a42-102">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component manages exposing the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> to the rest of the app.</span></span>
* <span data-ttu-id="27a42-103"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> 구성 요소는 현재 사용자가 지정된 페이지에 액세스할 수 있는 권한이 부여받았는지 확인하고 그러지 않은 경우 `RedirectToLogin` 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="27a42-103">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="27a42-104">`RedirectToLogin` 구성 요소는 권한 없는 사용자를 로그인 페이지로 리디렉션하는 기능을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="27a42-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!include[](../prefer-exact-matches.md)]
