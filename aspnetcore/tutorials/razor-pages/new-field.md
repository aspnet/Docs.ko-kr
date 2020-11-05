---
title: 7부. ASP.NET Core의 Razor Page에 새 필드 추가
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 7부입니다.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 951a8ada57ae523f362313426c0279556eb8339b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050617"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a>7부. ASP.NET Core의 Razor Page에 새 필드 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

이 섹션에서는 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션으로 다음 작업을 수행합니다.

* 모델에 새 필드를 추가합니다.
* 데이터베이스로 새 필드 스키마 변경 내용을 마이그레이션합니다.

EF Code First를 사용하여 자동으로 데이터베이스를 만들 경우 Code First는

* 데이터베이스에 `__EFMigrationsHistory` 테이블을 추가하여 데이터베이스의 스키마가 생성된 모델 클래스와 동기화되어 있는지 여부를 추적합니다.
* 모델 클래스가 DB와 동기화되지 않으면 EF는 예외를 throw합니다.

동기화된 스키마/모델의 자동 확인을 통해 일관성이 없는 데이터베이스/코드 문제를 쉽게 찾을 수 있습니다.

## <a name="adding-a-rating-property-to-the-movie-model"></a>영화 모델에 등급 속성 추가

*Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

앱을 빌드합니다.

*Pages/Movies/Index.cshtml* 를 편집하고 `Rating` 필드를 추가합니다.

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

다음 페이지를 업데이트합니다.

* 삭제 및 세부 정보 페이지에 `Rating` 필드를 추가합니다.
* [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml)을 `Rating` 필드로 업데이트합니다.
* 편집 페이지에 `Rating` 필드를 추가합니다.

새 필드를 포함하도록 DB가 수정될 때까지 앱은 작동하지 않습니다. 데이터베이스를 업데이트하지 않고 앱을 실행하면 `SqlException`이 발생합니다.

`SqlException: Invalid column name 'Rating'.`

`SqlException` 예외는 업데이트된 영화 모델 클래스가 데이터베이스의 영화 테이블의 스키마와 다르면 발생합니다. (데이터베이스 테이블에 `Rating` 열이 없습니다.)

이 오류를 해결할 수 있는 몇 가지 방법이 있습니다.

1. 새 모델 클래스 스키마를 사용하여 Entity Framework를 자동으로 삭제하고 데이터베이스를 다시 만듭니다. 이 방법은 개발 주기의 초기 단계에서 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다. 단점은 데이터베이스의 기존 데이터를 잃게 된다는 것입니다. 프로덕션 데이터베이스에서 이 방법을 사용하지 마세요. 테스트 데이터로 데이터베이스를 자동으로 시드하도록 스키마에서 DB를 삭제하고 이니셜라이저를 사용하는 것은 종종 앱을 개발하는 효율적인 방법입니다.

2. 모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다. 이 방법의 장점은 데이터가 유지된다는 점입니다. 이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행할 수 있습니다.

3. Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.

이 자습서의 경우 Code First 마이그레이션을 사용합니다.

새 열에 대한 값을 제공하도록 `SeedData` 클래스를 수정합니다. 샘플 변경은 아래에 표시되지만 각 `new Movie` 블록에 대해 이 변경을 수행하려고 합니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

[완료된 SeedData.cs 파일](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs)을 참조하세요.

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

* `Movie` 모델을 `Movie` DB 스키마와 비교합니다.
* DB 스키마를 새 모델로 마이그레이션하도록 코드를 만듭니다.

"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다. 마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.

`Update-Database` 명령은 프레임워크에게 스키마 변경 내용을 데이터베이스에 적용하고 기존 데이터를 보존하라고 명령합니다.

<a name="ssox"></a>

