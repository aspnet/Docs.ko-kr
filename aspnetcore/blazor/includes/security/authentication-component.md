`Authentication` 구성 요소에 의해 생성된 페이지(`Pages/Authentication.razor`)는 다른 인증 단계를 처리하는 데 필요한 경로를 정의합니다.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 구성 요소는 다음과 같습니다.

* [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지에서 제공됩니다.
* 각 인증 단계에서 적절한 작업을 수행하도록 관리합니다.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
