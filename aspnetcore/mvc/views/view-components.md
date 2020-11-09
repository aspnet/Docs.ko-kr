---
title: ASP.NET Core의 보기 구성 요소
author: rick-anderson
description: ASP.NET Core에서 보기 구성 요소가 사용되는 방법 및 이를 앱에 추가하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/view-components
ms.openlocfilehash: e0ff97b53d12fbf6c6a89e94704de1aee9d7f9e6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060588"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="24e73-103">ASP.NET Core의 보기 구성 요소</span><span class="sxs-lookup"><span data-stu-id="24e73-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="24e73-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="24e73-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="24e73-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="24e73-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="24e73-106">보기 구성 요소</span><span class="sxs-lookup"><span data-stu-id="24e73-106">View components</span></span>

<span data-ttu-id="24e73-107">뷰 구성 요소는 부분 보기와 유사하지만 훨씬 강력합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="24e73-108">뷰 구성 요소는 모델 바인딩을 사용하지 않으며 호출할 때 제공되는 데이터에만 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="24e73-109">이 문서는 컨트롤러 및 뷰를 사용 하 여 작성 되었지만 페이지 에서도 보기 구성 요소를 사용할 수 Razor 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="24e73-110">뷰 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="24e73-110">A view component:</span></span>

* <span data-ttu-id="24e73-111">전체 응답보다는 청크를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="24e73-112">컨트롤러 및 보기 간에서 발견할 수 있는 것과 동일한 문제의 분리 및 테스트 가능성의 이점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="24e73-113">매개 변수 및 비즈니스 논리를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="24e73-114">일반적으로 레이아웃 페이지에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="24e73-115">보기 구성 요소는 다음과 같이 부분 보기로는 너무 복잡한 재사용 가능한 렌더링 논리를 갖는 모든 곳에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="24e73-116">동적 탐색 메뉴</span><span class="sxs-lookup"><span data-stu-id="24e73-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="24e73-117">태그 클라우드(여기서는 데이터베이스를 쿼리합니다.)</span><span class="sxs-lookup"><span data-stu-id="24e73-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="24e73-118">로그인 패널</span><span class="sxs-lookup"><span data-stu-id="24e73-118">Login panel</span></span>
* <span data-ttu-id="24e73-119">쇼핑 카트</span><span class="sxs-lookup"><span data-stu-id="24e73-119">Shopping cart</span></span>
* <span data-ttu-id="24e73-120">최근에 게시된 문서</span><span class="sxs-lookup"><span data-stu-id="24e73-120">Recently published articles</span></span>
* <span data-ttu-id="24e73-121">일반적인 블로그의 사이드바 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="24e73-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="24e73-122">모든 페이지에 렌더링되고 사용자의 로그인 상태에 따라 로그아웃 또는 로그인 링크를 표시하는 로그인 패널</span><span class="sxs-lookup"><span data-stu-id="24e73-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="24e73-123">보기 구성 요소는 클래스(일반적으로 [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)에서 파생됨)와 보기 구성 요소가 반환하는 결과(일반적으로 보기)의 두 부분으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="24e73-124">컨트롤러와 마찬가지로, 뷰 구성 요소는 POCO일 수 있지만 대부분의 개발자는 `ViewComponent`에서 파생되어 사용 가능한 메서드와 속성을 활용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="24e73-125">뷰 구성 요소가 앱의 사양을 충족 하는지 고려할 때 구성 요소를 대신 사용 하는 것이 좋습니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="24e73-125">When considering if view components meet an app's specifications, consider using Razor Components instead.</span></span> <span data-ttu-id="24e73-126">Razor 또한 구성 요소는 태그를 c # 코드와 결합 하 여 재사용 가능한 UI 단위를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-126">Razor Components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="24e73-127">Razor 구성 요소는 클라이언트 쪽 UI 논리 및 컴퍼지션을 제공할 때 개발자 생산성을 위해 설계 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-127">Razor Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="24e73-128">자세한 내용은 <xref:blazor/components/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="24e73-128">For more information, see <xref:blazor/components/index>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="24e73-129">뷰 구성 요소 만들기</span><span class="sxs-lookup"><span data-stu-id="24e73-129">Creating a view component</span></span>

