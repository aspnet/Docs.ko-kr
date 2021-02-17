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
ms.openlocfilehash: 1700adafb58cad57ea1becbf53cad45edd047962
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552969"
---
생성 된 Identity 데이터베이스 코드에는 [Entity Framework Core 마이그레이션이](/ef/core/managing-schemas/migrations/)필요 합니다. 마이그레이션을 만들고 데이터베이스를 업데이트 합니다. 예를 들어 다음 명령을 실행 합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio **패키지 관리자 콘솔** 에서 다음을 수행 합니다.

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

Identity명령에 대 한 "Create Schema" name 매개 변수는 `Add-Migration` 임의입니다. `"CreateIdentitySchema"` 마이그레이션에 대해 설명 합니다.
