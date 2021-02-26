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
ms.openlocfilehash: 164c9e8f73502fc627c4514bd683dc183d318b1d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552373"
---
초기화를 사용하면 서버에서 지정된 오류 코드를 사용하여 HTTP/2 요청을 초기화할 수 있습니다. 초기화 요청은 중단된 것으로 간주합니다.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

이전 코드 예제의 `Reset`은 `INTERNAL_ERROR` 오류 코드를 지정합니다. HTTP/2 오류 코드에 관한 자세한 내용은 [HTTP/2 사양 오류 코드 섹션](https://tools.ietf.org/html/rfc7540#page-50)을 참조하세요.