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
ms.openlocfilehash: ca743cdb39423d833902c330f6f0682b600c9833
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552851"
---
::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> **catch-all** 매개 변수는 라우팅의 [버그](https://github.com/dotnet/aspnetcore/issues/18677)로 인해 경로와 일치하지 않을 수 있습니다. 이 버그의 영향을 받는 앱은 다음과 같은 특징이 있습니다.
>
> * catch-all 경로(예: `{**slug}"`)
> * catch-all 경로가 일치해야 하는 요청과 일치하지 않습니다.
> * 다른 경로를 제거하면 catch-all 경로 작동이 시작됩니다.
>
> 이 버그에 해당하는 사례는 GitHub 버그 [18677](https://github.com/dotnet/aspnetcore/issues/18677) 및 [16579](https://github.com/dotnet/aspnetcore/issues/16579)를 참조하세요.
>
> 이 버그에 대한 옵트인 픽스가 [.NET Core 3.1.301 SDK 이상](https://dotnet.microsoft.com/download/dotnet-core/3.1)에 포함되어 있습니다. 다음 코드는 이 버그를 수정하는 내부 스위치를 설정합니다.
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end