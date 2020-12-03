<span data-ttu-id="0b871-101">`Authentication` 구성 요소에 의해 생성된 페이지(`Pages/Authentication.razor`)는 다른 인증 단계를 처리하는 데 필요한 경로를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="0b871-101">The page produced by the `Authentication` component (`Pages/Authentication.razor`) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="0b871-102"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0b871-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="0b871-103">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b871-103">Is provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="0b871-104">각 인증 단계에서 적절한 작업을 수행하도록 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="0b871-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