<span data-ttu-id="24e73-130">이 섹션에서는 보기 구성 요소를 만들기 위한 높은 수준의 요구 사항이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="24e73-131">이 문서의 뒷부분에서는 각 단계를 자세히 검토하고 보기 구성 요소를 만들어봅니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="24e73-132">보기 구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="24e73-132">The view component class</span></span>

<span data-ttu-id="24e73-133">다음 중 한 가지 방법을 사용하여 보기 구성 요소 클래스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="24e73-134">*ViewComponent* 에서 파생</span><span class="sxs-lookup"><span data-stu-id="24e73-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="24e73-135">`[ViewComponent]` 특성을 사용하여 클래스를 데코레이팅하거나 `[ViewComponent]` 특성이 사용된 클래스에서 파생</span><span class="sxs-lookup"><span data-stu-id="24e73-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="24e73-136">이름이 *ViewComponent* 접미사로 끝나는 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="24e73-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="24e73-137">컨트롤러와 마찬가지로 보기 구성 요소는 공용, 비중첩 및 비추상 클래스이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="24e73-138">보기 구성 요소의 이름은 "ViewComponent" 접미사가 제거된 클래스 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="24e73-139">또한 `ViewComponentAttribute.Name` 속성을 사용하여 명시적으로 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="24e73-140">보기 구성 요소 클래스는 다음과 같은 특징을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-140">A view component class:</span></span>

* <span data-ttu-id="24e73-141">생성자 [종속성 주입](../../fundamentals/dependency-injection.md)을 완벽하게 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="24e73-142">컨트롤러 수명 주기를 따르지 않습니다. 즉, 뷰 구성 요소에 [필터](../controllers/filters.md)를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="24e73-143">뷰 구성 요소 메서드</span><span class="sxs-lookup"><span data-stu-id="24e73-143">View component methods</span></span>

<span data-ttu-id="24e73-144">보기 구성 요소는 `Task<IViewComponentResult>`를 반환하는 `InvokeAsync` 메서드 또는 `IViewComponentResult`를 반환하는 동기 `Invoke` 메서드에서 해당 논리를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="24e73-145">매개 변수는 모델 바인딩이 아닌 뷰 구성 요소 호출에서 직접 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="24e73-146">보기 구성 요소는 요청을 직접 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-146">A view component never directly handles a request.</span></span> <span data-ttu-id="24e73-147">일반적으로 보기 구성 요소는 모델을 초기화하고 `View` 메서드를 호출하여 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="24e73-148">요약하자면, 뷰 구성 요소 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-148">In summary, view component methods:</span></span>

* <span data-ttu-id="24e73-149">`Task<IViewComponentResult>`를 반환하는 `InvokeAsync` 메서드 또는 `IViewComponentResult`를 반환하는 동기 `Invoke` 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="24e73-150">일반적으로 모델을 초기화 하 고 메서드를 호출 하 여 뷰에 전달 `ViewComponent` `View` 합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="24e73-151">매개 변수는 HTTP가 아닌 호출 메서드에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="24e73-152">모델 바인딩이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-152">There's no model binding.</span></span>
* <span data-ttu-id="24e73-153">HTTP 엔드포인트로 직접 연결할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="24e73-154">(일반적으로 보기의) 코드에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="24e73-155">보기 구성 요소는 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-155">A view component never handles a request.</span></span>
* <span data-ttu-id="24e73-156">현재 HTTP 요청의 세부 정보가 아닌 시그니처에 오버로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="24e73-157">뷰 검색 경로</span><span class="sxs-lookup"><span data-stu-id="24e73-157">View search path</span></span>

