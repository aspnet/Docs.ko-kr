<span data-ttu-id="52290-101"><xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>를 사용하여 `User.Read` 권한에 대한 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52290-101">Add a <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> for `User.Read` permission with <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
