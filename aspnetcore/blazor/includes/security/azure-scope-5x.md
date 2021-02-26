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
ms.openlocfilehash: 5964554c36e2242b70faee390374828acd2bd860
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551336"
---
AAD에 등록된 서버 API에서 작업하고 앱의 AAD 등록이 [확인되지 않은 게시자 도메인](/azure/active-directory/develop/howto-configure-publisher-domain)을 사용하는 테넌트에 있는 경우 서버 API 앱의 앱 ID URI는 `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`가 아니라 `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` 형식입니다. 해당하는 경우 *`Client`* 앱의 `Program.Main`(`Program.cs`)에 있는 기본 액세스 토큰 범위는 다음과 같이 표시됩니다.

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

일치하는 대상 그룹을 위해 서버 API 앱을 구성하려면 *`Server`* API 앱 설정 파일(`appsettings.json`)에서 `Audience`를 Azure Portal에서 제공하는 앱의 대상 그룹과 일치하도록 설정합니다.

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

앞의 구성에서 `Audience` 값의 끝에는 기본 범위 `/{DEFAULT SCOPE}`가 포함되지 **않습니다**.

예제:

*`Client`* 앱의 `Program.Main`(`Program.cs`):

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

일치하는 대상 그룹(`Audience`)을 사용하여 *`Server`* API 앱 설정 파일(`appsettings.json`)을 구성합니다.

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

앞의 예제에서 `Audience` 값의 끝에는 기본 범위 `/API.Access`가 포함되지 **않습니다**.
