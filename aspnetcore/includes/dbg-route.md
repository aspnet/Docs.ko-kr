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
ms.openlocfilehash: 087c5a7dd20bc653a05c337dde905716032e00b3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551973"
---
## <a name="debug-diagnostics"></a><span data-ttu-id="2dc1b-101">디버그 진단</span><span class="sxs-lookup"><span data-stu-id="2dc1b-101">Debug diagnostics</span></span>

<span data-ttu-id="2dc1b-102">자세한 라우팅 진단 출력을 위해 `Logging:LogLevel:Microsoft`를 `Debug`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2dc1b-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="2dc1b-103">개발 환경에서 *appsettings.Development.json* 의 로그 수준을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2dc1b-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