<span data-ttu-id="24e73-158">런타임은 다음 경로에서 뷰를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="24e73-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="24e73-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="24e73-160">/Views/Shared/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="24e73-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="24e73-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="24e73-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="24e73-162">검색 경로는 컨트롤러 + 뷰 및 페이지를 사용 하는 프로젝트에 적용 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="24e73-162">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="24e73-163">뷰 구성 요소에 대한 기본 뷰 이름은 *Default* 이며 이것은 일반적으로 뷰 파일의 이름이 *Default.cshtml* 로 지정됨을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-163">The default view name for a view component is *Default* , which means your view file will typically be named *Default.cshtml* .</span></span> <span data-ttu-id="24e73-164">뷰 구성 요소 결과를 만들거나 `View` 메서드를 호출할 때 다른 뷰 이름을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="24e73-165">뷰 파일 이름을 *Default.cshtml* 로 지정하고 *Views/Shared/Components/{View Component Name}/{View Name}* 경로를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="24e73-166">이 샘플에 사용된 `PriorityList` 뷰 구성 요소는 뷰 구성 요소 보기에 *Views/Shared/Components/PriorityList/Default.cshtml* 을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

### <a name="customize-the-view-search-path"></a><span data-ttu-id="24e73-167">보기 검색 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="24e73-167">Customize the view search path</span></span>

<span data-ttu-id="24e73-168">보기 검색 경로를 사용자 지정 하려면 Razor 의 컬렉션을 수정 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> 합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-168">To customize the view search path, modify Razor's <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> collection.</span></span> <span data-ttu-id="24e73-169">예를 들어 "/Components/{View Component Name}/{View Name}" 경로 내에서 보기를 검색하려면 컬렉션에 새 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-169">For example, to search for views within the path "/Components/{View Component Name}/{View Name}", add a new item to the collection:</span></span>

