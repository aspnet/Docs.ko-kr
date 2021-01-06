`FetchData` 구성 요소는 다음을 수행하는 방법을 보여 줍니다.

* 액세스 토큰을 프로비저닝합니다.
* 액세스 토큰을 사용하여 ‘서버’ 앱에서 보호된 리소스 API를 호출합니다.

[`@attribute [Authorize]`](xref:mvc/views/razor#attribute) 지시문은 사용자가 이 구성 요소에 방문하기 위해 권한을 부여받아야 하는 Blazor WebAssembly 권한 부여 시스템을 나타냅니다. `Client` 앱에 특성이 있으면 서버에 있는 API가 적절한 자격 증명 없이 호출되는 것을 방지하지 않습니다. 또한 `Server` 앱은 적절한 엔드포인트에서 `[Authorize]`를 사용하여 올바로 보호해야 합니다.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>은 API를 호출하는 요청에 추가할 수 있는 액세스 토큰을 요청하는 작업을 처리합니다. 토큰이 캐시되었거나 서비스에서 사용자 개입 없이 새 액세스 토큰을 프로비저닝할 수 있으면 토큰 요청이 성공합니다. 그러지 않으면 토큰 요청이 실패하고 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>이 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 catch됩니다.

요청에 포함할 실제 토큰을 가져오려면 앱에서 [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A)을 호출하여 요청이 성공했는지 확인해야 합니다.

요청이 성공하면 토큰 변수가 액세스 토큰으로 채워집니다. 토큰의 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> 속성은 `Authorization` 요청 헤더에 포함할 리터럴 문자열을 노출합니다.

사용자 개입 없이 토큰을 프로비저닝할 수 없어 요청이 실패한 경우 토큰 결과에 리디렉션 URL이 포함됩니다. 이 URL로 이동하면 사용자가 로그인 페이지로 이동하고 인증 성공 후 현재 페이지로 돌아옵니다.

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
