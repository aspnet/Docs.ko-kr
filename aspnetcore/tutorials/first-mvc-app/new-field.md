---
title: 8부. ASP.NET Core MVC 앱에 새 필드 추가
author: rick-anderson
description: ASP.NET Core MVC에 대한 자습서 시리즈의 8부입니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 84e344aabc9171020c0117d55eaf1a95e6b768db
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422537"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a>8부. ASP.NET Core MVC 앱에 새 필드 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션으로 다음 작업을 수행합니다.

* 모델에 새 필드를 추가합니다.
* 새 필드를 데이터베이스로 마이그레이션합니다.

EF Code First를 사용하여 자동으로 데이터베이스를 만들 경우 Code First는 다음 작업을 수행합니다.

* 데이터베이스 스키마를 추적하기 위해 데이터베이스에 테이블을 추가합니다.
* 데이터베이스가 생성된 모델 클래스와 동기화되어 있는지 확인합니다. 동기화되어 있지 않다면 EF에서 예외를 던집니다. 이렇게 하면 더 쉽게 일관성이 없는 데이터베이스/코드 문제를 찾을 수 있습니다.

## <a name="add-a-rating-property-to-the-movie-model"></a>영화 모델에 Rating 속성 추가

*Models/Movie.cs* 에 `Rating` 속성을 추가합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?name=snippet)]

앱을 빌드합니다.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

 Ctrl+Shift+B

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

명령 ⌘ + B

------

`Movie` 클래스에 새 필드를 추가했으므로 이 새 속성이 포함되도록 속성 바인딩 목록을 업데이트해야 합니다. *MoviesController.cs* 에서 `Rating` 속성을 포함하도록 `Create` 및 `Edit` 작업 메서드에 대한 `[Bind]` 특성을 수정합니다.

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

브라우저 보기에서 새 `Rating` 속성을 표시, 작성 및 편집하기 위해 보기 템플릿을 수정합니다.

*/Views/Movies/Index.cshtml* 파일을 편집하고 `Rating` 필드를 추가합니다.

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Movies/Index.cshtml?highlight=28-49&range=12-51)]

::: moniker-end

`Rating` 필드를 사용하여 */Views/Movies/Create.cshtml* 을 수정합니다.

# <a name="visual-studio--visual-studio-for-mac"></a>[Visual Studio / Visual Studio for Mac](#tab/visual-studio+visual-studio-mac)

이전 "양식 그룹"을 복사/붙여넣기하고 intelliSense에서 필드를 업데이트하도록 할 수 있습니다. IntelliSense는 [태그 도우미](xref:mvc/views/tag-helpers/intro)와 함께 작동합니다.

![개발자가 보기의 두 번째 레이블 요소에서 asp-for의 특성 값에 대해 문자 R을 입력했습니다. 목록에서 자동으로 강조 표시되는 Rating을 비롯한 사용 가능한 필드를 보여 주는 Intellisense 상황에 맞는 메뉴가 나타납니다. 개발자가 필드를 클릭하거나 키보드에서 Enter 키를 누르면 값이 Rating으로 설정됩니다.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

나머지 템플릿을 수정합니다.

새 열에 대한 값을 제공하도록 `SeedData` 클래스를 수정합니다. 아래에서 변경 예제를 볼 수 있지만 각 `new Movie`마다 이 변경을 수행해야 합니다.

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

새 필드를 포함하도록 DB가 수정될 때까지 앱은 작동하지 않습니다. 지금 앱을 실행하면 `SqlException`이 던져집니다.

`SqlException: Invalid column name 'Rating'.`

이 오류는 수정된 Movie 모델 클래스가 기존 데이터베이스의 Movie 테이블 스키마와 다르기 때문에 발생합니다. (데이터베이스 테이블에 `Rating` 열이 없습니다.)

이 오류를 해결할 수 있는 몇 가지 방법이 있습니다.

1. Entity Framework에서 새 모델 클래스 스키마에 따라 데이터베이스를 자동으로 삭제하고 다시 만들도록 합니다. 이 방법은 테스트 데이터베이스에서 활발한 개발을 수행할 때 개발 주기의 초기 단계에서 매우 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다. 그러나 단점은 데이터베이스에서 기존 데이터를 손실한다는 것입니다. 따라서 프로덕션 데이터베이스에서 이 방법을 사용하지 않으려 합니다. 테스트 데이터로 데이터베이스를 자동으로 시드하는 데 이니셜라이저를 사용하는 것은 종종 애플리케이션을 개발하는 효율적인 방법입니다. 이는 초기 개발과 SQLite를 사용할 때 좋은 방법입니다.

2. 모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다. 이 방법의 장점은 데이터가 유지된다는 점입니다. 이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행할 수 있습니다.

3. Code First 마이그레이션을 사용하여 데이터베이스 스키마를 수정합니다.

이 자습서에서는 Code First 마이그레이션을 사용합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔** 을 선택합니다.

  ![PMC 메뉴](adding-model/_static/pmc.png)

PMC에서 다음 명령을 입력합니다.

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` 명령은 마이그레이션 프레임워크에서 현재 `Movie` DB 스키마로 현재 `Movie` 모델을 검사하고 DB를 새 모델로 마이그레이션하는 데 필요한 코드를 만들도록 합니다.

"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다. 마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.

DB의 모든 레코드가 삭제되면 이니셜라이즈 메서드가 DB를 시드하고 `Rating` 필드를 포함합니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

데이터베이스와 이전 마이그레이션을 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만듭니다.

```dotnetcli
dotnet ef migrations remove
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

`dotnet ef migrations remove`는 마지막 마이그레이션을 제거합니다. 둘 이상의 마이그레이션이 있는 경우 마이그레이션 폴더를 삭제합니다.

---
<!-- End of VS tabs -->

앱을 실행하고 `Rating` 필드를 사용하여 영화를 생성, 편집, 표시할 수 있는지 확인합니다.

> [!div class="step-by-step"]
> [이전](search.md)
> [다음](validation.md)
