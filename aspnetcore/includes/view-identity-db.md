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
ms.openlocfilehash: ff0502f68546213d76fe33f45574b5d8654b522b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551743"
---
### <a name="view-the-identity-database"></a><span data-ttu-id="43631-101">데이터베이스 보기 Identity</span><span class="sxs-lookup"><span data-stu-id="43631-101">View the Identity database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="43631-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43631-102">Visual Studio</span></span>](#tab/visual-studio) 

* <span data-ttu-id="43631-103">**보기** 메뉴에서 **SQL Server 개체 탐색기** (SSOX)를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="43631-103">From the **View** menu, select **SQL Server Object Explorer** (SSOX).</span></span>
* <span data-ttu-id="43631-104">**(Localdb) MSSQLLocalDB (SQL Server 13)** 로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="43631-104">Navigate to **(localdb)MSSQLLocalDB(SQL Server 13)**.</span></span> <span data-ttu-id="43631-105">Dbo를 마우스 오른쪽 단추로 클릭 **합니다. AspNetUsers**  >  **뷰 데이터**:</span><span class="sxs-lookup"><span data-stu-id="43631-105">Right-click on **dbo.AspNetUsers** > **View Data**:</span></span>

![SQL Server 개체 탐색기의 AspNetUsers 테이블에 대 한 상황에 맞는 메뉴](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[<span data-ttu-id="43631-107">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="43631-107">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="43631-108">Sqlite 데이터베이스를 관리 하 고 보는 데 사용할 수 있는 여러 타사 도구 (예: [sqlite 용 DB 브라우저](https://sqlitebrowser.org/))가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43631-108">There are many third party tools you can download to manage and view a SQLite database, for example [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

---