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
ms.openlocfilehash: 8772f383a830936881564ca95a7f034ba08a5798
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551590"
---
<span data-ttu-id="70386-101">`FetchData` 구성 요소는 다음을 수행하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="70386-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="70386-102">액세스 토큰을 프로비저닝합니다.</span><span class="sxs-lookup"><span data-stu-id="70386-102">Provision an access token.</span></span>
* <span data-ttu-id="70386-103">액세스 토큰을 사용하여 ‘서버’ 앱에서 보호된 리소스 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="70386-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="70386-104">[`@attribute [Authorize]`](xref:mvc/views/razor#attribute) 지시문은 사용자가 이 구성 요소에 액세스하기 위해 권한을 부여받아야 한다는 것을 Blazor WebAssembly 권한 부여 시스템에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="70386-104">The [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="70386-105">`Client` 앱에 특성이 있으면 서버에 있는 API가 적절한 자격 증명 없이 호출되는 것을 방지하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="70386-105">The presence of the attribute in the `Client` app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="70386-106">또한 `Server` 앱은 적절한 엔드포인트에서 `[Authorize]`를 사용하여 올바로 보호해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="70386-106">The `Server` app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="70386-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>은 API를 호출하는 요청에 추가할 수 있는 액세스 토큰을 요청하는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="70386-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="70386-108">토큰이 캐시되었거나 서비스에서 사용자 개입 없이 새 액세스 토큰을 프로비저닝할 수 있으면 토큰 요청이 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="70386-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="70386-109">그러지 않으면 토큰 요청이 실패하고 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>이 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 catch됩니다.</span><span class="sxs-lookup"><span data-stu-id="70386-109">Otherwise, the token request fails with an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, which is caught in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>

<span data-ttu-id="70386-110">요청에 포함할 실제 토큰을 가져오려면 앱에서 [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A)을 호출하여 요청이 성공했는지 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="70386-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span></span>

<span data-ttu-id="70386-111">요청이 성공하면 토큰 변수가 액세스 토큰으로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="70386-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="70386-112">토큰의 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> 속성은 `Authorization` 요청 헤더에 포함할 리터럴 문자열을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="70386-112">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="70386-113">사용자 개입 없이 토큰을 프로비저닝할 수 없어 요청이 실패한 경우 토큰 결과에 리디렉션 URL이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="70386-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="70386-114">이 URL로 이동하면 사용자가 로그인 페이지로 이동하고 인증 성공 후 현재 페이지로 돌아옵니다.</span><span class="sxs-lookup"><span data-stu-id="70386-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
