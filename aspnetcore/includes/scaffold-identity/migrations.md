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
<span data-ttu-id="f1eb4-101">생성 된 Identity 데이터베이스 코드에는 [Entity Framework Core 마이그레이션이](/ef/core/managing-schemas/migrations/)필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1eb4-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="f1eb4-102">마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1eb4-102">Create a migration and update the database.</span></span> <span data-ttu-id="f1eb4-103">예를 들어 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1eb4-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f1eb4-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1eb4-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f1eb4-105">Visual Studio **패키지 관리자 콘솔** 에서 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1eb4-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="f1eb4-106">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f1eb4-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="f1eb4-107">Identity명령에 대 한 "Create Schema" name 매개 변수는 `Add-Migration` 임의입니다.</span><span class="sxs-lookup"><span data-stu-id="f1eb4-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="f1eb4-108">`"CreateIdentitySchema"` 마이그레이션에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1eb4-108">`"CreateIdentitySchema"` describes the migration.</span></span>