[!code-csharp[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

<span data-ttu-id="24e73-170">이전 코드에서 "{0}" 자리 표시자는 "Components/{View Component Name}/{View Name}" 경로를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-170">In the preceding code, the placeholder "{0}" represents the path "Components/{View Component Name}/{View Name}".</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="24e73-171">보기 구성 요소 호출</span><span class="sxs-lookup"><span data-stu-id="24e73-171">Invoking a view component</span></span>

<span data-ttu-id="24e73-172">보기 구성 요소를 사용하려면 보기에서 다음을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-172">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="24e73-173">매개 변수가 `InvokeAsync` 메서드에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-173">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="24e73-174">`PriorityList`아티클에서 개발한 뷰 구성 요소는 *Views/ToDo/Index. cshtml* 뷰 파일에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-174">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="24e73-175">다음에서 `InvokeAsync` 메서드는 두 매개 변수를 사용하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-175">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="24e73-176">태그 도우미로 보기 구성 요소 호출</span><span class="sxs-lookup"><span data-stu-id="24e73-176">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="24e73-177">ASP.NET Core 1.1 이상에서는 뷰 구성 요소를 [태그 도우미](xref:mvc/views/tag-helpers/intro)로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-177">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="24e73-178">태그 도우미에 대한 파스칼식 클래스 및 메서드 매개 변수는 [케밥식](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)으로 번역됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-178">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="24e73-179">뷰 구성 요소를 호출하는 태그 도우미는 `<vc></vc>` 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-179">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="24e73-180">뷰 구성 요소는 다음과 같이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-180">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="24e73-181">뷰 구성 요소를 태그 도우미로 사용하려면 `@addTagHelper` 지시문을 사용하여 뷰 구성 요소가 포함된 어셈블리를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-181">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="24e73-182">보기 구성 요소가 `MyWebApp`이라는 어셈블리에 있는 경우 다음 지시문을 *_ViewImports.cshtml* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-182">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="24e73-183">뷰 구성 요소를 참조하는 모든 파일에 뷰 구성 요소를 태그 도우미로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-183">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="24e73-184">태그 도우미를 등록하는 방법에 대한 자세한 내용은 [태그 도우미 범위 관리](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="24e73-184">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="24e73-185">이 자습서에 사용된 `InvokeAsync` 메서드:</span><span class="sxs-lookup"><span data-stu-id="24e73-185">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="24e73-186">태그 도우미 태그:</span><span class="sxs-lookup"><span data-stu-id="24e73-186">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="24e73-187">위의 샘플에서 `PriorityList` 뷰 구성 요소는 `priority-list`가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-187">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="24e73-188">보기 구성 요소에 대한 매개 변수는 케밥식의 특성으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-188">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="24e73-189">컨트롤러에서 뷰 구성 요소 직접 호출</span><span class="sxs-lookup"><span data-stu-id="24e73-189">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="24e73-190">일반적으로 보기 구성 요소는 보기에서 호출되지만 컨트롤러 메서드에서 직접 호출할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-190">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="24e73-191">보기 구성 요소는 컨트롤러 같은 엔드포인트를 정의하지 않지만 `ViewComponentResult`의 내용을 반환하는 컨트롤러 동작을 쉽게 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-191">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="24e73-192">이 예제에서는 뷰 구성 요소가 컨트롤러에서 직접 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-192">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="24e73-193">연습: 간단한 뷰 구성 요소 만들기</span><span class="sxs-lookup"><span data-stu-id="24e73-193">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="24e73-194">시작 코드를 [다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), 빌드 및 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-194">[Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="24e73-195">`ToDo` *ToDo* 항목 목록을 표시 하는 컨트롤러를 포함 하는 간단한 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-195">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![ToDo 목록](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="24e73-197">ViewComponent 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="24e73-197">Add a ViewComponent class</span></span>

<span data-ttu-id="24e73-198">*ViewComponents* 폴더를 만들고 다음 `PriorityListViewComponent` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-198">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="24e73-199">코드에 대한 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="24e73-199">Notes on the code:</span></span>

* <span data-ttu-id="24e73-200">보기 구성 요소 클래스는 프로젝트의 **모든** 폴더에 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-200">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="24e73-201">클래스 이름 PriorityList **ViewComponent** 는 **ViewComponent** 접미사로 끝나기 때문에 런타임이 보기에서 클래스 구성 요소를 참조할 때 "PriorityList" 문자열을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-201">Because the class name PriorityList **ViewComponent** ends with the suffix **ViewComponent** , the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="24e73-202">나중에 보다 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="24e73-203">`[ViewComponent]` 특성은 보기 구성 요소를 참조하는 데 사용되는 이름을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-203">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="24e73-204">예를 들어 클래스의 이름을 `XYZ`로 지정하고 `ViewComponent` 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-204">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="24e73-205">위의 `[ViewComponent]` 특성은 구성 요소와 연관된 보기를 찾을 때 `PriorityList` 이름을 사용하고, 보기에서 클래스 구성 요소를 참조할 때 "PriorityList" 문자열을 사용하도록 보기 구성 요소 선택기에게 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-205">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="24e73-206">나중에 보다 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-206">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="24e73-207">이 구성 요소에서는 [종속성 주입](../../fundamentals/dependency-injection.md)을 사용하여 데이터 컨텍스트를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-207">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="24e73-208">`InvokeAsync`는 보기에서 호출할 수 있는 메서드를 노출하며 임의의 개수의 인수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-208">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="24e73-209">`InvokeAsync` 메서드는 `isDone` 및 `maxPriority` 매개 변수를 만족하는 `ToDo` 항목 집합을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-209">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-no-locrazor-view"></a><span data-ttu-id="24e73-210">뷰 구성 요소 Razor 보기 만들기</span><span class="sxs-lookup"><span data-stu-id="24e73-210">Create the view component Razor view</span></span>

* <span data-ttu-id="24e73-211">*Views/Shared/Components* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-211">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="24e73-212">이 폴더의 이름은 *Components* 로 **지정되어야만** 합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-212">This folder **must** be named *Components* .</span></span>

* <span data-ttu-id="24e73-213">*Views/Shared/Components/PriorityList* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-213">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="24e73-214">이 폴더 이름은 보기 구성 요소 클래스의 이름 또는 클래스 이름에서 접미사를 뺀 이름과 일치해야 합니다(규칙을 준수하고 클래스 이름에 *ViewComponent* 접미사를 사용한 경우).</span><span class="sxs-lookup"><span data-stu-id="24e73-214">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="24e73-215">`ViewComponent` 특성을 사용한 경우 클래스 이름은 특성 지정과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-215">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="24e73-216">*Views/Shared/Components/PriorityList/Default* 뷰를 만듭니다. Razor</span><span class="sxs-lookup"><span data-stu-id="24e73-216">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="24e73-217">뷰를 사용 하 여 Razor 목록을 `TodoItem` 표시 하 고 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-217">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="24e73-218">뷰 구성 요소 `InvokeAsync` 메서드가 뷰의 이름을 전달하지 않은 경우(샘플에서처럼) 규칙에 따라 뷰 이름으로 *Default* 가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-218">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="24e73-219">자습서의 뒷부분에서 뷰 이름을 전달하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-219">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="24e73-220">특정 컨트롤러에 대 한 기본 스타일을 재정의 하려면 뷰를 컨트롤러 관련 뷰 폴더에 추가 합니다 (예: *Views/ToDo/Components/PriorityList/default. cshtml)* .</span><span class="sxs-lookup"><span data-stu-id="24e73-220">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)* .</span></span>

    <span data-ttu-id="24e73-221">뷰 구성 요소가 컨트롤러에 특정 한 경우에는 컨트롤러 관련 폴더 ( *Views/ToDo/Components/PriorityList/Default. cshtml* )에 해당 구성 요소를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-221">If the view component is controller-specific, you can add it to the controller-specific folder ( *Views/ToDo/Components/PriorityList/Default.cshtml* ).</span></span>

* <span data-ttu-id="24e73-222">`div`우선 순위 목록 구성 요소에 대 한 호출을 포함 하는을 *Views/ToDo/index. cshtml* 파일의 아래쪽에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-222">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="24e73-223">`@await Component.InvokeAsync` 태그는 호출하는 뷰 구성 요소에 대한 구문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-223">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="24e73-224">첫 번째 인수는 실행하거나 호출하려는 구성 요소의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-224">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="24e73-225">후속 매개 변수는 구성 요소에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-225">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="24e73-226">`InvokeAsync`는 임의 개수의 인수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-226">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="24e73-227">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-227">Test the app.</span></span> <span data-ttu-id="24e73-228">다음 이미지는 ToDo 목록 및 우선 순위 항목을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-228">The following image shows the ToDo list and the priority items:</span></span>

![todo 목록 및 우선 순위 항목](view-components/_static/pi.png)

<span data-ttu-id="24e73-230">또한 컨트롤러에서 보기 구성 요소를 직접 호출할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-230">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![IndexVC 작업에서 우선 순위 항목](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="24e73-232">보기 이름 지정</span><span class="sxs-lookup"><span data-stu-id="24e73-232">Specifying a view name</span></span>

<span data-ttu-id="24e73-233">복잡한 보기 구성 요소에서는 조건에 따라 기본값이 아닌 보기를 지정해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-233">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="24e73-234">다음 코드는 `InvokeAsync` 메서드에서 "PVC" 보기를 지정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-234">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="24e73-235">`PriorityListViewComponent` 클래스에서 `InvokeAsync` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-235">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="24e73-236">*Views/Shared/Components/PriorityList/Default.cshtml* 파일을 *Views/Shared/Components/PriorityList/PVC.cshtml* 이라는 보기로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-236">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml* .</span></span> <span data-ttu-id="24e73-237">PVC 뷰가 사용되었다는 것을 나타내는 제목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-237">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="24e73-238">*Views/ToDo/Index.cshtml* 을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-238">Update *Views/ToDo/Index.cshtml* :</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="24e73-239">앱을 실행하고 PVC 뷰를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-239">Run the app and verify PVC view.</span></span>

![우선 순위 보기 구성 요소](view-components/_static/pvc.png)

<span data-ttu-id="24e73-241">PVC 보기가 렌더링되지 않는다면 우선 순위가 4 이상인 보기 구성 요소를 호출하고 있는지 확인해봅니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-241">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="24e73-242">보기 경로 검토</span><span class="sxs-lookup"><span data-stu-id="24e73-242">Examine the view path</span></span>

* <span data-ttu-id="24e73-243">우선 순위 뷰가 반환되지 않도록 우선 순위 매개 변수를 3 이하로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-243">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="24e73-244">*Views/ToDo/Components/PriorityList/Default. cshtml* 의 이름을 *기본값인 1* 로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-244">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml* .</span></span>
* <span data-ttu-id="24e73-245">앱을 테스트하면 다음과 같은 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-245">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="24e73-246">*Views/ToDo/components/PriorityList/1Default* 를 *views/Shared/components/PriorityList/* 로 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-246">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml* .</span></span>
* <span data-ttu-id="24e73-247">*공유 폴더에서* 보기를 표시 하려면 *공유* 할 일 뷰 구성 요소 보기에 일부 태그를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-247">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="24e73-248">**Shared** 구성 요소 뷰를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-248">Test the **Shared** component view.</span></span>

![공유 구성 요소 뷰가 있는 ToDo 출력](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="24e73-250">하드 코드된 문자열 방지</span><span class="sxs-lookup"><span data-stu-id="24e73-250">Avoiding hard-coded strings</span></span>

<span data-ttu-id="24e73-251">컴파일 시간 안전성을 원하는 경우 하드 코드된 뷰 구성 요소 이름을 클래스 이름으로 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-251">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="24e73-252">"ViewComponent" 접미사 없이 보기 구성 요소를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-252">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="24e73-253">`using`뷰 파일에 문을 추가 하 Razor 고 연산자를 사용 합니다 `nameof` .</span><span class="sxs-lookup"><span data-stu-id="24e73-253">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="24e73-254">동기 작업 수행</span><span class="sxs-lookup"><span data-stu-id="24e73-254">Perform synchronous work</span></span>

<span data-ttu-id="24e73-255">비동기 작업을 수행할 필요가 없는 경우 프레임워크에서 동기 `Invoke` 메서드 호출을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-255">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="24e73-256">다음 메서드는 동기 `Invoke` 보기 구성 요소를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-256">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="24e73-257">뷰 구성 요소 파일에는 Razor 메서드에 전달 된 문자열이 나열 됩니다 `Invoke` ( *Views/Home/Components/PriorityList/Default. cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="24e73-257">The view component's Razor file lists the strings passed to the `Invoke` method ( *Views/Home/Components/PriorityList/Default.cshtml* ):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="24e73-258">뷰 구성 요소는 Razor 다음 방법 중 하나를 사용 하 여 파일 (예: *Views/Home/Index. cshtml* )에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-258">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml* ) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="24e73-259">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="24e73-259">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="24e73-260"><xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> 접근 방법을 사용하려면 `Component.InvokeAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-260">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="24e73-261">뷰 구성 요소는 Razor 를 사용 하 여 파일 (예: *Views/Home/Index. cshtml* )에서 호출 됩니다 <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> .</span><span class="sxs-lookup"><span data-stu-id="24e73-261">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml* ) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="24e73-262">`Component.InvokeAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-262">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="24e73-263">태그 도우미를 사용하려면 `@addTagHelper` 지시문을 사용하여 보기 구성 요소가 포함된 어셈블리를 등록합니다(보기 구성 요소는 `MyWebApp`이라는 어셈블리에 있음).</span><span class="sxs-lookup"><span data-stu-id="24e73-263">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="24e73-264">태그 파일에서 뷰 구성 요소 태그 도우미를 사용 합니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="24e73-264">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="24e73-265">의 메서드 시그니처는 `PriorityList.Invoke` 동기적 이지만 Razor 태그 파일의를 사용 하 여 메서드를 찾아서 호출 합니다 `Component.InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="24e73-265">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="24e73-266">모든 뷰 구성 요소 매개 변수 필요</span><span class="sxs-lookup"><span data-stu-id="24e73-266">All view component parameters are required</span></span>

<span data-ttu-id="24e73-267">뷰 구성 요소의 각 매개 변수는 필수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-267">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="24e73-268">이 [GitHub 문제](https://github.com/dotnet/AspNetCore/issues/5011)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="24e73-268">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="24e73-269">매개 변수가 하나라도 생략되면</span><span class="sxs-lookup"><span data-stu-id="24e73-269">If any  parameter is omitted:</span></span>

* <span data-ttu-id="24e73-270">`InvokeAsync` 메서드 시그니처가 일치하지 않게 되므로 메서드가 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-270">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="24e73-271">ViewComponent가 마크업을 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-271">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="24e73-272">오류가 throw되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="24e73-272">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="24e73-273">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="24e73-273">Additional resources</span></span>

* [<span data-ttu-id="24e73-274">보기에 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="24e73-274">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
