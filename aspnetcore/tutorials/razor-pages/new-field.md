---
title: '7부: 새 필드 추가'
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 7부입니다.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 852c26f89b229d89797d9ce6fce2983319defe9c
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101148"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a>7부. ASP.NET Core의 Razor Page에 새 필드 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

이 섹션에서는 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션으로 다음 작업을 수행합니다.

* 모델에 새 필드를 추가합니다.
* 데이터베이스로 새 필드 스키마 변경 내용을 마이그레이션합니다.

EF Code First를 사용하여 자동으로 데이터베이스를 만들 경우 Code First는

* 데이터베이스에 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 테이블을 추가하여 데이터베이스의 스키마가 생성된 모델 클래스와 동기화되어 있는지 여부를 추적합니다.
* 모델 클래스가 데이터베이스와 동기화되지 않으면 EF는 예외를 throw합니다.

스키마와 모델의 동기화를 자동 확인하면 일관성이 없는 데이터베이스 코드 문제를 쉽게 찾을 수 있습니다.

## <a name="adding-a-rating-property-to-the-movie-model"></a>영화 모델에 등급 속성 추가

1. *Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. 앱을 빌드합니다.

1. *Pages/Movies/Index.cshtml* 을 편집하고 `Rating` 필드를 추가합니다.

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. 다음 페이지를 업데이트합니다.
   1. 삭제 및 세부 정보 페이지에 `Rating` 필드를 추가합니다.
   1. [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml)을 `Rating` 필드로 업데이트합니다.
   1. 편집 페이지에 `Rating` 필드를 추가합니다.

새 필드를 포함하도록 데이터베이스가 업데이트될 때까지 앱은 작동하지 않습니다. 데이터베이스를 업데이트하지 않고 앱을 실행하면 `SqlException`이 throw됩니다.

`SqlException: Invalid column name 'Rating'.`

`SqlException` 예외는 업데이트된 영화 모델 클래스가 데이터베이스의 영화 테이블의 스키마와 다르면 발생합니다. 데이터베이스 테이블에 `Rating` 열이 없습니다.

이 오류를 해결할 수 있는 몇 가지 방법이 있습니다.

1. 새 모델 클래스 스키마를 사용하여 Entity Framework를 자동으로 삭제하고 데이터베이스를 다시 만듭니다. 이 방법은 개발 주기의 초기 단계에서 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다. 단점은 데이터베이스의 기존 데이터를 잃게 된다는 것입니다. 프로덕션 데이터베이스에서 이 방법을 사용하지 마세요. 테스트 데이터로 데이터베이스를 자동으로 시드하도록 스키마 변경 시 데이터베이스를 삭제하고 이니셜라이저를 사용하는 것은 종종 앱을 개발하는 효율적인 방법입니다.

2. 모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다. 이 방법의 장점은 데이터가 유지된다는 점입니다. 이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행합니다.

3. Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.

이 자습서의 경우 Code First 마이그레이션을 사용합니다.

새 열에 대한 값을 제공하도록 `SeedData` 클래스를 수정합니다. 아래에 샘플 변경이 나와 있지만 `new Movie` 블록마다 이 변경을 수행하세요.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

[완료된 SeedData.cs 파일](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)을 참조하세요.

솔루션을 빌드합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>등급 필드에 대한 마이그레이션을 추가합니다.

1. **도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔** 을 선택합니다.
2. PMC에서 다음 명령을 입력합니다.

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

`Add-Migration` 명령은 프레임워크에 다음 작업을 지시합니다.

* `Movie` 모델을 `Movie` 데이터베이스 스키마와 비교합니다.
* 데이터베이스 스키마를 새 모델로 마이그레이션하도록 코드를 작성합니다.

"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다. 마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.

`Update-Database` 명령은 프레임워크에게 스키마 변경 내용을 데이터베이스에 적용하고 기존 데이터를 보존하라고 명령합니다.

<a name="ssox"></a>

