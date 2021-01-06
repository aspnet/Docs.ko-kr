프레임워크는 기본적으로 팝업 로그인 모드로 설정되며 팝업을 열 수 없는 경우 리디렉션 로그인 모드로 대체됩니다. <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>의 `LoginMode` 속성을 `redirect`로 설정하여 리디렉션 로그인 모드를 사용하도록 MSAL을 구성합니다.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

기본 설정은 `popup`이며 문자열 값은 대/소문자를 구분하지 않습니다.
