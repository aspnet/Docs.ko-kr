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
ms.openlocfilehash: e8c5bd00178aefb91ab0e7066c5490ceba315530
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552526"
---
> [!NOTE]
> 이 자습서에서는 가능한 한 Entity Framework Core *마이그레이션* 기능을 사용합니다. 마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 수정합니다. 그러나 마이그레이션은 EF Core 공급자가 지원하는 유형의 변경만 수행할 수 있으며 SQLite 공급자의 기능은 제한적입니다. 예를 들어 열 추가는 지원되지만 열 제거 또는 변경은 지원되지 않습니다. 열을 제거 또는 변경하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다. 이러한 제한 때문에 이 자습서에서는 SQLite 스키마 변경에 대한 마이그레이션을 사용하지 않습니다. 대신 스키마가 변경되면 데이터베이스를 삭제하고 다시 만듭니다.
>
>SQLite 제한 사항에 대한 해결 방법은 테이블의 내용이 변경되면 테이블을 다시 빌드하기 위해 수동으로 마이그레이션 코드를 작성하는 것입니다. 테이블 다시 빌드에는 다음이 포함됩니다.
>
>* 새 테이블 만들기.
>* 이전 테이블에서 새 테이블로 데이터 복사.
>* 이전 테이블 삭제.
>* 새 테이블 이름 바꾸기.
>
>자세한 내용은 다음 자료를 참조하세요.
>
> * [SQLite EF Core 데이터베이스 공급자 제한 사항](/ef/core/providers/sqlite/limitations)
> * [마이그레이션 코드 사용자 지정](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [데이터 시드](/ef/core/modeling/data-seeding)
  * [SQLite ALTER TABLE 문](https://sqlite.org/lang_altertable.html)