데이터베이스의 모든 레코드를 삭제하면 이니셜라이저가 데이터베이스를 시드하고 `Rating` 필드를 포함합니다. 브라우저 또는 [SSOX](xref:tutorials/razor-pages/sql#ssox)(Sql Server 개체 탐색기)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.

다른 옵션은 데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만드는 것입니다. SSOX에서 데이터베이스를 삭제하려면:

1. SSOX에서 데이터베이스를 선택합니다.
1. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.
1. **기존 연결 닫기** 를 선택합니다.
1. **확인** 을 선택합니다.
1. [PMC](xref:tutorials/razor-pages/new-field#pmc)에서 데이터베이스를 업데이트합니다.

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>데이터베이스를 삭제하고 다시 만들기

> [!NOTE]
> 이 자습서에서는 가능한 경우 Entity Framework Core 마이그레이션 기능을 사용합니다. 마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 수정합니다. 그러나 마이그레이션은 EF Core 공급자가 지원하는 유형의 변경만 수행할 수 있으며 SQLite 공급자의 기능은 제한적입니다. 예를 들어 열 추가는 지원되지만 열 제거 또는 변경은 지원되지 않습니다. 열을 제거 또는 변경하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다. 이러한 제한 때문에 이 자습서에서는 SQLite 스키마 변경에 대한 마이그레이션을 사용하지 않습니다. 대신 스키마가 변경되면 데이터베이스를 삭제하고 다시 만듭니다.
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
> * [SQLite ALTER TABLE 문](https://sqlite.org/lang_altertable.html)

1. 마이그레이션 폴더를 삭제합니다.  

1. 다음 명령을 사용하여 데이터베이스를 다시 만듭니다.

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

앱을 실행하고 `Rating` 필드를 사용하여 영화를 만들고/편집/표시할 수 있는지 확인합니다. 데이터베이스가 시드되지 않은 경우 `SeedData.Initialize` 메서드에서 중단점을 설정합니다.

## <a name="additional-resources"></a>추가 자료

> [!div class="step-by-step"]
> [이전: 검색 추가](xref:tutorials/razor-pages/search)
> [다음: 유효성 검사 추가](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

이 섹션에서는 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션으로 다음 작업을 수행합니다.

* 모델에 새 필드를 추가합니다.
* 데이터베이스로 새 필드 스키마 변경 내용을 마이그레이션합니다.

EF Code First를 사용하여 자동으로 데이터베이스를 만들 경우 Code First는

* 데이터베이스에 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 테이블을 추가하여 데이터베이스의 스키마가 생성된 모델 클래스와 동기화되어 있는지 여부를 추적합니다.
* 모델 클래스가 데이터베이스와 동기화되지 않으면 EF는 예외를 throw합니다.

스키마와 모델의 동기화를 자동 확인하면 일관성이 없는 데이터베이스 코드 문제를 쉽게 찾을 수 있습니다.

## <a name="adding-a-rating-property-to-the-movie-model"></a>영화 모델에 등급 속성 추가

1. *Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. 앱을 빌드합니다.

1. *Pages/Movies/Index.cshtml* 을 편집하고 `Rating` 필드를 추가합니다.

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. 다음 페이지를 업데이트합니다.
   1. 삭제 및 세부 정보 페이지에 `Rating` 필드를 추가합니다.
   1. [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml)을 `Rating` 필드로 업데이트합니다.
   1. 편집 페이지에 `Rating` 필드를 추가합니다.

새 필드를 포함하도록 데이터베이스가 업데이트될 때까지 앱은 작동하지 않습니다. 데이터베이스를 업데이트하지 않고 앱을 실행하면 `SqlException`이 throw됩니다.

`SqlException: Invalid column name 'Rating'.`

`SqlException` 예외는 업데이트된 영화 모델 클래스가 데이터베이스의 영화 테이블의 스키마와 다르면 발생합니다. 데이터베이스 테이블에 `Rating` 열이 없습니다.

이 오류를 해결할 수 있는 몇 가지 방법이 있습니다.

1. 새 모델 클래스 스키마를 사용하여 Entity Framework를 자동으로 삭제하고 데이터베이스를 다시 만듭니다. 이 방법은 개발 주기의 초기 단계에서 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다. 단점은 데이터베이스의 기존 데이터를 잃게 된다는 것입니다. 프로덕션 데이터베이스에서 이 방법을 사용하지 마세요. 테스트 데이터로 데이터베이스를 자동으로 시드하도록 스키마 변경 시 데이터베이스를 삭제하고 이니셜라이저를 사용하는 것은 종종 앱을 개발하는 효율적인 방법입니다.

2. 모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다. 이 방법의 장점은 데이터가 유지된다는 점입니다. 이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행합니다.

3. Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.

이 자습서의 경우 Code First 마이그레이션을 사용합니다.

새 열에 대한 값을 제공하도록 `SeedData` 클래스를 수정합니다. 아래에 샘플 변경이 나와 있지만 `new Movie` 블록마다 이 변경을 수행하세요.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

[완료된 SeedData.cs 파일](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)을 참조하세요.

솔루션을 빌드합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>등급 필드에 대한 마이그레이션을 추가합니다.

1. **도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔** 을 선택합니다.
2. PMC에서 다음 명령을 입력합니다.

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

`Add-Migration` 명령은 프레임워크에 다음 작업을 지시합니다.

* `Movie` 모델을 `Movie` 데이터베이스 스키마와 비교합니다.
* 데이터베이스 스키마를 새 모델로 마이그레이션하도록 코드를 작성합니다.

"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다. 마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.

`Update-Database` 명령은 프레임워크에게 스키마 변경 내용을 데이터베이스에 적용하고 기존 데이터를 보존하라고 명령합니다.

<a name="ssox"></a>

데이터베이스의 모든 레코드를 삭제하면 이니셜라이저가 데이터베이스를 시드하고 `Rating` 필드를 포함합니다. 브라우저 또는 [SSOX](xref:tutorials/razor-pages/sql#ssox)(Sql Server 개체 탐색기)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.

다른 옵션은 데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만드는 것입니다. SSOX에서 데이터베이스를 삭제하려면:

* SSOX에서 데이터베이스를 선택합니다.
* 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.
* **기존 연결 닫기** 를 선택합니다.
* **확인** 을 선택합니다.
* [PMC](xref:tutorials/razor-pages/new-field#pmc)에서 데이터베이스를 업데이트합니다.

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>데이터베이스를 삭제하고 다시 만들기

> [!NOTE]
> 이 자습서에서는 가능한 경우 Entity Framework Core 마이그레이션 기능을 사용합니다. 마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 수정합니다. 그러나 마이그레이션은 EF Core 공급자가 지원하는 유형의 변경만 수행할 수 있으며 SQLite 공급자의 기능은 제한적입니다. 예를 들어 열 추가는 지원되지만 열 제거 또는 변경은 지원되지 않습니다. 열을 제거 또는 변경하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다. 이러한 제한 때문에 이 자습서에서는 SQLite 스키마 변경에 대한 마이그레이션을 사용하지 않습니다. 대신 스키마가 변경되면 데이터베이스를 삭제하고 다시 만듭니다.
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
> * [SQLite ALTER TABLE 문](https://sqlite.org/lang_altertable.html)

1. 마이그레이션 폴더를 삭제합니다.  

1. 다음 명령을 사용하여 데이터베이스를 다시 만듭니다.

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

앱을 실행하고 `Rating` 필드를 사용하여 영화를 만들고/편집/표시할 수 있는지 확인합니다. 데이터베이스가 시드되지 않은 경우 `SeedData.Initialize` 메서드에서 중단점을 설정합니다.

## <a name="additional-resources"></a>추가 자료

> [!div class="step-by-step"]
> [이전: 검색 추가](xref:tutorials/razor-pages/search)
> [다음: 유효성 검사 추가](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

이 섹션에서는 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션으로 다음 작업을 수행합니다.

* 모델에 새 필드를 추가합니다.
* 데이터베이스로 새 필드 스키마 변경 내용을 마이그레이션합니다.

EF Code First를 사용하여 자동으로 데이터베이스를 만들 경우 Code First는

* 데이터베이스에 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 테이블을 추가하여 데이터베이스의 스키마가 생성된 모델 클래스와 동기화되어 있는지 여부를 추적합니다.
* 모델 클래스가 데이터베이스와 동기화되지 않으면 EF는 예외를 throw합니다.

스키마와 모델의 동기화를 자동 확인하면 일관성이 없는 데이터베이스 코드 문제를 쉽게 찾을 수 있습니다.

## <a name="adding-a-rating-property-to-the-movie-model"></a>영화 모델에 등급 속성 추가

*Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

앱을 빌드합니다.

*Pages/Movies/Index.cshtml* 을 편집하고 `Rating` 필드를 추가합니다.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

다음 페이지를 업데이트합니다.

* 삭제 및 세부 정보 페이지에 `Rating` 필드를 추가합니다.
* [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml)을 `Rating` 필드로 업데이트합니다.
* 편집 페이지에 `Rating` 필드를 추가합니다.

새 필드를 포함하도록 데이터베이스가 업데이트될 때까지 앱은 작동하지 않습니다. 앱을 지금 실행하면 앱은 `SqlException`을 throw합니다.

`SqlException: Invalid column name 'Rating'.`

이 오류는 데이터베이스의 동영상 테이블의 스키마와 다른 업데이트된 동영상 모델 클래스로 인해 발생됩니다. 데이터베이스 테이블에 `Rating` 열이 없습니다.

이 오류를 해결할 수 있는 몇 가지 방법이 있습니다.

1. 새 모델 클래스 스키마를 사용하여 Entity Framework를 자동으로 삭제하고 데이터베이스를 다시 만듭니다. 이 방법은 개발 주기의 초기 단계에서 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다. 단점은 데이터베이스의 기존 데이터를 잃게 된다는 것입니다. 프로덕션 데이터베이스에서 이 방법을 사용하지 마세요. 테스트 데이터로 데이터베이스를 자동으로 시드하도록 스키마 변경 시 데이터베이스를 삭제하고 이니셜라이저를 사용하는 것은 종종 앱을 개발하는 효율적인 방법입니다.

2. 모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다. 이 방법의 장점은 데이터가 유지된다는 점입니다. 이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행합니다.

3. Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.

이 자습서의 경우 Code First 마이그레이션을 사용합니다.

새 열에 대한 값을 제공하도록 `SeedData` 클래스를 수정합니다. 아래에 샘플 변경이 나와 있지만 `new Movie` 블록마다 이 변경을 수행하세요.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

[완료된 SeedData.cs 파일](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)을 참조하세요.

솔루션을 빌드합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>등급 필드에 대한 마이그레이션을 추가합니다.

**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔** 을 선택합니다.
PMC에서 다음 명령을 입력합니다.

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` 명령은 프레임워크에 다음 작업을 지시합니다.

* `Movie` 모델을 `Movie` 데이터베이스 스키마와 비교합니다.
* 데이터베이스 스키마를 새 모델로 마이그레이션하도록 코드를 작성합니다.

"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다. 마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.

`Update-Database` 명령은 스키마 변경 내용을 데이터베이스에 적용하기 위한 프레임워크를 알려줍니다.

<a name="ssox"></a>

데이터베이스의 모든 레코드를 삭제하면 이니셜라이저가 데이터베이스를 시드하고 `Rating` 필드를 포함합니다. 브라우저 또는 [SSOX](xref:tutorials/razor-pages/sql#ssox)(Sql Server 개체 탐색기)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.

다른 옵션은 데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만드는 것입니다. SSOX에서 데이터베이스를 삭제하려면:

* SSOX에서 데이터베이스를 선택합니다.
* 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.
* **기존 연결 닫기** 를 선택합니다.
* **확인** 을 선택합니다.
* [PMC](xref:tutorials/razor-pages/new-field#pmc)에서 데이터베이스를 업데이트합니다.

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>데이터베이스를 삭제하고 다시 만들기

> [!NOTE]
> 이 자습서에서는 가능한 경우 Entity Framework Core 마이그레이션 기능을 사용합니다. 마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 수정합니다. 그러나 마이그레이션은 EF Core 공급자가 지원하는 유형의 변경만 수행할 수 있으며 SQLite 공급자의 기능은 제한적입니다. 예를 들어 열 추가는 지원되지만 열 제거 또는 변경은 지원되지 않습니다. 열을 제거 또는 변경하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다. 이러한 제한 때문에 이 자습서에서는 SQLite 스키마 변경에 대한 마이그레이션을 사용하지 않습니다. 대신 스키마가 변경되면 데이터베이스를 삭제하고 다시 만듭니다.
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
> * [SQLite ALTER TABLE 문](https://sqlite.org/lang_altertable.html)

데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만듭니다. 데이터베이스를 삭제하려면 데이터베이스 파일(*MvcMovie.db*)을 삭제합니다. 그런 다음, `ef database update` 명령을 실행합니다.

```dotnetcli
dotnet ef database update
```

---

앱을 실행하고 `Rating` 필드를 사용하여 영화를 만들고/편집/표시할 수 있는지 확인합니다. 데이터베이스가 시드되지 않은 경우 `SeedData.Initialize` 메서드에서 중단점을 설정합니다.

## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [이전: 검색 추가](xref:tutorials/razor-pages/search)
> [다음: 유효성 검사 추가](xref:tutorials/razor-pages/validation)

::: moniker-end