DB의 모든 레코드를 삭제하는 경우 이니셜라이저에서 DB를 시드하고 `Rating` 필드를 포함합니다. 브라우저 또는 [SSOX](xref:tutorials/razor-pages/sql#ssox)(Sql Server 개체 탐색기)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.

다른 옵션은 데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만드는 것입니다. SSOX에서 데이터베이스를 삭제하려면:

* SSOX에서 데이터베이스를 선택합니다.
* 데이터베이스를 마우스 오른쪽 단추로 클릭하고 *삭제* 를 선택합니다.
* **기존 연결 닫기** 를 선택합니다.
* **확인** 을 선택합니다.
* [PMC](xref:tutorials/razor-pages/new-field#pmc)에서 데이터베이스를 업데이트합니다.

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>데이터베이스를 삭제하고 다시 만들기

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

마이그레이션 폴더를 삭제합니다.  다음 명령을 사용하여 데이터베이스를 다시 만듭니다.

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
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

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

이 섹션에서는 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션으로 다음 작업을 수행합니다.

* 모델에 새 필드를 추가합니다.
* 데이터베이스로 새 필드 스키마 변경 내용을 마이그레이션합니다.

EF Code First를 사용하여 자동으로 데이터베이스를 만들 경우 Code First는

* 데이터베이스에 테이블을 추가하여 데이터베이스의 스키마가 생성된 모델 클래스와 동기화되어 있는지 여부를 추적합니다.
* 모델 클래스가 DB와 동기화되지 않으면 EF는 예외를 throw합니다.

동기화된 스키마/모델의 자동 확인을 통해 일관성이 없는 데이터베이스/코드 문제를 쉽게 찾을 수 있습니다.

## <a name="adding-a-rating-property-to-the-movie-model"></a>영화 모델에 등급 속성 추가

*Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

앱을 빌드합니다.

*Pages/Movies/Index.cshtml* 를 편집하고 `Rating` 필드를 추가합니다.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

다음 페이지를 업데이트합니다.

* 삭제 및 세부 정보 페이지에 `Rating` 필드를 추가합니다.
* [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml)을 `Rating` 필드로 업데이트합니다.
* 편집 페이지에 `Rating` 필드를 추가합니다.

앱은 새 필드를 포함하도록 DB가 업데이트될 때까지 작동하지 않습니다. 지금 실행하면 앱은 `SqlException`을 throw합니다.

`SqlException: Invalid column name 'Rating'.`

이 오류는 데이터베이스의 동영상 테이블의 스키마와 다른 업데이트된 동영상 모델 클래스로 인해 발생됩니다. (데이터베이스 테이블에 `Rating` 열이 없습니다.)

이 오류를 해결할 수 있는 몇 가지 방법이 있습니다.

1. 새 모델 클래스 스키마를 사용하여 Entity Framework를 자동으로 삭제하고 데이터베이스를 다시 만듭니다. 이 방법은 개발 주기의 초기 단계에서 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다. 단점은 데이터베이스의 기존 데이터를 잃게 된다는 것입니다. 프로덕션 데이터베이스에서 이 방법을 사용하지 마세요. 테스트 데이터로 데이터베이스를 자동으로 시드하도록 스키마에서 DB를 삭제하고 이니셜라이저를 사용하는 것은 종종 앱을 개발하는 효율적인 방법입니다.

2. 모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다. 이 방법의 장점은 데이터가 유지된다는 점입니다. 이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행할 수 있습니다.

3. Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.

이 자습서의 경우 Code First 마이그레이션을 사용합니다.

새 열에 대한 값을 제공하도록 `SeedData` 클래스를 수정합니다. 샘플 변경은 아래에 표시되지만 각 `new Movie` 블록에 대해 이 변경을 수행하려고 합니다.

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

* `Movie` 모델을 `Movie` DB 스키마와 비교합니다.
* DB 스키마를 새 모델로 마이그레이션하도록 코드를 만듭니다.

"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다. 마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.

`Update-Database` 명령은 스키마 변경 내용을 데이터베이스에 적용하기 위한 프레임워크를 알려줍니다.

<a name="ssox"></a>

DB의 모든 레코드를 삭제하는 경우 이니셜라이저에서 DB를 시드하고 `Rating` 필드를 포함합니다. 브라우저 또는 [SSOX](xref:tutorials/razor-pages/sql#ssox)(Sql Server 개체 탐색기)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.

다른 옵션은 데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만드는 것입니다. SSOX에서 데이터베이스를 삭제하려면:

* SSOX에서 데이터베이스를 선택합니다.
* 데이터베이스를 마우스 오른쪽 단추로 클릭하고 *삭제* 를 선택합니다.
* **기존 연결 닫기** 를 선택합니다.
* **확인** 을 선택합니다.
* [PMC](xref:tutorials/razor-pages/new-field#pmc)에서 데이터베이스를 업데이트합니다.

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>데이터베이스를 삭제하고 다시 만들기

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만듭니다. 데이터베이스를 삭제하려면 데이터베이스 파일( *MvcMovie.db* )을 삭제합니다. 그런 다음, `ef database update` 명령을 실행합니다.

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
