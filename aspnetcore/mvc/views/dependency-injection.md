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
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="0ea2f-103">ASP.NET Core의 보기에 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="0ea2f-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="0ea2f-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0ea2f-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0ea2f-105">ASP.NET Core는 보기에 대한 [종속성 주입](xref:fundamentals/dependency-injection)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="0ea2f-106">이는 보기 요소를 채우는 데만 필요한 지역화 또는 데이터 같은 보기 관련 서비스에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="0ea2f-107">컨트롤러와 보기 간에 [문제를 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="0ea2f-108">보기에서 표시하는 대부분의 데이터는 컨트롤러에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="0ea2f-109">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/dependency-injection/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0ea2f-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration-injection"></a><span data-ttu-id="0ea2f-110">구성 주입</span><span class="sxs-lookup"><span data-stu-id="0ea2f-110">Configuration injection</span></span>

<span data-ttu-id="0ea2f-111">*appsettings.json* 값을 뷰에 직접 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-111">*appsettings.json* values can be injected directly into a view.</span></span>

<span data-ttu-id="0ea2f-112">파일의 예 *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="0ea2f-112">Example of an *appsettings.json* file:</span></span>

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

<span data-ttu-id="0ea2f-113">`@inject`에 대한 구문: `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="0ea2f-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="0ea2f-114">`@inject`를 사용하는 예제:</span><span class="sxs-lookup"><span data-stu-id="0ea2f-114">An example using `@inject`:</span></span>

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a><span data-ttu-id="0ea2f-115">서비스 주입</span><span class="sxs-lookup"><span data-stu-id="0ea2f-115">Service injection</span></span>

<span data-ttu-id="0ea2f-116">`@inject` 지시문을 사용하여 서비스를 보기에 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-116">A service can be injected into a view using the `@inject` directive.</span></span> <span data-ttu-id="0ea2f-117">`@inject`를 보기에 속성을 추가하고 DI를 사용하여 속성을 채우는 것으로 생각하셔도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-117">You can think of `@inject` as adding a property to the view, and populating the property using DI.</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="0ea2f-118">이 보기는 전체 통계를 보여주는 요약 정보와 함께 `ToDoItem` 인스턴스 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-118">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="0ea2f-119">요약 정보는 주입된 `StatisticsService`에서 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-119">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="0ea2f-120">이 서비스는 *Startup.cs* 의 `ConfigureServices`에서 종속성 주입을 위해 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-120">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="0ea2f-121">`StatisticsService`는 리포지토리를 통해 액세스되는 `ToDoItem` 인스턴스 집합에서 몇 가지 계산을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-121">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="0ea2f-122">예제 리포지토리는 메모리 내 컬렉션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-122">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="0ea2f-123">위에서 살펴본 구현은 메모리 내의 모든 데이터를 대상으로 작동하므로 원격으로 액세스되는 대규모 데이터 집합에는 권장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-123">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="0ea2f-124">이 예제는 보기에 바인딩된 모델과 보기에 주입된 서비스의 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-124">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![총 항목, 완료된 항목, 평균 우선 순위, 작업 목록을 해당 우선 순위 수준 및 작업 완료를 나타내는 부울 값과 함께 나열하는 할 일 보기입니다.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="0ea2f-126">조회 데이터 채우기</span><span class="sxs-lookup"><span data-stu-id="0ea2f-126">Populating Lookup Data</span></span>

<span data-ttu-id="0ea2f-127">보기 주입은 드롭다운 목록 같은 UI 요소의 옵션을 채우는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-127">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="0ea2f-128">성별, 상태 및 기타 기본 설정을 지정하는 옵션이 포함된 사용자 프로필 양식을 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-128">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="0ea2f-129">표준 MVC 방식을 사용하여 이러한 양식을 렌더링하려면 컨트롤러에서 이러한 각 옵션 집합에 대한 데이터 액세스 서비스를 요청한 후 모델 또는 `ViewBag`을 바인딩할 각 옵션 집합으로 채워야합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-129">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="0ea2f-130">다른 방법은 서비스를 보기에 직접 주입하여 옵션을 가져오는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-130">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="0ea2f-131">이 방법은 컨트롤러에 필요한 코드 양을 최소화하고, 보기 요소 생성 논리를 보기 자체로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-131">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="0ea2f-132">프로필 편집 양식을 표시하는 컨트롤러 작업은 프로필 인스턴스 양식만 전달하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-132">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="0ea2f-133">이러한 기본 설정을 수정하기 위한 HTML 양식으로는 세 가지 속성에 대한 드롭다운 목록을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-133">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![이름, 성별, 상태 및 좋아하는 색을 입력할 수 있는 양식을 제공하는 프로필 업데이트 보기.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="0ea2f-135">이러한 목록은 보기에 주입된 서비스에 의해서 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-135">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="0ea2f-136">`ProfileOptionsService`는 이 양식에 필요한 데이터만 제공하도록 설계된 UI 수준 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-136">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="0ea2f-137">`Startup.ConfigureServices`에서 종속성 주입을 통해 요청할 형식을 등록하는 것을 잊지 마세요.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-137">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0ea2f-138">서비스 공급자가 [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice)를 통해 내부적으로 쿼리되기 때문에 등록되지 않은 형식은 런타임 시 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-138">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="0ea2f-139">서비스 재정의</span><span class="sxs-lookup"><span data-stu-id="0ea2f-139">Overriding Services</span></span>

<span data-ttu-id="0ea2f-140">새 서비스 주입 외에도, 이 기술은 페이지에서 이전에 주입된 서비스를 재정의하는 데 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-140">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="0ea2f-141">아래 그림은 첫 번째 예에서 사용된 페이지에서 사용 가능한 모든 필드를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-141">The figure below shows all of the fields available on the page used in the first example:</span></span>

![입력된 @ 기호에서 Html, 구성 요소, StatsService 및 Url 필드를 나열하는 Intellisense 상황에 맞는 메뉴](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="0ea2f-143">볼 수 있는 것처럼 기본 필드에는 `Html`, `Component` 및 `Url`(그리고 우리가 주입한 `StatsService`)이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-143">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="0ea2f-144">예를 들어 기본 HTML 도우미를 개발자 고유의 도우미로 바꾸려면 간단하게 `@inject`를 사용하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-144">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="0ea2f-145">기존 서비스를 확장하려는 경우 간단하게 이 기술을 사용하여 기존 구현에서 상속하거나 기존 구현을 개발자 고유의 구현으로 래핑하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0ea2f-145">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="0ea2f-146">참고 항목</span><span class="sxs-lookup"><span data-stu-id="0ea2f-146">See Also</span></span>

* <span data-ttu-id="0ea2f-147">Simon Timms 블로그: [보기로 조회 데이터 가져오기](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="0ea2f-147">Simon Timms Blog: [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
