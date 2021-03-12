---
title: ASP.NET Core의 보기에 종속성 주입
author: ardalis
description: ASP.NET Core가 MVC 보기에 종속성 주입을 지원하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
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
uid: mvc/views/dependency-injection
ms.openlocfilehash: 9d4920a7b572163b048bfb06029d913cee3f9e5a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587255"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a>ASP.NET Core의 보기에 종속성 주입

작성자: [Steve Smith](https://ardalis.com/)

ASP.NET Core는 보기에 대한 [종속성 주입](xref:fundamentals/dependency-injection)을 지원합니다. 이는 보기 요소를 채우는 데만 필요한 지역화 또는 데이터 같은 보기 관련 서비스에 유용합니다. 컨트롤러와 보기 간에 [문제를 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)해야 합니다. 보기에서 표시하는 대부분의 데이터는 컨트롤러에서 전달되어야 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/dependency-injection/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="configuration-injection"></a>구성 주입

*appsettings.json* 값을 뷰에 직접 삽입할 수 있습니다.

파일의 예 *appsettings.json* :

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

`@inject`에 대한 구문: `@inject <type> <name>`

`@inject`를 사용하는 예제:

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a>서비스 주입

`@inject` 지시문을 사용하여 서비스를 보기에 주입할 수 있습니다. `@inject`를 보기에 속성을 추가하고 DI를 사용하여 속성을 채우는 것으로 생각하셔도 좋습니다.

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

이 보기는 전체 통계를 보여주는 요약 정보와 함께 `ToDoItem` 인스턴스 목록을 표시합니다. 요약 정보는 주입된 `StatisticsService`에서 채워집니다. 이 서비스는 *Startup.cs* 의 `ConfigureServices`에서 종속성 주입을 위해 등록됩니다.

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

`StatisticsService`는 리포지토리를 통해 액세스되는 `ToDoItem` 인스턴스 집합에서 몇 가지 계산을 수행합니다.

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

예제 리포지토리는 메모리 내 컬렉션을 사용합니다. 위에서 살펴본 구현은 메모리 내의 모든 데이터를 대상으로 작동하므로 원격으로 액세스되는 대규모 데이터 집합에는 권장하지 않습니다.

이 예제는 보기에 바인딩된 모델과 보기에 주입된 서비스의 데이터를 표시합니다.

![총 항목, 완료된 항목, 평균 우선 순위, 작업 목록을 해당 우선 순위 수준 및 작업 완료를 나타내는 부울 값과 함께 나열하는 할 일 보기입니다.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a>조회 데이터 채우기

보기 주입은 드롭다운 목록 같은 UI 요소의 옵션을 채우는 데 유용합니다. 성별, 상태 및 기타 기본 설정을 지정하는 옵션이 포함된 사용자 프로필 양식을 고려해보세요. 표준 MVC 방식을 사용하여 이러한 양식을 렌더링하려면 컨트롤러에서 이러한 각 옵션 집합에 대한 데이터 액세스 서비스를 요청한 후 모델 또는 `ViewBag`을 바인딩할 각 옵션 집합으로 채워야합니다.

다른 방법은 서비스를 보기에 직접 주입하여 옵션을 가져오는 것입니다. 이 방법은 컨트롤러에 필요한 코드 양을 최소화하고, 보기 요소 생성 논리를 보기 자체로 이동합니다. 프로필 편집 양식을 표시하는 컨트롤러 작업은 프로필 인스턴스 양식만 전달하면 됩니다.

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

이러한 기본 설정을 수정하기 위한 HTML 양식으로는 세 가지 속성에 대한 드롭다운 목록을 포함합니다.

![이름, 성별, 상태 및 좋아하는 색을 입력할 수 있는 양식을 제공하는 프로필 업데이트 보기.](dependency-injection/_static/updateprofile.png)

이러한 목록은 보기에 주입된 서비스에 의해서 채워집니다.

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

`ProfileOptionsService`는 이 양식에 필요한 데이터만 제공하도록 설계된 UI 수준 서비스입니다.

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> `Startup.ConfigureServices`에서 종속성 주입을 통해 요청할 형식을 등록하는 것을 잊지 마세요. 서비스 공급자가 [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice)를 통해 내부적으로 쿼리되기 때문에 등록되지 않은 형식은 런타임 시 예외를 throw합니다.

## <a name="overriding-services"></a>서비스 재정의

새 서비스 주입 외에도, 이 기술은 페이지에서 이전에 주입된 서비스를 재정의하는 데 사용할 수도 있습니다. 아래 그림은 첫 번째 예에서 사용된 페이지에서 사용 가능한 모든 필드를 보여줍니다.

![입력된 @ 기호에서 Html, 구성 요소, StatsService 및 Url 필드를 나열하는 Intellisense 상황에 맞는 메뉴](dependency-injection/_static/razor-fields.png)

볼 수 있는 것처럼 기본 필드에는 `Html`, `Component` 및 `Url`(그리고 우리가 주입한 `StatsService`)이 포함됩니다. 예를 들어 기본 HTML 도우미를 개발자 고유의 도우미로 바꾸려면 간단하게 `@inject`를 사용하면 됩니다.

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

기존 서비스를 확장하려는 경우 간단하게 이 기술을 사용하여 기존 구현에서 상속하거나 기존 구현을 개발자 고유의 구현으로 래핑하면 됩니다.

## <a name="see-also"></a>참고 항목

* Simon Timms 블로그: [보기로 조회 데이터 가져오기](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)
