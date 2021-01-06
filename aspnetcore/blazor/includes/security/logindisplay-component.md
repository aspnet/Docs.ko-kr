<span data-ttu-id="0759f-101">`LoginDisplay` 구성 요소(`Shared/LoginDisplay.razor`)는 `MainLayout` 구성 요소(`Shared/MainLayout.razor`)에서 렌더링되고 다음 동작을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="0759f-101">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="0759f-102">인증된 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="0759f-102">For authenticated users:</span></span>
  * <span data-ttu-id="0759f-103">현재 사용자 이름을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="0759f-103">Displays the current username.</span></span>
  * <span data-ttu-id="0759f-104">앱에서 로그아웃하는 단추를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0759f-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="0759f-105">익명 사용자의 경우, 로그인 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0759f-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
