<xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>를 사용하여 `User.Read` 권한에 대한 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>를 추가합니다.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
