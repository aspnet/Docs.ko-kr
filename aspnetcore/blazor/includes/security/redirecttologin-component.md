<span data-ttu-id="4033f-101">`RedirectToLogin` 구성 요소(`Shared/RedirectToLogin.razor`)는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4033f-101">The `RedirectToLogin` component (`Shared/RedirectToLogin.razor`):</span></span>

* <span data-ttu-id="4033f-102">로그인 페이지로의 권한 없는 사용자 리디렉션을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="4033f-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="4033f-103">사용자가 인증에 성공하면 해당 페이지로 되돌아올 수 있도록 사용자가 액세스하려고 하는 현재 URL을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="4033f-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

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
