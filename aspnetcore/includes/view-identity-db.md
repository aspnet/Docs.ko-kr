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
### <a name="view-the-identity-database"></a>데이터베이스 보기 Identity

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

* **보기** 메뉴에서 **SQL Server 개체 탐색기** (SSOX)를 선택 합니다.
* **(Localdb) MSSQLLocalDB (SQL Server 13)** 로 이동 합니다. Dbo를 마우스 오른쪽 단추로 클릭 **합니다. AspNetUsers**  >  **뷰 데이터**:

![SQL Server 개체 탐색기의 AspNetUsers 테이블에 대 한 상황에 맞는 메뉴](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Sqlite 데이터베이스를 관리 하 고 보는 데 사용할 수 있는 여러 타사 도구 (예: [sqlite 용 DB 브라우저](https://sqlitebrowser.org/))가 있습니다.

---