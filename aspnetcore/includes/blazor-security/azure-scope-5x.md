<span data-ttu-id="ef5ba-101">AAD에 등록된 서버 API에서 작업하고 앱의 AAD 등록이 [확인되지 않은 게시자 도메인](/azure/active-directory/develop/howto-configure-publisher-domain)을 사용하는 테넌트에 있는 경우 서버 API 앱의 앱 ID URI는 `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`가 아니라 `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="ef5ba-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="ef5ba-102">해당하는 경우 *`Client`* 앱의 `Program.Main`(`Program.cs`)에 있는 기본 액세스 토큰 범위는 다음과 같이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef5ba-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="ef5ba-103">일치하는 대상 그룹을 위해 서버 API 앱을 구성하려면 *`Server`* API 앱 설정 파일(`appsettings.json`)에서 `Audience`를 Azure Portal에서 제공하는 앱의 대상 그룹과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ef5ba-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

<span data-ttu-id="ef5ba-104">앞의 구성에서 `Audience` 값의 끝에는 기본 범위 `/{DEFAULT SCOPE}`가 포함되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="ef5ba-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="ef5ba-105">예제:</span><span class="sxs-lookup"><span data-stu-id="ef5ba-105">Example:</span></span>

<span data-ttu-id="ef5ba-106">*`Client`* 앱의 `Program.Main`(`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="ef5ba-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="ef5ba-107">일치하는 대상 그룹(`Audience`)을 사용하여 *`Server`* API 앱 설정 파일(`appsettings.json`)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ef5ba-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

<span data-ttu-id="ef5ba-108">앞의 예제에서 `Audience` 값의 끝에는 기본 범위 `/API.Access`가 포함되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="ef5ba-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
