> [!NOTE]
> 
> **SQLite 제한 사항**
>
> 이 자습서에서는 가능한 경우 Entity Framework Core ‘마이그레이션’ 기능을 사용합니다. 마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 수정합니다. 그러나 마이그레이션은 데이터베이스 엔진에서 지원하는 종류의 변경만 수행하며 SQLite의 스키마 변경 기능은 제한됩니다. 예를 들어 열 추가는 지원되지만 열 제거는 지원되지 않습니다. 열을 제거하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다. 
>
> SQLite 제한 사항에 대한 해결 방법은 테이블의 내용이 변경되면 테이블을 다시 빌드하기 위해 수동으로 마이그레이션 코드를 작성하는 것입니다. 이 코드는 마이그레이션을 위해 `Up` 및 `Down` 메서드로 이동하고 다음을 포함합니다.
>
> * 새 테이블 만들기.
> * 이전 테이블에서 새 테이블로 데이터 복사.
> * 이전 테이블 삭제.
> * 새 테이블 이름 바꾸기.
>
> 이 형식의 데이터베이스 관련 코드를 작성하는 방법은 이 자습서에서 다루지 않습니다. 대신, 이 자습서에서는 마이그레이션을 적용하려는 시도가 실패할 때마다 데이터베이스를 삭제하고 다시 만듭니다. 자세한 내용은 다음 자료를 참조하세요.
>
> * [SQLite EF Core 데이터베이스 공급자 제한 사항](/ef/core/providers/sqlite/limitations)
> * [마이그레이션 코드 사용자 지정](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [데이터 시드](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 문](https://sqlite.org/lang_altertable.html)