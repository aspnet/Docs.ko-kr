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
ms.openlocfilehash: ae5d8cf64c0db9402a5457cec1df8402f4db103c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551257"
---
Azure Portal은 AAD 테넌트에 [확인되거나 확인되지 않은 게시자 도메인](/azure/active-directory/develop/howto-configure-publisher-domain)이 있는지 여부를 기준으로 다음 앱 ID URI 형식 중 하나를 제공합니다.

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

위 앱 ID URI 중 두 번째를 클라이언트 앱에서 사용하여 범위 URI를 구성했는데 권한 부여에 실패한 경우에는 스키마 및 호스트 없이 범위 URI를 제공해 보세요.

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

예제:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
