---
title: ASP.NET Core의 컨트롤러 작업에 라우팅
author: rick-anderson
description: ASP.NET Core MVC가 라우팅 미들웨어를 사용하여 들어오는 요청의 URL을 일치시키고 이를 작업에 매핑하는 방법을 알아봅니다.
ms.author: riande
ms.date: 3/25/2020
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
uid: mvc/controllers/routing
ms.openlocfilehash: 59ad373cefaa12370aa7c02a367125c7a94f59a6
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422602"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="7bc1e-103">ASP.NET Core의 컨트롤러 작업에 라우팅</span><span class="sxs-lookup"><span data-stu-id="7bc1e-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="7bc1e-104">작성자: [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7bc1e-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7bc1e-105">ASP.NET Core 컨트롤러는 라우팅 [미들웨어](xref:fundamentals/middleware/index) 를 사용 하 여 들어오는 요청의 url을 일치 시키고이를 [작업](#action)에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="7bc1e-106">경로 템플릿:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-106">Routes templates:</span></span>

* <span data-ttu-id="7bc1e-107">는 시작 코드 또는 특성에서 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="7bc1e-108">URL 경로가 [작업과](#action)일치 하는 방식을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="7bc1e-109">는 링크에 대 한 Url을 생성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="7bc1e-110">생성 된 링크는 일반적으로 응답에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="7bc1e-111">작업은 [일반적으로 라우트된 경로](#cr) 또는 [특성 라우팅](#ar)입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="7bc1e-112">컨트롤러 또는 [작업](#action) 에 경로를 배치 하면 특성이 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="7bc1e-113">자세한 내용은 [혼합 라우팅](#routing-mixed-ref-label)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="7bc1e-114">이 문서:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-114">This document:</span></span>

* <span data-ttu-id="7bc1e-115">MVC와 라우팅 간의 상호 작용에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="7bc1e-116">일반적인 MVC 앱이 라우팅 기능을 사용 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="7bc1e-117">모두 포함:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-117">Covers both:</span></span>
    * <span data-ttu-id="7bc1e-118">일반적으로 컨트롤러 및 뷰에 사용 되는 [라우팅](#cr) 입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="7bc1e-119">REST Api와 함께 사용 되는 *특성 라우팅* 입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="7bc1e-120">주로 REST Api에 대 한 라우팅에 관심이 있는 경우 [Rest api에 대 한 특성 라우팅](#ar) 섹션으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="7bc1e-121">라우팅 고급 라우팅에 대 한 자세한 내용은 [라우팅](xref:fundamentals/routing) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="7bc1e-122">ASP.NET Core 3.0에 추가 된 기본 라우팅 시스템 (끝점 라우팅 이라고 함)을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="7bc1e-123">호환성을 위해 이전 버전의 라우팅으로 컨트롤러를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="7bc1e-124">지침은 [2.2-3.0 마이그레이션 가이드](xref:migration/22-to-30) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="7bc1e-125">레거시 라우팅 시스템에 대 한 참조 자료는 [이 문서의 2.2 버전](xref:mvc/controllers/routing?view=aspnetcore-2.2) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="7bc1e-126">기본 경로 설정</span><span class="sxs-lookup"><span data-stu-id="7bc1e-126">Set up conventional route</span></span>

<span data-ttu-id="7bc1e-127">`Startup.Configure` 일반적으로 [기본 라우팅을](#crd)사용할 때 다음과 비슷한 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="7bc1e-128">호출 내 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 에서 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> 는 단일 경로를 만드는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> is used to create a single route.</span></span> <span data-ttu-id="7bc1e-129">단일 경로는 경로 라는 이름으로 지정 됩니다 `default` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-129">The single route is named `default` route.</span></span> <span data-ttu-id="7bc1e-130">컨트롤러 및 뷰를 사용 하는 대부분의 앱은 경로와 유사한 경로 템플릿을 사용 `default` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="7bc1e-131">REST Api는 [특성 라우팅을](#ar)사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="7bc1e-132">경로 템플릿 `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="7bc1e-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="7bc1e-133">URL 경로와 일치 합니다. `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="7bc1e-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="7bc1e-134">`{ controller = Products, action = Details, id = 5 }`경로를 토큰화 하 여 경로 값을 추출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="7bc1e-135">경로 값을 추출 하면 앱에 라는 컨트롤러와 작업이 포함 된 경우 일치 하는 항목이 생성 됩니다 `ProductsController` `Details` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="7bc1e-136">`/Products/Details/5` 모델은 `id = 5` 매개 변수를로 설정 하기 위해의 값을 바인딩합니다 `id` `5` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="7bc1e-137">자세한 내용은 [모델 바인딩](xref:mvc/models/model-binding) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="7bc1e-138">`{controller=Home}``Home`는를 기본값으로 정의 `controller` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="7bc1e-139">`{action=Index}``Index`는를 기본값으로 정의 `action` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="7bc1e-140">`?`의 문자는 `{id?}` 를 `id` 선택적으로 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="7bc1e-141">기본 및 선택적 경로 매개 변수는 매칭을 위해 URL 경로에 반드시 있어야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="7bc1e-142">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="7bc1e-143">URL 경로와 일치 `/` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="7bc1e-144">경로 값을 생성 `{ controller = Home, action = Index }` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="7bc1e-145">및에 대 한 값은 `controller` `action` 기본값을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="7bc1e-146">`id` URL 경로에 해당 세그먼트가 없기 때문에에서 값을 생성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="7bc1e-147">`/` 및 작업이 있는 경우에만 `HomeController` 일치 `Index` :</span><span class="sxs-lookup"><span data-stu-id="7bc1e-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="7bc1e-148">위의 컨트롤러 정의와 경로 템플릿을 사용 하 여 `HomeController.Index` 다음 URL 경로에 대해 작업을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="7bc1e-149">URL 경로는 `/` 경로 템플릿 기본 `Home` 컨트롤러 및 작업을 사용 합니다 `Index` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="7bc1e-150">URL 경로는 `/Home` 경로 템플릿 기본 작업을 사용 합니다 `Index` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="7bc1e-151">편의 메서드인 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>를 사용하여:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="7bc1e-152">대체</span><span class="sxs-lookup"><span data-stu-id="7bc1e-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="7bc1e-153">라우팅은 및 미들웨어를 사용 하 여 구성 됩니다 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middleware.</span></span> <span data-ttu-id="7bc1e-154">컨트롤러를 사용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-154">To use controllers:</span></span>
>
> * <span data-ttu-id="7bc1e-155"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A>안쪽 `UseEndpoints` 을 호출 하 여 라우트된 컨트롤러 [특성](#ar) 을 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="7bc1e-156"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>또는 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> 를 호출 하 여 대상 [conventionally routed](#cr) 컨트롤러 및 [특성 라우트된](#ar) 컨트롤러를 모두 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="7bc1e-157">규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="7bc1e-157">Conventional routing</span></span>

<span data-ttu-id="7bc1e-158">기존 라우팅은 컨트롤러 및 뷰에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="7bc1e-159">`default` 경로인:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="7bc1e-160">는 *규칙 기반 라우팅* 의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="7bc1e-161">이는 URL 경로에 대 한 *규칙* 을 설정 하기 때문에 *기존 라우팅* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="7bc1e-162">첫 번째 경로 세그먼트는 `{controller=Home}` 컨트롤러 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="7bc1e-163">두 번째 세그먼트 인은 `{action=Index}` [작업](#action) 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="7bc1e-164">세 번째 세그먼트는 `{id?}` 선택 사항에 사용 됩니다 `id` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="7bc1e-165">`?`의를 `{id?}` 사용 하면이 옵션을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="7bc1e-166">`id` 는 모델 엔터티에 매핑하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="7bc1e-167">이 경로를 사용 하 여 `default` URL 경로를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="7bc1e-168">`/Products/List` 작업에 매핑됩니다 `ProductsController.List` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="7bc1e-169">`/Blog/Article/17` 는에 매핑되고 `BlogController.Article` 일반적으로 `id` 매개 변수를 17에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="7bc1e-170">이 매핑:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-170">This mapping:</span></span>

* <span data-ttu-id="7bc1e-171">는 컨트롤러 및 [작업](#action) 이름만을 기반으로 **합니다.**</span><span class="sxs-lookup"><span data-stu-id="7bc1e-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="7bc1e-172">는 네임 스페이스, 소스 파일 위치 또는 메서드 매개 변수를 기반으로 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="7bc1e-173">기본 경로를 사용 하 여 기본 라우팅을 사용 하면 각 작업에 대 한 새 URL 패턴을 제공 하지 않고도 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="7bc1e-174">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 스타일 작업을 포함 하는 앱의 경우, 컨트롤러 간의 url에 대해 일관성을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="7bc1e-175">코드를 간소화 하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-175">Helps simplify the code.</span></span>
* <span data-ttu-id="7bc1e-176">UI를 보다 예측 가능 하 게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="7bc1e-177">`id`위의 코드에서은 경로 템플릿에서 선택적으로 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="7bc1e-178">URL의 일부로 제공 되는 선택적 ID 없이 작업을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="7bc1e-179">일반적으로 `id` URL에서이 생략 된 경우:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="7bc1e-180">`id` 모델 바인딩에서로 설정 됩니다 `0` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="7bc1e-181">데이터베이스 일치에서 엔터티를 찾을 수 없습니다 `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="7bc1e-182">[특성 라우팅은](#ar) 사용자가 아닌 일부 작업에 필요한 ID를 설정 하는 세분화 된 제어를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="7bc1e-183">규칙에 따라 설명서에는 `id` 올바른 사용에 표시 될 수 있는 것과 같은 선택적 매개 변수가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="7bc1e-184">대부분의 앱은 URL을 읽을 수 있고 의미를 담고 있도록 기본적이고 서술적인 라우팅 체계를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="7bc1e-185">기본 기존 경로인 `{controller=Home}/{action=Index}/{id?}`는:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="7bc1e-186">기본적이고 서술적인 라우팅 체계를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="7bc1e-187">UI 기반 앱에 대한 유용한 시작점입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="7bc1e-188">는 많은 웹 UI 앱에 필요한 유일한 경로 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="7bc1e-189">규모가 큰 웹 UI 앱의 경우 [영역](#areas) 을 사용 하는 또 다른 경로를 사용 하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-189">For larger web UI apps, another route using [Areas](#areas) is frequently all that's needed.</span></span>

<span data-ttu-id="7bc1e-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> 및 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span><span class="sxs-lookup"><span data-stu-id="7bc1e-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span></span>

* <span data-ttu-id="7bc1e-191">호출 된 순서에 따라 해당 끝점에 **순서** 값을 자동으로 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="7bc1e-192">ASP.NET Core 3.0 이상의 엔드포인트 라우팅은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="7bc1e-193">경로 개념이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="7bc1e-194">는 확장성 실행을 위한 순서 보증을 제공 하지 않으며 모든 끝점이 한 번에 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="7bc1e-195">[로깅](xref:fundamentals/logging/index)을 사용하도록 설정하여 <xref:Microsoft.AspNetCore.Routing.Route>와 같은 기본 제공 라우팅 구현이 요청과 일치하는 방법을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="7bc1e-196">[특성 라우팅은](#ar) 이 문서의 뒷부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="7bc1e-197">다중 기본 경로</span><span class="sxs-lookup"><span data-stu-id="7bc1e-197">Multiple conventional routes</span></span>

<span data-ttu-id="7bc1e-198">[conventional routes](#cr) `UseEndpoints` 및에 대 한 호출을 추가 하 여 내에서 여러 개의 기존 경로를 추가할 수 있습니다 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>.</span></span> <span data-ttu-id="7bc1e-199">이렇게 하면 여러 가지 규칙을 정의 하거나 다음과 같이 특정 [작업](#action)에 전용으로 사용 되는 기본 경로를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="7bc1e-200">`blog`위의 코드에서 경로는 **전용 기본 경로** 입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="7bc1e-201">다음 이유 때문에 전용 기본 경로 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="7bc1e-202">[기존 라우팅을](#cr)사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="7bc1e-203">특정 [작업](#action)전용입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="7bc1e-204">`controller`및 `action` 는 경로 템플릿에 매개 변수로 표시 되지 않기 때문에 `"blog/{*article}"` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="7bc1e-205">기본값만 사용할 수 있습니다 `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="7bc1e-206">이 경로는 항상 작업에 매핑됩니다 `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="7bc1e-207">`/Blog`, `/Blog/Article` 및 `/Blog/{any-string}` 은 블로그 경로와 일치 하는 유일한 URL 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="7bc1e-208">위의 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-208">The preceding example:</span></span>

* <span data-ttu-id="7bc1e-209">`blog` 경로는 `default` 먼저 추가 되기 때문에 경로 보다 일치 하는 항목에 대 한 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="7bc1e-210">는 문서 이름을 URL [의 일부로](https://developer.mozilla.org/docs/Glossary/Slug) 포함 하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-210">Is an example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="7bc1e-211">ASP.NET Core 3.0 이상에서 라우팅은 다음과 같이 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="7bc1e-212">*경로* 라는 개념을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-212">Define a concept called a *route*.</span></span> <span data-ttu-id="7bc1e-213">`UseRouting`은 경로 일치를 미들웨어 파이프라인에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="7bc1e-214">`UseRouting`미들웨어는 앱에 정의 된 끝점 집합을 살펴보고 요청에 따라 가장 적합 한 끝점 일치를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="7bc1e-215">또는과 같은 확장성의 실행 순서에 대 한 보장을 제공 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="7bc1e-216">라우팅에 대 한 참조 자료에 대 한 [라우팅](xref:fundamentals/routing) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="7bc1e-217">기본 라우팅 순서</span><span class="sxs-lookup"><span data-stu-id="7bc1e-217">Conventional routing order</span></span>

<span data-ttu-id="7bc1e-218">기존 라우팅은 앱에서 정의 된 작업과 컨트롤러의 조합만 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="7bc1e-219">이는 기존 경로가 중복 되는 경우를 단순화 하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="7bc1e-220">, 및를 사용 하 여 경로를 추가 하면 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> 호출 되는 순서에 따라 해당 끝점에 순서 값이 자동으로 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="7bc1e-221">이전에 표시 된 경로의 일치 항목은 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="7bc1e-222">규칙 기반 라우팅은 순서에 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7bc1e-223">일반적으로 영역이 있는 경로는 영역이 없는 경로 보다 더 구체적 이므로 앞에 배치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="7bc1e-224">와 같은 모든 경로 매개 변수를 사용 하는 [전용 기본 경로](#dcr) 를 사용 하면 `{*article}` 경로를 너무 많이 사용할 수 있습니다. 즉, 다른 경로와 일치 시키려는 url과 일치 하 게 됩니다. [greedy](xref:fundamentals/routing#greedy)</span><span class="sxs-lookup"><span data-stu-id="7bc1e-224">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="7bc1e-225">Greedy 일치를 방지 하려면 greedy 경로를 경로 테이블에 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="7bc1e-226">모호한 작업 확인</span><span class="sxs-lookup"><span data-stu-id="7bc1e-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="7bc1e-227">두 끝점이 라우팅을 통해 일치 하는 경우 라우팅은 다음 중 하나를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="7bc1e-228">가장 적합 한 후보를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-228">Choose the best candidate.</span></span>
* <span data-ttu-id="7bc1e-229">예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-229">Throw an exception.</span></span>

<span data-ttu-id="7bc1e-230">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="7bc1e-231">위의 컨트롤러는 다음과 일치 하는 두 작업을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="7bc1e-232">URL 경로 `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="7bc1e-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="7bc1e-233">데이터 `{ controller = Products33, action = Edit, id = 17 }` 를 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="7bc1e-234">MVC 컨트롤러의 일반적인 패턴은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="7bc1e-235">`Edit(int)` 제품을 편집 하는 폼을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="7bc1e-236">`Edit(int, Product)` 게시 된 폼을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="7bc1e-237">올바른 경로를 확인 하려면:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-237">To resolve the correct route:</span></span>

* <span data-ttu-id="7bc1e-238">`Edit(int, Product)` 요청이 HTTP 인 경우이 선택 됩니다 `POST` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="7bc1e-239">`Edit(int)`[HTTP 동사가](#verb) 다른 항목이 면이 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="7bc1e-240">`Edit(int)` 는 일반적으로을 통해 호출 됩니다 `GET` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="7bc1e-241"><xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]` 요청의 HTTP 메서드를 기반으로 하 여 선택할 수 있도록 라우팅에는가 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="7bc1e-242">는 `HttpPostAttribute` `Edit(int, Product)` 보다 더 일치 하는 항목을 만듭니다 `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="7bc1e-243">와 같은 특성의 역할을 이해 하는 것이 중요 `HttpPostAttribute` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="7bc1e-244">다른 [HTTP 동사](#verb)에 대해 유사한 특성이 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="7bc1e-245">[기본 라우팅](#cr)에서 일반적으로 작업은 표시 양식, 제출 양식 워크플로의 일부인 경우에 동일한 동작 이름을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="7bc1e-246">예를 들어 [두 개의 편집 작업 메서드 검사](xref:tutorials/first-mvc-app/controller-methods-views#get-post)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="7bc1e-247">라우팅이 최적의 후보를 선택할 수 없는 경우 일치 하는 <xref:System.Reflection.AmbiguousMatchException> 여러 끝점을 나열 하는이 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="7bc1e-248">기본 경로 이름</span><span class="sxs-lookup"><span data-stu-id="7bc1e-248">Conventional route names</span></span>

<span data-ttu-id="7bc1e-249">`"blog"` `"default"` 다음 예제에서 및 문자열은 기존 경로 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="7bc1e-250">경로 이름은 경로에 논리적 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-250">The route names give the route a logical name.</span></span> <span data-ttu-id="7bc1e-251">명명 된 경로는 URL 생성에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="7bc1e-252">명명 된 경로를 사용 하면 경로 순서가 URL 생성을 복잡 하 게 만들 때 URL 만들기가 간단해 집니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="7bc1e-253">경로 이름은 고유한 응용 프로그램 전체에 해당 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="7bc1e-254">경로 이름:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-254">Route names:</span></span>

* <span data-ttu-id="7bc1e-255">URL 일치 또는 요청 처리에는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="7bc1e-256">URL 생성에만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-256">Are used only for URL generation.</span></span>

<span data-ttu-id="7bc1e-257">경로 이름 개념은 라우팅에서 [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="7bc1e-258">용어 **경로 이름** 및 **끝점 이름** :</span><span class="sxs-lookup"><span data-stu-id="7bc1e-258">The terms **route name** and **endpoint name** :</span></span>

* <span data-ttu-id="7bc1e-259">는 바꿔 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-259">Are interchangeable.</span></span>
* <span data-ttu-id="7bc1e-260">문서와 코드에 사용 되는 항목은 설명 된 API에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="7bc1e-261">REST Api에 대 한 특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="7bc1e-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="7bc1e-262">REST Api는 특성 라우팅을 사용 하 여 응용 프로그램의 기능을 작업을 [HTTP 동사로](#verb)나타내는 리소스 집합으로 모델링 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="7bc1e-263">특성 라우팅은 특성 모음을 사용하여 작업을 경로 템플릿에 직접 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="7bc1e-264">다음 `StartUp.Configure` 코드는 REST API에 일반적 이며 다음 샘플에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="7bc1e-265">위의 코드에서 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> 는 `UseEndpoints` 특성 라우트된 컨트롤러를 매핑하기 위해 내부에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="7bc1e-266">다음 예제에서,</span><span class="sxs-lookup"><span data-stu-id="7bc1e-266">In the following example:</span></span>

* <span data-ttu-id="7bc1e-267">위의 `Configure` 메서드가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="7bc1e-268">`HomeController` 기본 기본 경로와 유사한 Url 집합과 일치 `{controller=Home}/{action=Index}/{id?}` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="7bc1e-269">`HomeController.Index`작업은 URL 경로,, 또는에 대해 실행 `/` 됩니다 `/Home` `/Home/Index` `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="7bc1e-270">이 예에서는 특성 라우팅과 [기본 라우팅](#cr)간의 주요 프로그래밍 차이점을 강조 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="7bc1e-271">특성 라우팅은 경로를 지정 하는 데 더 많은 입력이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="7bc1e-272">기본 경로는 더 많은 간략하게 경로를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="7bc1e-273">그러나 특성 라우팅은 각 [작업](#action)에 적용 되는 경로 템플릿을 정확 하 게 제어 하도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="7bc1e-274">특성 라우팅을 사용 하는 경우에는 [토큰 대체가](#routing-token-replacement-templates-ref-label) 사용 되지 않는 한 컨트롤러 및 작업 이름이 동작이 일치 하는 부분을 재생 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-274">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="7bc1e-275">다음 예제에서는 이전 예제와 동일한 Url을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="7bc1e-276">다음 코드에서는 및에 대 한 토큰 대체를 사용 합니다 `action` `controller` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="7bc1e-277">다음 코드는 `[Route("[controller]/[action]")]` 컨트롤러에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="7bc1e-278">위의 코드에서 `Index` 메서드 템플릿은 `/` 경로 템플릿에 또는 앞에와 야 합니다 `~/` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="7bc1e-279">작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="7bc1e-280">경로 템플릿 선택에 대 한 자세한 내용은 [경로 템플릿 우선 순위](xref:fundamentals/routing#rtp) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="7bc1e-281">예약된 라우팅 이름</span><span class="sxs-lookup"><span data-stu-id="7bc1e-281">Reserved routing names</span></span>

<span data-ttu-id="7bc1e-282">다음 키워드는 컨트롤러 또는 페이지를 사용할 때 예약 된 경로 매개 변수 이름입니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="7bc1e-283">`page`특성 라우팅이 있는 경로 매개 변수로를 사용 하는 것은 일반적인 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="7bc1e-284">이렇게 하면 URL 생성 시 일관 되지 않은 동작이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="7bc1e-285">특수 매개 변수 이름은 url 생성 작업에서 페이지 또는 컨트롤러를 참조 하는지 확인 하는 데 사용 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="7bc1e-286">HTTP 동사 템플릿</span><span class="sxs-lookup"><span data-stu-id="7bc1e-286">HTTP verb templates</span></span>

<span data-ttu-id="7bc1e-287">ASP.NET Core에는 다음과 같은 HTTP 동사 템플릿이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="7bc1e-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="7bc1e-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="7bc1e-289">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="7bc1e-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="7bc1e-290">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="7bc1e-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="7bc1e-291">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="7bc1e-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="7bc1e-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="7bc1e-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="7bc1e-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="7bc1e-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="7bc1e-294">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="7bc1e-294">Route templates</span></span>

<span data-ttu-id="7bc1e-295">ASP.NET Core에는 다음과 같은 경로 템플릿이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="7bc1e-296">모든 [HTTP 동사 템플릿은](#verb) 경로 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="7bc1e-297">[경로](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="7bc1e-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="7bc1e-298">Http 동사 특성을 사용 하는 특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="7bc1e-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="7bc1e-299">다음 컨트롤러를 살펴보십시오.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="7bc1e-300">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="7bc1e-300">In the preceding code:</span></span>

* <span data-ttu-id="7bc1e-301">각 작업에는 `[HttpGet]` HTTP GET 요청에 대해서만 일치를 제한 하는 특성이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="7bc1e-302">`GetProduct`작업은 템플릿을 포함 `"{id}"` 하므로 `id` `"api/[controller]"` 컨트롤러의 템플릿에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="7bc1e-303">메서드 템플릿은 `"api/[controller]/"{id}""` 입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="7bc1e-304">따라서이 작업은,, 등의 폼에 대 한 GET 요청만 일치 `/api/test2/xyz` `/api/test2/123` `/api/test2/{any string}` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-304">Therefore this action only matches GET requests for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="7bc1e-305">작업에는 `GetIntProduct` 템플릿이 포함 되어 있습니다 `"int/{id:int}")` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="7bc1e-306">`:int`템플릿의 부분은 `id` 정수로 변환할 수 있는 문자열에 대 한 경로 값을 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="7bc1e-307">GET 요청 대상 `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="7bc1e-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="7bc1e-308">이 작업과 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="7bc1e-309">404를 [찾을 수](https://developer.mozilla.org/docs/Web/HTTP/Status/404) 없음 오류를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="7bc1e-310">`GetInt2Product`작업은 `{id}` 템플릿에 포함 되지만 `id` 정수로 변환할 수 있는 값으로 제한 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="7bc1e-311">GET 요청 대상 `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="7bc1e-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="7bc1e-312">이 경로와 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-312">Matches this route.</span></span>
  * <span data-ttu-id="7bc1e-313">모델 바인딩이 정수로 변환 되지 않습니다 `abc` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="7bc1e-314">`id`메서드의 매개 변수는 정수입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="7bc1e-315">모델 바인딩이 정수로 변환 하지 못해 [400 잘못 된 요청](https://developer.mozilla.org/docs/Web/HTTP/Status/400) 을 반환 합니다 `abc` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="7bc1e-316">특성 라우팅은 <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> , 및와 같은 특성을 사용할 수 있습니다 <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="7bc1e-317">모든 [HTTP 동사](#verb) 특성은 경로 템플릿을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="7bc1e-318">다음 예에서는 동일한 경로 템플릿과 일치 하는 두 가지 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="7bc1e-319">URL 경로 사용 `/products3` :</span><span class="sxs-lookup"><span data-stu-id="7bc1e-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="7bc1e-320">`MyProductsController.ListProducts` [HTTP 동사가](#verb) 일 때 동작이 실행 됩니다 `GET` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="7bc1e-321">`MyProductsController.CreateProduct` [HTTP 동사가](#verb) 일 때 동작이 실행 됩니다 `POST` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="7bc1e-322">REST API를 빌드할 때 `[Route(...)]` 작업에서 모든 HTTP 메서드를 허용 하므로 작업 메서드에서를 사용 해야 하는 경우는 드뭅니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="7bc1e-323">API에서 지 원하는 항목을 정확 하 게 이해 하려면 보다 구체적인 [HTTP 동사 특성](#verb) 을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="7bc1e-324">REST API의 클라이언트는 특정 논리 작업에 어떤 경로 및 HTTP 동사가 매핑되는지 알아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="7bc1e-325">REST Api는 특성 라우팅을 사용 하 여 응용 프로그램의 기능을 작업을 HTTP 동사로 나타내는 리소스 집합으로 모델링 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="7bc1e-326">즉, 동일한 논리 리소스에 대 한 GET 및 POST와 같은 많은 작업은 동일한 URL을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="7bc1e-327">특성 라우팅은 API의 공개 엔드포인트 레이아웃을 신중하게 설계하는 데 필요한 제어 수준을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="7bc1e-328">특성 경로는 특정 작업에 적용되므로 경로 템플릿 정의의 일환으로 필요한 매개 변수를 간단하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="7bc1e-329">다음 예제에서 `id` 는 URL 경로의 일부로 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="7bc1e-330">`Products2ApiController.GetProduct(int)`작업은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="7bc1e-331">다음과 같이 URL 경로를 사용 하 여 실행 됩니다. `/products2/3`</span><span class="sxs-lookup"><span data-stu-id="7bc1e-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="7bc1e-332">URL 경로를 사용 하 여 실행 되지 않습니다 `/products2` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="7bc1e-333">[[사용]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 특성을 사용하면 작업에서 지원되는 요청 콘텐츠 형식을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="7bc1e-334">자세한 내용은 [소비 특성을 사용 하 여 지원 되는 요청 콘텐츠 형식 정의](xref:web-api/index#consumes)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="7bc1e-335">경로 템플릿 및 관련 옵션에 대한 전체 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="7bc1e-336">에 대 한 자세한 내용은 `[ApiController]` [ApiController 특성](xref:web-api/index##apicontroller-attribute)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="7bc1e-337">경로 이름</span><span class="sxs-lookup"><span data-stu-id="7bc1e-337">Route name</span></span>

<span data-ttu-id="7bc1e-338">다음 코드는 의 `Products_List`경로 이름을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="7bc1e-339">경로 이름을 사용하여 특정 경로 기반의 URL을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="7bc1e-340">경로 이름:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-340">Route names:</span></span>

* <span data-ttu-id="7bc1e-341">라우팅의 URL 일치 동작에는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="7bc1e-342">URL 생성에만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-342">Are only used for URL generation.</span></span>

<span data-ttu-id="7bc1e-343">경로 이름은 애플리케이션 전체에서 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="7bc1e-344">앞의 코드와 `id` 매개 변수를 선택적 ()으로 정의 하는 기존의 기본 경로를 대조 합니다 `{id?}` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="7bc1e-345">Api를 정확 하 게 지정 하는 기능에는 `/products` 및 `/products/5` 를 다른 작업에 디스패치할 수 있는 것과 같은 장점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="7bc1e-346">특성 경로 조합</span><span class="sxs-lookup"><span data-stu-id="7bc1e-346">Combining attribute routes</span></span>

<span data-ttu-id="7bc1e-347">특성 라우팅의 반복 횟수를 줄이기 위해 컨트롤러의 경로 특성은 개별 작업의 경로 특성과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="7bc1e-348">컨트롤러에 정의된 경로 템플릿은 작업의 경로 템플릿에 앞에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="7bc1e-349">경로 특성을 컨트롤러에 배치하면 컨트롤러의 **모든** 작업이 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="7bc1e-350">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-350">In the preceding example:</span></span>

* <span data-ttu-id="7bc1e-351">URL 경로가 `/products` 일치 하는 경우 `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="7bc1e-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="7bc1e-352">URL 경로는 `/products/5` 와 일치할 수 있습니다 `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="7bc1e-353">이러한 작업은 모두 `GET` 특성으로 표시 되어 있으므로 HTTP만 일치 `[HttpGet]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="7bc1e-354">작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="7bc1e-355">다음 예에서는 기본 경로와 유사한 URL 경로 집합을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="7bc1e-356">다음 표에서는 `[Route]` 위의 코드에 있는 특성에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="7bc1e-357">attribute</span><span class="sxs-lookup"><span data-stu-id="7bc1e-357">Attribute</span></span>               | <span data-ttu-id="7bc1e-358">와 결합 `[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="7bc1e-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="7bc1e-359">경로 템플릿을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="7bc1e-360">예</span><span class="sxs-lookup"><span data-stu-id="7bc1e-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="7bc1e-361">예</span><span class="sxs-lookup"><span data-stu-id="7bc1e-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="7bc1e-362">**아니요**</span><span class="sxs-lookup"><span data-stu-id="7bc1e-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="7bc1e-363">예</span><span class="sxs-lookup"><span data-stu-id="7bc1e-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="7bc1e-364">특성 경로 순서</span><span class="sxs-lookup"><span data-stu-id="7bc1e-364">Attribute route order</span></span>

<span data-ttu-id="7bc1e-365">라우팅은 트리를 빌드하고 모든 끝점을 동시에 일치 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="7bc1e-366">경로 항목은 이상적인 순서로 배치 된 것 처럼 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="7bc1e-367">가장 구체적인 경로는 보다 일반적인 경로 보다 먼저 실행 될 가능성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="7bc1e-368">예를 들어와 같은 특성 경로는 `blog/search/{topic}` 와 같은 특성 경로 보다 구체적입니다 `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="7bc1e-369">`blog/search/{topic}`경로는 보다 구체적 이므로 기본적으로 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="7bc1e-370">개발자는 [기본 라우팅을](#cr)사용 하 여 원하는 순서로 경로를 배치할 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="7bc1e-371">특성 경로는 속성을 사용 하 여 주문을 구성할 수 있습니다 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="7bc1e-372">제공 된 모든 프레임 워크 [경로 특성](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 에는가 포함 `Order` 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="7bc1e-373">경로는 `Order` 속성의 오름차순 정렬에 따라 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="7bc1e-374">기본 순서는 `0`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-374">The default order is `0`.</span></span> <span data-ttu-id="7bc1e-375">를 사용 하 여 경로를 설정 하 `Order = -1` 는 것은 순서를 설정 하지 않은 경로 보다 먼저 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="7bc1e-376">기본 경로 순서 지정 후를 사용 하 여 경로를 설정 `Order = 1` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="7bc1e-377">**Avoid** 에 따라 방지 `Order` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="7bc1e-378">응용 프로그램의 URL 공간에 올바른 경로를 위해 명시적인 순서 값이 필요한 경우에는 클라이언트와 혼동 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="7bc1e-379">일반적으로 특성 라우팅은 URL이 일치 하는 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="7bc1e-380">URL 생성에 사용 되는 기본 순서가 작동 하지 않는 경우 경로 이름을 재정의로 사용 하는 것은 일반적으로 속성을 적용 하는 것 보다 간단 합니다 `Order` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="7bc1e-381">경로 일치를 정의 하는 다음 두 개의 컨트롤러를 고려 하십시오 `/home` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="7bc1e-382">위의 코드를 요청 하면 `/home` 다음과 유사한 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="7bc1e-383">`Order`경로 특성 중 하나에를 추가 하면 모호성을 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="7bc1e-384">위의 코드를 사용 하 여 `/home` 끝점을 실행 `HomeController.Index` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="7bc1e-385">에 가져오려면를 `MyDemoController.MyIndex` 요청 `/home/MyIndex` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="7bc1e-386">**고** :</span><span class="sxs-lookup"><span data-stu-id="7bc1e-386">**Note** :</span></span>

* <span data-ttu-id="7bc1e-387">위의 코드는 예 이거나 낮은 라우팅 디자인입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="7bc1e-388">속성을 설명 하는 데 사용 되었습니다 `Order` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="7bc1e-389">`Order`속성은 모호성을 해결 하 고 해당 템플릿은 일치 시킬 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="7bc1e-390">템플릿을 제거 하는 것이 좋습니다 `[Route("Home")]` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="7bc1e-391">페이지 [ Razor 경로 및 앱 규칙:](xref:razor-pages/razor-pages-conventions#route-order) 경로 순서와 페이지의 경로 순서에 대 한 정보를 참조 하세요 Razor .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="7bc1e-392">일부 경우에는 모호한 경로를 사용 하 여 HTTP 500 오류가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="7bc1e-393">[로깅을](xref:fundamentals/logging/index) 사용 하 여를 일으킨 끝점을 확인 `AmbiguousMatchException` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="7bc1e-394">경로 템플릿에서 토큰 바꾸기 [컨트롤러], [작업], [영역]</span><span class="sxs-lookup"><span data-stu-id="7bc1e-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="7bc1e-395">편의를 위해 특성 경로는 토큰을 다음 중 하나로 묶어 예약 된 경로 매개 변수에 대 한 토큰 바꾸기를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="7bc1e-396">대괄호: `[]`</span><span class="sxs-lookup"><span data-stu-id="7bc1e-396">Square brackets: `[]`</span></span>
* <span data-ttu-id="7bc1e-397">중괄호: `{}`</span><span class="sxs-lookup"><span data-stu-id="7bc1e-397">Curly braces: `{}`</span></span>

<span data-ttu-id="7bc1e-398">`[action]`, 및 토큰은 `[area]` `[controller]` 경로가 정의 된 작업에서 동작 이름, 영역 이름 및 컨트롤러 이름 값으로 대체 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="7bc1e-399">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="7bc1e-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="7bc1e-400">항목 `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="7bc1e-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="7bc1e-401">항목 `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="7bc1e-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="7bc1e-402">토큰 교체는 특성 경로 빌드 과정의 마지막 단계로 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="7bc1e-403">앞의 예제는 다음 코드와 동일 하 게 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="7bc1e-404">특성 경로를 상속과 결합할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="7bc1e-405">토큰 교체와 강력 하 게 결합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="7bc1e-406">토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="7bc1e-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`각 작업에 대해 고유한 경로 이름을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="7bc1e-408">토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="7bc1e-409">는 각 작업의 고유한 경로 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="7bc1e-410">리터럴 토큰 교체 구분 기호 `[` 또는 `]`와 매칭하려면 문자(`[[` 또는 `]]`)를 반복하여 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="7bc1e-411">매개 변수 변환기를 사용하여 토큰 교체 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="7bc1e-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="7bc1e-412">매개 변수 변환기를 사용하여 토큰 교체를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="7bc1e-413">매개 변수 변환기는 <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer>를 구현하며 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="7bc1e-414">예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을로 변경 합니다 `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="7bc1e-415"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention>은 다음과 같은 응용 프로그램 모델 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="7bc1e-416">애플리케이션의 모든 특성 경로에 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="7bc1e-417">대체되는 특성 경로 토큰 값을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="7bc1e-418">이전 `ListAll` 메서드는와 일치 `/subscription-management/list-all` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="7bc1e-419">`RouteTokenTransformerConvention`은 `ConfigureServices`에 옵션으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="7bc1e-420">슬러그의 정의는 [슬러그의 MDN 웹 문서](https://developer.mozilla.org/docs/Glossary/Slug) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="7bc1e-421">여러 특성 경로</span><span class="sxs-lookup"><span data-stu-id="7bc1e-421">Multiple attribute routes</span></span>

<span data-ttu-id="7bc1e-422">특성 경로는 동일한 작업에 도달하는 여러 경로를 정의하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="7bc1e-423">가장 일반적인 사용 방법은 다음 예제와 같이 기본 규칙 기반 경로의 동작을 모방하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="7bc1e-424">여러 경로 특성을 컨트롤러에 배치 하면 각 경로 특성이 동작 메서드의 각 경로 특성과 결합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="7bc1e-425">모든 [HTTP 동사](#verb) 경로 제약 조건은를 구현 `IActionConstraint` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="7bc1e-426">을 구현 하는 여러 경로 특성이 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 동작에 배치 되는 경우:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="7bc1e-427">각 작업 제약 조건은 컨트롤러에 적용 된 경로 템플릿과 결합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="7bc1e-428">작업에 여러 경로를 사용 하면 유용 하 고 강력 하 게 보일 수 있습니다. 앱의 URL 공간을 기본 및 잘 정의 된 상태로 유지 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="7bc1e-429">기존 클라이언트를 지 원하는 경우와 같이 필요한 경우에 **만** 작업에 대해 여러 경로를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="7bc1e-430">특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정</span><span class="sxs-lookup"><span data-stu-id="7bc1e-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="7bc1e-431">특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정할 수 있는 규칙 기반 경로와 동일한 인라인 구문을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="7bc1e-432">위의 코드에서는 `[HttpPost("product/{id:int}")]` 경로 제약 조건을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="7bc1e-433">`ProductsController.ShowProduct`작업은와 같은 URL 경로만 일치 시킵니다 `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="7bc1e-434">경로 템플릿 부분은 `{id:int}` 해당 세그먼트를 정수로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="7bc1e-435">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="7bc1e-436">IRouteTemplateProvider를 사용 하는 사용자 지정 경로 특성</span><span class="sxs-lookup"><span data-stu-id="7bc1e-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="7bc1e-437">모든 [경로 특성](#rt) 은를 구현 <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="7bc1e-438">ASP.NET Core 런타임:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="7bc1e-439">응용 프로그램이 시작 될 때 컨트롤러 클래스 및 작업 메서드에서 특성을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="7bc1e-440">는를 구현 하는 특성을 사용 하 여 `IRouteTemplateProvider` 초기 경로 집합을 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="7bc1e-441">`IRouteTemplateProvider`을 구현 하 여 사용자 지정 경로 특성을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="7bc1e-442">각각의 `IRouteTemplateProvider`를 사용하여 사용자 지정 경로 템플릿, 순서 및 이름으로 단일 경로를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="7bc1e-443">이전 메서드는를 `Get` 반환 합니다 `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="7bc1e-444">응용 프로그램 모델을 사용 하 여 특성 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="7bc1e-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="7bc1e-445">응용 프로그램 모델:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-445">The application model:</span></span>

* <span data-ttu-id="7bc1e-446">는 시작할 때 생성 되는 개체 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="7bc1e-447">앱에서 작업을 라우팅하고 실행 하기 위해 ASP.NET Core에서 사용 하는 모든 메타 데이터를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="7bc1e-448">응용 프로그램 모델은 경로 특성에서 수집 된 모든 데이터를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="7bc1e-449">경로 특성의 데이터는 구현에 의해 제공 됩니다 `IRouteTemplateProvider` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="7bc1e-450">규약</span><span class="sxs-lookup"><span data-stu-id="7bc1e-450">Conventions:</span></span>

* <span data-ttu-id="7bc1e-451">응용 프로그램 모델을 수정 하 여 라우팅이 동작 하는 방식을 사용자 지정 하도록를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="7bc1e-452">앱 시작 시 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-452">Are read at app startup.</span></span>

<span data-ttu-id="7bc1e-453">이 섹션에서는 응용 프로그램 모델을 사용 하 여 라우팅을 사용자 지정 하는 기본적인 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="7bc1e-454">다음 코드는 프로젝트의 폴더 구조를 사용 하 여 경로를 대략적으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="7bc1e-455">다음 코드는 `namespace` 특성이 라우팅되는 컨트롤러에 규칙이 적용 되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="7bc1e-456">예를 들어 다음 컨트롤러는를 사용 하지 않습니다 `NamespaceRoutingConvention` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7bc1e-457">`NamespaceRoutingConvention.Apply` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="7bc1e-458">컨트롤러에서 특성을 라우팅하는 경우 아무 작업도 수행 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="7bc1e-459">을 기반으로 하는 컨트롤러 템플릿을 설정 합니다 `namespace` . 이때 기본가 `namespace` 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="7bc1e-460">는 `NamespaceRoutingConvention` 다음에 적용할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="7bc1e-461">예를 들어 다음 컨트롤러를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="7bc1e-462">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="7bc1e-462">In the preceding code:</span></span>

* <span data-ttu-id="7bc1e-463">밑 `namespace` 이 인 `My.Application` 경우</span><span class="sxs-lookup"><span data-stu-id="7bc1e-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="7bc1e-464">이전 컨트롤러의 전체 이름은 `My.Application.Admin.Controllers.UsersController` 입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="7bc1e-465">는 `NamespaceRoutingConvention` 컨트롤러 템플릿을로 설정 합니다 `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="7bc1e-466">를 `NamespaceRoutingConvention` 컨트롤러의 특성으로 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="7bc1e-467">혼합 라우팅: 특성 라우팅 및 규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="7bc1e-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="7bc1e-468">ASP.NET Core apps는 기존 라우팅 및 특성 라우팅의 사용을 혼합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="7bc1e-469">일반적으로 브라우저의 HTML 페이지를 처리하는 컨트롤러에는 규칙 기반 경로를 사용하고 REST API를 제공하는 컨트롤러에는 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="7bc1e-470">작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7bc1e-471">컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7bc1e-472">특성 경로를 정의하는 동작은 규칙 기반 경로를 통해 도달할 수 없으며 그 반대도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="7bc1e-473">컨트롤러의 **모든** 경로 특성은 컨트롤러 특성의 모든 작업 **을 라우팅합니다.**</span><span class="sxs-lookup"><span data-stu-id="7bc1e-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="7bc1e-474">특성 라우팅 및 기존 라우팅은 동일한 라우팅 엔진을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="7bc1e-475">URL 생성 및 앰비언트 값</span><span class="sxs-lookup"><span data-stu-id="7bc1e-475">URL Generation and ambient values</span></span>

<span data-ttu-id="7bc1e-476">앱은 라우팅 URL 생성 기능을 사용 하 여 작업에 대 한 URL 링크를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="7bc1e-477">Url을 생성 하면 하드 코딩 되는 Url이 제거 되어 코드가 더 강력 하 고 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="7bc1e-478">이 섹션에서는 MVC에서 제공 하는 URL 생성 기능에 초점을 맞춘 후 URL 생성이 작동 하는 방식에 대 한 기본 내용만 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="7bc1e-479">URL 생성에 대한 자세한 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="7bc1e-480"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>인터페이스는 MVC와 URL 생성을 위한 라우팅 간의 기본 인프라 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="7bc1e-481">인스턴스는 `IUrlHelper` `Url` 컨트롤러, 뷰 및 뷰 구성 요소의 속성을 통해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="7bc1e-482">다음 예제에서 `IUrlHelper` 인터페이스는 속성을 통해 `Controller.Url` 다른 동작에 대 한 URL을 생성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="7bc1e-483">앱에서 기본 기본 경로를 사용 하는 경우 변수의 값은 `url` URL 경로 문자열입니다 `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="7bc1e-484">이 URL 경로는 다음을 결합 하 여 라우팅하는 방식으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="7bc1e-485">**앰비언트 값** 이라고 하는 현재 요청의 경로 값입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="7bc1e-486">에 전달 되는 값으로 `Url.Action` , 해당 값을 경로 템플릿으로 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="7bc1e-487">경로 템플릿에 있는 각 경로 매개 변수의 값은 이름을 값 및 앰비언트 값과 매칭한 값으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="7bc1e-488">값이 없는 경로 매개 변수는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="7bc1e-489">기본값이 있는 경우 기본값을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="7bc1e-490">선택 사항인 경우 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-490">Be skipped if it's optional.</span></span> <span data-ttu-id="7bc1e-491">예를 들어 `id` 경로 템플릿의 `{controller}/{action}/{id?}` 입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="7bc1e-492">필요한 경로 매개 변수에 해당 값이 없는 경우 URL 생성이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="7bc1e-493">경로에 대한 URL 생성이 실패하면 모든 경로를 시도하거나 일치 항목을 찾을 때까지 그 다음 경로를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="7bc1e-494">위의 예에서는 `Url.Action` [기존 라우팅을](#cr)가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="7bc1e-495">개념은 서로 다르지만 URL 생성은 [특성 라우팅과](#ar)유사 하 게 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="7bc1e-496">기본 라우팅을 사용 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-496">With conventional routing:</span></span>

* <span data-ttu-id="7bc1e-497">경로 값은 템플릿을 확장 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="7bc1e-498">및에 대 한 경로 값은 `controller` `action` 일반적으로 해당 템플릿에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="7bc1e-499">이는 라우팅을 통해 일치 하는 Url이 규칙을 준수 하기 때문에 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="7bc1e-500">다음 예에서는 특성 라우팅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="7bc1e-501">`Source`위의 코드에서 작업은을 생성 `custom/url/to/destination` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="7bc1e-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> 는의 대 안으로 ASP.NET Core 3.0에 추가 되었습니다 `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="7bc1e-503">`LinkGenerator` 는 유사 하지만 유연한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="7bc1e-504">의 각 메서드에 `IUrlHelper` 는의 해당 메서드 패밀리가 있습니다 `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="7bc1e-505">작업 이름으로 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7bc1e-505">Generating URLs by action name</span></span>

<span data-ttu-id="7bc1e-506">[Url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [Linkgenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)및 모든 관련 된 오버 로드는 컨트롤러 이름과 작업 이름을 지정 하 여 대상 끝점을 생성 하도록 디자인 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="7bc1e-507">를 사용 하는 경우 `Url.Action` 및에 대 한 현재 경로 값은 `controller` `action` 런타임에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="7bc1e-508">및의 값 `controller` 은 `action` [앰비언트 값](#ambient) 과 값의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="7bc1e-509">메서드는 `Url.Action` 항상 및의 현재 값을 사용 하 `action` `controller` 고 현재 작업으로 라우팅되는 URL 경로를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="7bc1e-510">라우팅은 앰비언트 값의 값을 사용 하 여 URL을 생성할 때 제공 되지 않은 정보를 채우도록 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="7bc1e-511">앰비언트 값과 같은 경로를 고려 합니다 `{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="7bc1e-512">라우팅에는 추가 값 없이 URL을 생성 하는 데 충분 한 정보가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="7bc1e-513">모든 경로 매개 변수에는 값이 있으므로 라우팅에 충분 한 정보가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="7bc1e-514">값 `{ d = Donovan }` 이 추가 된 경우:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="7bc1e-515">값은 `{ d = David }` 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="7bc1e-516">생성 된 URL 경로는 `Alice/Bob/Carol/Donovan` 입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="7bc1e-517">**경고** : URL 경로는 계층 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-517">**Warning** : URL paths are hierarchical.</span></span> <span data-ttu-id="7bc1e-518">앞의 예제에서 다음과 같이 값을 `{ c = Cheryl }` 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="7bc1e-519">두 값은 모두 `{ c = Carol, d = David }` 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="7bc1e-520">에 대 한 값이 더 이상 없으며 `d` URL 생성이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="7bc1e-521">`c`URL을 생성 하려면 및의 원하는 값을 `d` 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="7bc1e-522">기본 경로를 사용 하 여이 문제가 발생할 수 있습니다 `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="7bc1e-523">`Url.Action`항상 명시적으로 및 값을 지정 하기 때문에이 문제는 드물게 발생 `controller` `action` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="7bc1e-524">Url의 여러 오버 로드. Action은 경로 값 개체를 사용 하 여 및 이외의 경로 매개 변수에 대 한 값을 제공 [합니다.](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) `controller` `action`</span><span class="sxs-lookup"><span data-stu-id="7bc1e-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="7bc1e-525">경로 값 개체는와 함께 자주 사용 됩니다 `id` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="7bc1e-526">예: `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="7bc1e-527">경로 값 개체:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-527">The route values object:</span></span>

* <span data-ttu-id="7bc1e-528">규칙에 따라 일반적으로 무명 형식의 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="7bc1e-529">`IDictionary<>`또는 [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="7bc1e-530">경로 매개 변수와 일치하지 않는 추가 경로 값들은 쿼리 문자열에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="7bc1e-531">위의 코드는을 생성 `/Products/Buy/17?color=red` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="7bc1e-532">다음 코드에서는 절대 URL을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="7bc1e-533">절대 URL을 만들려면 다음 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="7bc1e-534">을 허용 하는 오버 로드 `protocol` 입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="7bc1e-535">예를 들어 앞의 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-535">For example, the preceding code.</span></span>
* <span data-ttu-id="7bc1e-536">[Linkgenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)은 기본적으로 절대 uri를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="7bc1e-537">경로로 Url 생성</span><span class="sxs-lookup"><span data-stu-id="7bc1e-537">Generate URLs by route</span></span>

<span data-ttu-id="7bc1e-538">위의 코드는 컨트롤러 및 작업 이름을 전달 하 여 URL을 생성 하는 방법을 보여 주었습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="7bc1e-539">`IUrlHelper` 또한 [RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) 메서드 패밀리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="7bc1e-540">이러한 메서드는 [Url](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)과 유사 하지만 및의 현재 값을 `action` 경로 값에 복사 하지 않습니다 `controller` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="7bc1e-541">의 가장 일반적인 사용법은 `Url.RouteUrl` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="7bc1e-542">URL을 생성 하는 경로 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="7bc1e-543">일반적으로 컨트롤러 또는 작업 이름을 지정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="7bc1e-544">다음 Razor 파일은에 대 한 HTML 링크를 생성 합니다 `Destination_Route` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a><span data-ttu-id="7bc1e-545">HTML로 Url 생성 Razor</span><span class="sxs-lookup"><span data-stu-id="7bc1e-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="7bc1e-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper>및 요소를 각각 생성 하는 [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) [html.beginform](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) 및 html.actionlink 메서드를 제공 합니다 `<form>` `<a>` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="7bc1e-547">이러한 메서드는 url을 생성 하는 데 [url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) 메서드를 사용 하 여 비슷한 인수를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="7bc1e-548">`HtmlHelper`에 대한 `Url.RouteUrl` 보조 도구는 `Html.BeginRouteForm` 및 `Html.RouteLink`이며 서로 기능이 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="7bc1e-549">TagHelper는 `form` TagHelper 및 `<a>` TagHelper를 통해 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="7bc1e-550">둘 다 구현에 `IUrlHelper`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="7bc1e-551">자세한 내용은 [양식의 태그 도우미](xref:mvc/views/working-with-forms) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="7bc1e-552">보기 내에서 `IUrlHelper`는 `Url` 속성을 통해서 위에서 다루지 않은 임시 URL을 생성하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="7bc1e-553">작업 결과의 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7bc1e-553">URL generation in Action Results</span></span>

<span data-ttu-id="7bc1e-554">위의 예제는 컨트롤러에서를 사용 하는 방법을 보여 주었습니다 `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="7bc1e-555">컨트롤러에서 가장 일반적으로 사용 되는 작업은 URL을 작업 결과의 일부로 생성 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="7bc1e-556"><xref:Microsoft.AspNetCore.Mvc.ControllerBase> 및 <xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스는 다른 작업을 참조하는 작업 결과에 대한 편의 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="7bc1e-557">일반적인 사용법은 사용자 입력을 수락한 후 리디렉션하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="7bc1e-558">및와 같은 작업 결과 팩터리 메서드 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> 는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> 의 메서드와 유사한 패턴을 따릅니다 `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="7bc1e-559">전용 규칙 기반 경로의 특별한 사례</span><span class="sxs-lookup"><span data-stu-id="7bc1e-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="7bc1e-560">[기존 라우팅은](#cr) [전용 기본 경로](#dcr)라고 하는 특별 한 종류의 경로 정의를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="7bc1e-561">다음 예제에서 라는 경로는 `blog` 전용 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="7bc1e-562">위의 경로 정의를 사용 하 여 `Url.Action("Index", "Home")` 경로를 사용 하 여 URL 경로를 생성 `/` `default` 하지만 이유는 무엇입니까?</span><span class="sxs-lookup"><span data-stu-id="7bc1e-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="7bc1e-563">`{ controller = Home, action = Index }` 경로 값이면 `blog`를 사용하여 URL을 생성하기에 충분하며, 그 결과는 `/blog?action=Index&controller=Home`가 될 것이라고 추측할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="7bc1e-564">[전용 기본 경로](#dcr) 는 URL 생성을 사용 하 여 경로가 너무 [non-greedy](xref:fundamentals/routing#greedy) 하는 것을 방지 하는 해당 경로 매개 변수가 없는 기본 값의 특수 한 동작을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="7bc1e-565">이 예제에서 기본값은 `{ controller = Blog, action = Article }`이고, `controller` 및 `action`이 경로 매개 변수로 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="7bc1e-566">라우팅이 URL 생성을 수행할 때 입력한 값이 기본값과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="7bc1e-567">`blog`값이 일치 하지 않으므로를 사용한 URL 생성이 실패 `{ controller = Home, action = Index }` `{ controller = Blog, action = Article }` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="7bc1e-568">그 후 라우팅이 `default`로 대체되고, 이것은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="7bc1e-569">Areas</span><span class="sxs-lookup"><span data-stu-id="7bc1e-569">Areas</span></span>

<span data-ttu-id="7bc1e-570">[영역은](xref:mvc/controllers/areas) 별도의 그룹으로 관련 기능을 구성 하는 데 사용 되는 MVC 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="7bc1e-571">컨트롤러 작업에 대 한 라우팅 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="7bc1e-572">보기에 대 한 폴더 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-572">Folder structure for views.</span></span>

<span data-ttu-id="7bc1e-573">영역을 사용 하면 서로 다른 영역이 있는 한 앱에 동일한 이름의 여러 컨트롤러가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="7bc1e-574">영역을 사용하면 또 다른 경로 매개 변수 `area`를 `controller` 및 `action`에 추가하여 라우팅을 위한 계층 구조를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="7bc1e-575">이 섹션에서는 라우팅이 영역과 상호 작용 하는 방법에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="7bc1e-576">영역을 뷰에서 사용 하는 방법에 대 한 자세한 내용은 [영역](xref:mvc/controllers/areas) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="7bc1e-577">다음 예에서는 기본 기본 경로와 명명 된의 경로를 사용 하도록 MVC를 구성 합니다 `area` `area` `Blog` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="7bc1e-578">위의 코드에서를 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> 만들기 위해가 호출 됩니다 `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="7bc1e-579">두 번째 매개 변수인는 `"Blog"` 영역 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="7bc1e-580">URL 경로와 일치 하는 경우 경로 `/Manage/Users/AddUser` 는 `"blog_route"` 경로 값을 생성 합니다 `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="7bc1e-581">`area`경로 값은에 대 한 기본값으로 생성 됩니다 `area` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="7bc1e-582">에서 만든 경로는 `MapAreaControllerRoute` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="7bc1e-583">`MapAreaControllerRoute`는 제공된 영역 이름(여기에서는 `Blog`)을 사용하는 `area`에 대해 기본값 및 제약 조건을 모두 사용하여 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="7bc1e-584">기본값은 경로가 항상 `{ area = Blog, ... }`를 생성함을 보장하고, 제약 조건은 URL 생성을 위해 `{ area = Blog, ... }` 값을 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="7bc1e-585">규칙 기반 라우팅은 순서에 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7bc1e-586">일반적으로 영역이 있는 경로는 영역이 없는 경로 보다 더 구체적 이므로 앞에 배치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="7bc1e-587">위의 예제를 사용 하면 경로 값이 `{ area = Blog, controller = Users, action = AddUser }` 다음 동작과 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="7bc1e-588">[[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 특성은 컨트롤러를 영역의 일부로 나타내는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="7bc1e-589">이 컨트롤러는 영역에 `Blog` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="7bc1e-590">특성이 없는 컨트롤러 `[Area]` 는 어떤 영역의 구성원도 아니므로 **not** `area` 경로 값이 라우팅에서 제공 되는 경우 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="7bc1e-591">다음 예제에서는 나열된 첫 번째 컨트롤러만 `{ area = Blog, controller = Users, action = AddUser }` 경로 값과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="7bc1e-592">각 컨트롤러의 네임 스페이스는 완전성을 위해 여기에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="7bc1e-593">이전 컨트롤러에서 동일한 네임 스페이스를 사용 하는 경우 컴파일러 오류가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="7bc1e-594">클래스 네임스페이스는 MVC의 라우팅에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="7bc1e-595">처음 두 컨트롤러는 영역의 구성원이며, `area` 경로 값으로 해당 영역 이름을 제공하는 경우에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="7bc1e-596">세 번째 컨트롤러는 어떤 영역의 구성원도 아니며, 라우팅에서 `area`에 대한 값을 제공하지 않을 때에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="7bc1e-597">매칭 측면에서 *값 없음* , 즉 `area` 값이 없는 것은 `area`의 값이 null 또는 빈 문자열인 경우와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-597">In terms of matching *no value* , the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="7bc1e-598">영역 내에서 작업을 실행 하는 경우의 경로 값은 `area` URL 생성에 사용할 라우팅에 대 한 [앰비언트 값](#ambient) 으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="7bc1e-599">즉, 기본적으로 영역은 다음 예제에서 볼 수 있듯이 URL 생성을 위한 *스티커처럼* 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="7bc1e-600">다음 코드에서는에 대 한 URL을 생성 합니다 `/Zebra/Users/AddUser` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="7bc1e-601">작업 정의</span><span class="sxs-lookup"><span data-stu-id="7bc1e-601">Action definition</span></span>

<span data-ttu-id="7bc1e-602">[비 action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) 특성을 사용 하는 경우를 제외 하 고 컨트롤러의 공용 메서드는 동작입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="7bc1e-603">예제 코드</span><span class="sxs-lookup"><span data-stu-id="7bc1e-603">Sample code</span></span>

* [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]
* <span data-ttu-id="7bc1e-604">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7bc1e-604">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7bc1e-605">ASP.NET Core MVC는 라우팅 [미들웨어](xref:fundamentals/middleware/index)를 사용하여 들어오는 요청의 URL을 매칭하고 작업에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-605">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="7bc1e-606">경로는 시작 코드 또는 특성에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-606">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="7bc1e-607">경로는 URL 경로를 작업에 매칭하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-607">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="7bc1e-608">경로는 응답으로 전송되는 URL(링크 용)을 생성하기 위해서도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-608">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="7bc1e-609">작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-609">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7bc1e-610">컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-610">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7bc1e-611">자세한 내용은 [혼합 라우팅](#routing-mixed-ref-label)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-611">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="7bc1e-612">이 항목에서는 MVC와 라우팅 간의 상호 작용 및 일반적인 MVC 앱이 라우팅 기능을 사용하는 방식에 관해서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-612">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="7bc1e-613">고급 라우팅에 대한 자세한 내용은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-613">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="7bc1e-614">라우팅 미들웨어 설정</span><span class="sxs-lookup"><span data-stu-id="7bc1e-614">Setting up Routing Middleware</span></span>

<span data-ttu-id="7bc1e-615">*Configure* 메서드에서 다음과 비슷한 코드를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-615">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7bc1e-616">`UseMvc` 호출 내부에서 `MapRoute`를 사용하여 `default` 경로라고 부르는 단일 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-616">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="7bc1e-617">대부분의 MVC 앱은 `default` 경로와 비슷한 템플릿을 갖는 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-617">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="7bc1e-618">`"{controller=Home}/{action=Index}/{id?}"` 경로 템플릿은 `/Products/Details/5`와 같은 URL 경로를 매칭하고 경로를 토큰화하여 `{ controller = Products, action = Details, id = 5 }` 경로 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-618">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="7bc1e-619">MVC는 `ProductsController`라는 컨트롤러를 찾아 `Details` 작업을 실행하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-619">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="7bc1e-620">이 예제에서 이 작업을 호출할 때 모델 바인딩이 `id = 5` 값을 사용하여 `id` 매개 변수를 `5`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-620">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="7bc1e-621">자세한 내용은 [모델 바인딩](../models/model-binding.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-621">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="7bc1e-622">`default` 경로를 사용하여:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-622">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="7bc1e-623">경로 템플릿의:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-623">The route template:</span></span>

* <span data-ttu-id="7bc1e-624">`{controller=Home}`는 `Home`를 기본 `controller`으로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-624">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="7bc1e-625">`{action=Index}`는 `Index`를 기본 `action`으로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-625">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="7bc1e-626">`{id?}`는 `id`를 선택 사항으로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-626">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="7bc1e-627">기본 및 선택적 경로 매개 변수는 매칭을 위해 URL 경로에 반드시 있어야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-627">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="7bc1e-628">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-628">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="7bc1e-629">`"{controller=Home}/{action=Index}/{id?}"`는 URL 경로 `/`를 매칭할 수 있으며 `{ controller = Home, action = Index }` 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-629">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="7bc1e-630">`controller` 및 `action` 값으로 기본값이 사용되며, `id`는 URL 경로에 해당 세그먼트가 없기 때문에 값을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-630">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="7bc1e-631">MVC는 이러한 경로 값을 사용하여 `HomeController` 및 `Index` 작업을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-631">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="7bc1e-632">이러한 컨트롤러 정의와 경로 템플릿을 사용하면 다음 URL 경로에 대해 `HomeController.Index` 작업이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-632">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="7bc1e-633">편의 메서드인 `UseMvcWithDefaultRoute`를 사용하여:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-633">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="7bc1e-634">다음을 대체할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-634">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7bc1e-635">`UseMvc` 및 `UseMvcWithDefaultRoute`는 미들웨어 파이프라인에 `RouterMiddleware` 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-635">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="7bc1e-636">MVC는 미들웨어와 직접 상호 작용하지 않고 라우팅을 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-636">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="7bc1e-637">MVC는 `MvcRouteHandler`의 인스턴스를 통해 경로에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-637">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="7bc1e-638">`UseMvc` 내부의 코드는 다음과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-638">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="7bc1e-639">`UseMvc`는 아무런 경로도 직접 정의하지 않고 경로 컬렉션에 `attribute` 경로에 대한 자리 표시자만 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-639">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="7bc1e-640">`UseMvc(Action<IRouteBuilder>)` 오버로드를 사용하면 고유의 경로를 추가하고 특성 라우팅도 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-640">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="7bc1e-641">`UseMvc` 및 모든 변형은 특성 경로에 대한 자리 표시자를 추가합니다. 특성 라우팅은 `UseMvc`를 구성하는 방법에 관계없이 항상 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-641">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="7bc1e-642">`UseMvcWithDefaultRoute`는 기본 경로를 정의하고 특성 라우팅을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-642">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="7bc1e-643">[특성 라우팅](#attribute-routing-ref-label) 섹션에는 특성 라우팅에 대한 자세한 내용이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-643">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="7bc1e-644">규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="7bc1e-644">Conventional routing</span></span>

<span data-ttu-id="7bc1e-645">`default` 경로인:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-645">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="7bc1e-646">위의 코드는 기존 라우팅의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-646">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="7bc1e-647">이 스타일은 URL 경로에 대 한 *규칙* 을 설정 하기 때문에 기존 라우팅 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-647">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="7bc1e-648">첫 번째 경로 세그먼트는 컨트롤러 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-648">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="7bc1e-649">두 번째는 작업 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-649">The second maps to the action name.</span></span>
* <span data-ttu-id="7bc1e-650">세 번째 세그먼트는 선택 사항에 사용 됩니다 `id` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-650">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="7bc1e-651">`id` 모델 엔터티에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-651">`id` maps to a model entity.</span></span>

<span data-ttu-id="7bc1e-652">이 `default` 경로를 사용하면 URL 경로 `/Products/List`는 `ProductsController.List` 작업에 매핑되고 `/Blog/Article/17`은 `BlogController.Article`에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-652">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="7bc1e-653">매핑은 **오직** 컨트롤러 및 작업 이름만을 기준으로 하며 네임스페이스, 원본 파일 위치 또는 메서드 매개 변수를 기준으로 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-653">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="7bc1e-654">기본 경로와 함께 규칙 기반 라우팅을 사용하면 정의하는 각 작업마다 새 URL 패턴을 만들지 않고도 신속하게 애플리케이션을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-654">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="7bc1e-655">CRUD 스타일의 작업을 수행하는 애플리케이션의 경우 컨트롤러 전반에서 URL을 일관적으로 유지하면 코드를 단순화하고 UI의 예측 가능성을 높이는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-655">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="7bc1e-656">`id`는 경로 템플릿에서 선택 사항으로 정의됩니다. 즉, URL의 일부로 제공되는 ID 없이도 작업을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-656">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="7bc1e-657">일반적으로 URL에서 `id`가 생략되면 모델 바인딩에 의해 `0`으로 설정되고, 그 결과 데이터베이스에서 `id == 0`과 일치하는 엔터티를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-657">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="7bc1e-658">특성 라우팅을 사용하면 일부 작업에는 필요하고 다른 작업에는 필요하지 않은 ID를 만들기 위해 세밀하게 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-658">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="7bc1e-659">일반적으로 `id` 같은 선택적 매개 변수가 올바른 사용법으로 나타날 가능성이 있는 경우 설명서에 이러한 선택적 매개 변수가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-659">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="7bc1e-660">여러 경로</span><span class="sxs-lookup"><span data-stu-id="7bc1e-660">Multiple routes</span></span>

<span data-ttu-id="7bc1e-661">`MapRoute`에 대한 더 많은 호출을 추가하여 `UseMvc` 내부에서 여러 경로를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-661">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="7bc1e-662">이렇게 하면 여러 규칙을 정의하거나 다음과 같이 특정 작업에만 사용되는 규칙 기반 경로를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-662">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7bc1e-663">여기서 `blog` 경로는 규칙 기반 라우팅 시스템을 사용하지만 특정 작업에만 활용되는 *전용 규칙 기반 경로* 입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-663">The `blog` route here is a *dedicated conventional route* , meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="7bc1e-664">`controller` 및 `action`이 경로 템플릿에 매개 변수로 표시되지 않기 때문에 기본값만 가질 수 있으며, 따라서 이 경로는 항상 `BlogController.Article` 작업에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-664">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="7bc1e-665">경로 컬렉션의 경로는 순서대로 정렬되며 추가된 순서대로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-665">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="7bc1e-666">따라서 이 예제의 `blog` 경로는 `default` 경로보다 먼저 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-666">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="7bc1e-667">*전용 기본 경로* 는 URL 경로의 나머지 부분을 캡처하는 등 **의 모든** 경로 매개 변수를 사용 하는 경우가 많습니다 `{*article}` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-667">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="7bc1e-668">이 경우 경로가 '너무 많은 욕심'을 부리게 됩니다. 즉, 다른 경로와 매칭하려는 URL과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-668">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="7bc1e-669">이 '욕심 많은' 경로를 경로 테이블의 뒷부분에 배치하면 이 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-669">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="7bc1e-670">대체</span><span class="sxs-lookup"><span data-stu-id="7bc1e-670">Fallback</span></span>

<span data-ttu-id="7bc1e-671">요청 처리의 일부로, MVC는 경로 값을 사용하여 애플리케이션의 컨트롤러 및 작업을 찾을 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-671">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="7bc1e-672">경로 값이 작업과 일치하지 않으면 해당 경로는 불일치하는 것으로 간주되고, 그 다음 경로를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-672">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="7bc1e-673">이것을 *대체* 라고 부르며, 규칙 기반 경로가 겹치는 상황을 단순화하는 것이 목적입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-673">This is called *fallback* , and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="7bc1e-674">명확한 작업 구분</span><span class="sxs-lookup"><span data-stu-id="7bc1e-674">Disambiguating actions</span></span>

<span data-ttu-id="7bc1e-675">두 작업이 라우팅을 통해 일치하는 경우 MVC는 작업을 명확히 구분하여 '최적의' 후보를 선택해야 하며, 그렇지 못하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-675">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="7bc1e-676">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="7bc1e-676">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="7bc1e-677">이 컨트롤러는 URL 경로 `/Products/Edit/17` 및 경로 데이터 `{ controller = Products, action = Edit, id = 17 }`과 일치하는 두 작업을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-677">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="7bc1e-678">이는 MVC 컨트롤러의 일반적인 패턴으로, `Edit(int)`는 제품을 편집할 양식을 보여주고 `Edit(int, Product)`는 게시된 양식을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-678">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="7bc1e-679">이것이 가능하려면 MVC가 요청이 HTTP `POST`이면 `Edit(int, Product)`를 선택해야 하고, HTTP 동사가 그 외의 다른 것이면 `Edit(int)`를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-679">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="7bc1e-680">`HttpPostAttribute`(`[HttpPost]`)는 HTTP 동사가 `POST`인 경우에만 작업을 선택하는 것이 가능한 `IActionConstraint`의 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-680">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="7bc1e-681">`IActionConstraint`가 존재하면 `Edit(int, Product)`가 `Edit(int)`보다 '더 정확하게' 일치하므로 `Edit(int, Product)`를 가장 먼저 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-681">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="7bc1e-682">개발자는 특별한 시나리오의 사용자 지정 `IActionConstraint` 구현을 작성하기만 하면 되지만, `HttpPostAttribute` 같은 특성 역할을 이해하는 것이 중요합니다. 다른 HTTP 동사에 대해 비슷한 특성이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-682">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="7bc1e-683">규칙 기반 라우팅에서는 작업이 `show form -> submit form` 워크플로의 일부인 경우 작업에서 동일한 작업을 사용하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-683">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="7bc1e-684">[IActionConstraint 이해](#understanding-iactionconstraint) 섹션을 검토하시면 이 패턴의 편리함을 보다 명확하게 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-684">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="7bc1e-685">여러 경로가 일치하고 MVC가 '최적의' 경로를 찾을 수 없는 경우 MVC는 `AmbiguousActionException`을 던집니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-685">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="7bc1e-686">경로 이름</span><span class="sxs-lookup"><span data-stu-id="7bc1e-686">Route names</span></span>

<span data-ttu-id="7bc1e-687">다음 예제의 `"blog"` 및 `"default"` 문자열은 경로 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-687">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7bc1e-688">경로 이름은 명명된 경로를 URL 생성에 사용할 수 있도록 경로에 논리 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-688">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="7bc1e-689">이렇게 하면 경로 순서 지정으로 인해 URL 생성이 복잡해질 수 있는 상황에서 URL 생성 방법이 매우 간단해집니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-689">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="7bc1e-690">경로 이름은 애플리케이션 전체에서 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-690">Route names must be unique application-wide.</span></span>

<span data-ttu-id="7bc1e-691">경로 이름은 URL 일치 또는 요청 처리에 영향을 미치지 않으며 URL 생성에서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-691">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="7bc1e-692">[라우팅](xref:fundamentals/routing)은 MVC 관련 도우미에서 URL 생성을 포함하여 URL 생성에 대한 보다 자세한 정보를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-692">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="7bc1e-693">특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="7bc1e-693">Attribute routing</span></span>

<span data-ttu-id="7bc1e-694">특성 라우팅은 특성 모음을 사용하여 작업을 경로 템플릿에 직접 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-694">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="7bc1e-695">다음 예제에서는 `Configure` 메서드에서 `app.UseMvc();`가 사용되어 경로가 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-695">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="7bc1e-696">`HomeController`는 기본 경로 `{controller=Home}/{action=Index}/{id?}`의 일치 항목과 비슷한 URL 집합과 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-696">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="7bc1e-697">`HomeController.Index()` 작업은 URL 경로 `/`, `/Home` 또는 `/Home/Index`에 대해서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-697">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="7bc1e-698">이 예제에서는 특성 라우팅과 규칙 기반 라우팅 간의 주요 프로그래밍 차이점을 강조합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-698">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="7bc1e-699">특성 라우팅은 경로를 지정하기 위해 더 많은 입력이 필요하고, 규칙 기반 기본 경로는 경로를 보다 간결하게 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-699">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="7bc1e-700">그러나 특성 라우팅을 사용하면 각 작업에 적용되는 경로 템플릿을 정확하게 제어할 수 있습니다(또 그래야만 합니다).</span><span class="sxs-lookup"><span data-stu-id="7bc1e-700">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="7bc1e-701">특성 라우팅을 사용하면 컨트롤러 이름 및 작업 이름은 작업 선택에 있어서 아무 역할도 하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-701">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="7bc1e-702">이 예제는 이전 예제와 동일한 URL을 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-702">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="7bc1e-703">위의 경로 템플릿은 `action`, `area` 및 `controller`에 대한 경로 매개 변수를 정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-703">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="7bc1e-704">사실, 이러한 경로 매개 변수는 특성 경로에 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-704">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="7bc1e-705">경로 템플릿은 이미 작업에 연결되어 있기 때문에 URL에서 작업 이름을 구문 분석할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-705">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="7bc1e-706">Http[Verb] 특성을 사용한 특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="7bc1e-706">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="7bc1e-707">특성 라우팅은 `HttpPostAttribute` 같은 `Http[Verb]`를 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-707">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="7bc1e-708">이러한 특성은 모두 경로 템플릿을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-708">All of these attributes can accept a route template.</span></span> <span data-ttu-id="7bc1e-709">이 예제는 동일한 경로 템플릿과 일치하는 두 가지 작업을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-709">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="7bc1e-710">`/products` 같은 URL 경로의 경우 HTTP 동사가 `GET`이면 `ProductsApi.ListProducts` 작업이 실행되고 HTTP 동사가 `POST`이면 `ProductsApi.CreateProduct`가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-710">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="7bc1e-711">특성 라우팅은 먼저 URL을 경로 특성에 정의된 경로 템플릿 집합과 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-711">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="7bc1e-712">경로 템플릿이 일치하면 `IActionConstraint` 제약 조건을 적용하여 실행 가능한 작업을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-712">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="7bc1e-713">REST API를 빌드할 때 작업 메서드에 `[Route(...)]`를 사용하려는 경우는 거의 없습니다. 작업이 모든 HTTP 메서드를 받기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-713">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="7bc1e-714">보다 구체적인 `Http*Verb*Attributes`를 사용하여 API에서 지원하는 항목을 정확하게 지정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-714">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="7bc1e-715">REST API의 클라이언트는 특정 논리 작업에 어떤 경로 및 HTTP 동사가 매핑되는지 알아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-715">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="7bc1e-716">특성 경로는 특정 작업에 적용되므로 경로 템플릿 정의의 일환으로 필요한 매개 변수를 간단하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-716">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="7bc1e-717">이 예제에서 `id`는 URL 경로에 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-717">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7bc1e-718">`ProductsApi.GetProduct(int)` 작업은 `/products/3` 같은 URL 경로에 대해 실행되지만 `/products` 같은 URL 경로에 대해서는 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-718">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="7bc1e-719">경로 템플릿 및 관련 옵션에 대한 전체 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-719">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="7bc1e-720">경로 이름</span><span class="sxs-lookup"><span data-stu-id="7bc1e-720">Route Name</span></span>

<span data-ttu-id="7bc1e-721">다음 코드는 `Products_List`의 *경로 이름* 을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-721">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7bc1e-722">경로 이름을 사용하여 특정 경로 기반의 URL을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-722">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="7bc1e-723">경로 이름은 라우팅의 URL 매칭 동작에 영향을 미치지 않으며 URL 생성에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-723">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="7bc1e-724">경로 이름은 애플리케이션 전체에서 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-724">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="7bc1e-725">`id` 매개 변수를 선택 사항(`{id?}`)으로 정의하는 규칙 기반 *기본 경로* 와는 대조적입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-725">Contrast this with the conventional *default route* , which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="7bc1e-726">API를 정확하게 지정하는 이 기능은 `/products`와 `/products/5`를 서로 다른 작업에 디스패치할 수 있는 등의 장점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-726">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="7bc1e-727">경로 결합</span><span class="sxs-lookup"><span data-stu-id="7bc1e-727">Combining routes</span></span>

<span data-ttu-id="7bc1e-728">특성 라우팅의 반복 횟수를 줄이기 위해 컨트롤러의 경로 특성은 개별 작업의 경로 특성과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-728">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="7bc1e-729">컨트롤러에 정의된 경로 템플릿은 작업의 경로 템플릿에 앞에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-729">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="7bc1e-730">경로 특성을 컨트롤러에 배치하면 컨트롤러의 **모든** 작업이 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-730">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7bc1e-731">이 예제에서 URL 경로 `/products`는 `ProductsApi.ListProducts`와 매칭할 수 있고, URL 경로 `/products/5`는 `ProductsApi.GetProduct(int)`와 매칭할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-731">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="7bc1e-732">두 작업은 모두 `HttpGetAttribute`로 표시되기 때문에 HTTP `GET`만 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-732">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="7bc1e-733">작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-733">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="7bc1e-734">이 예제는 *기본 경로* 와 비슷한 URL 경로 집합을 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-734">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="7bc1e-735">특성 경로 순서 지정</span><span class="sxs-lookup"><span data-stu-id="7bc1e-735">Ordering attribute routes</span></span>

<span data-ttu-id="7bc1e-736">정의 된 순서로 실행 되는 기존 경로와 달리 특성 라우팅은 트리를 작성 하 고 모든 경로를 동시에 일치 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-736">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="7bc1e-737">이 동작은 경로 전체가 이상적인 순서로 정렬된 것처럼 수행됩니다. 가장 구체적인 경로가 일반적인 경로보다 먼저 실행될 가능성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-737">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="7bc1e-738">예를 들어 `blog/search/{topic}` 같은 경로는 `blog/{*article}` 같은 경로보다 구체적입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-738">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="7bc1e-739">논리적으로 말해서, 기본적으로 `blog/search/{topic}` 경로가 가장 먼저 '실행'됩니다. 유일하게 합리적인 순서이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-739">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="7bc1e-740">규칙 기반 라우팅을 사용하면 개발자가 원하는 순서대로 경로를 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-740">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="7bc1e-741">특성 경로는 프레임워크가 제공하는 모든 경로 특성의 `Order` 속성을 사용하여 순서를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-741">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="7bc1e-742">경로는 `Order` 속성의 오름차순 정렬에 따라 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-742">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="7bc1e-743">기본 순서는 `0`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-743">The default order is `0`.</span></span> <span data-ttu-id="7bc1e-744">`Order = -1`을 사용한 경로 설정은 순서를 설정하지 않는 경로보다 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-744">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="7bc1e-745">`Order = 1`을 사용한 경로 설정은 기본 경로 순서보다 늦게 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-745">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="7bc1e-746">`Order`를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-746">Avoid depending on `Order`.</span></span> <span data-ttu-id="7bc1e-747">URL 공간에 올바른 라우팅을 위한 명시적 순서 값이 필요한 경우 클라이언트에서도 혼란이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-747">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="7bc1e-748">일반적으로 특성 라우팅은 URL이 일치하는 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-748">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="7bc1e-749">URL 생성에 사용되는 기본 순서가 작동하지 않는 경우 일반적으로 경로 이름을 재정의로 사용하는 것이 `Order` 속성을 적용하는 것보다 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-749">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="7bc1e-750">Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-750">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="7bc1e-751">페이지 항목의 경로 순서에 대 한 정보 Razor 는 [ Razor 페이지 경로 및 앱 규칙: 경로 순서](xref:razor-pages/razor-pages-conventions#route-order)에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-751">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="7bc1e-752">경로 템플릿에서 토큰 바꾸기([컨트롤러] [작업] [지역])</span><span class="sxs-lookup"><span data-stu-id="7bc1e-752">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="7bc1e-753">편의를 위해 특성 경로는 토큰을 대괄호 (,)로 묶어 *토큰 바꾸기를* 지원 `[` `]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-753">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="7bc1e-754">`[action]`, `[area]` 및 `[controller]` 토큰은 경로가 정의된 작업의 작업 이름, 영역 이름 및 컨트롤러 이름으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-754">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="7bc1e-755">다음 예의 작업은 주석에 설명된 대로 URL 경로를 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-755">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="7bc1e-756">토큰 교체는 특성 경로 빌드 과정의 마지막 단계로 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-756">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="7bc1e-757">위의 예제는 다음 코드와 동일하게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-757">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="7bc1e-758">특성 경로를 상속과 결합할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-758">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="7bc1e-759">특히 토큰 교체와 결합하면 더욱 강력합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-759">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="7bc1e-760">토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-760">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="7bc1e-761">`[Route("[controller]/[action]", Name="[controller]_[action]")]`는 각 작업의 고유한 경로 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-761">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="7bc1e-762">리터럴 토큰 교체 구분 기호 `[` 또는 `]`와 매칭하려면 문자(`[[` 또는 `]]`)를 반복하여 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-762">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="7bc1e-763">매개 변수 변환기를 사용하여 토큰 교체 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="7bc1e-763">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="7bc1e-764">매개 변수 변환기를 사용하여 토큰 교체를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-764">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="7bc1e-765">매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하며 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-765">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="7bc1e-766">예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-766">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="7bc1e-767">`RouteTokenTransformerConvention`은 다음과 같은 응용 프로그램 모델 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-767">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="7bc1e-768">애플리케이션의 모든 특성 경로에 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-768">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="7bc1e-769">대체되는 특성 경로 토큰 값을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-769">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="7bc1e-770">`RouteTokenTransformerConvention`은 `ConfigureServices`에 옵션으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-770">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="7bc1e-771">여러 경로</span><span class="sxs-lookup"><span data-stu-id="7bc1e-771">Multiple Routes</span></span>

<span data-ttu-id="7bc1e-772">특성 경로는 동일한 작업에 도달하는 여러 경로를 정의하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-772">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="7bc1e-773">가장 일반적인 사용 방법은 다음 예제와 같이 *기본 규칙 기반 경로* 의 동작을 모방하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-773">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="7bc1e-774">컨트롤러에 여러 경로 특성을 배치하면 각 경로 특성이 작업 메서드의 각 경로 특성과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-774">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="7bc1e-775">여러 경로 특성(`IActionConstraint`를 구현하는)이 작업에 배치되면 각 작업 제약 조건은 제약 조건을 정의한 특성의 경로 템플릿과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-775">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="7bc1e-776">작업에 여러 경로를 사용하는 것이 좋아 보일 수 있지만, 애플리케이션의 URL 공간을 간단하고 잘 정의된 상태로 유지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-776">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="7bc1e-777">기존 클라이언트를 지원하려는 경우처럼 꼭 필요한 경우에만 작업에 여러 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-777">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="7bc1e-778">특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정</span><span class="sxs-lookup"><span data-stu-id="7bc1e-778">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="7bc1e-779">특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정할 수 있는 규칙 기반 경로와 동일한 인라인 구문을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-779">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="7bc1e-780">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-780">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="7bc1e-781">`IRouteTemplateProvider`를 사용하는 사용자 지정 경로 특성</span><span class="sxs-lookup"><span data-stu-id="7bc1e-781">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="7bc1e-782">프레임워크에서 제공하는 모든 경로 특성(`[Route(...)]`, `[HttpGet(...)]` 등)은 `IRouteTemplateProvider` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-782">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="7bc1e-783">앱이 시작되면 MVC는 컨트롤러 클래스 및 작업 메서드에서 특성을 찾아 `IRouteTemplateProvider`를 구현하는 특성을 사용하여 초기 경로 집합을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-783">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="7bc1e-784">개발자 고유의 경로 특성을 정의하는 `IRouteTemplateProvider`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-784">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="7bc1e-785">각각의 `IRouteTemplateProvider`를 사용하여 사용자 지정 경로 템플릿, 순서 및 이름으로 단일 경로를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-785">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="7bc1e-786">위의 예제의 특성은 `[MyApiController]`가 적용되면 자동으로 `Template`을 `"api/[controller]"`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-786">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="7bc1e-787">응용 프로그램 모델을 사용하여 특성 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="7bc1e-787">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="7bc1e-788">*응용 프로그램 모델* 은 MVC에 의해서 작업을 라우팅하고 실행하는 데 사용되는 모든 메타데이터를 이용하여 시작 시 만들어지는 개체 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-788">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="7bc1e-789">*응용 프로그램 모델* 은 `IRouteTemplateProvider`를 통해서 경로 특성으로부터 수집한 모든 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-789">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="7bc1e-790">개발자는 시작 시에 응용 프로그램 모델을 수정하는 *규칙* 을 작성하여 라우팅 동작을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-790">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="7bc1e-791">이 섹션에서는 애플리케이션 모델을 사용하여 라우팅을 사용자 지정하는 간단한 예를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-791">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="7bc1e-792">혼합 라우팅: 특성 라우팅 및 규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="7bc1e-792">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="7bc1e-793">MVC 애플리케이션은 규칙 기반 라우팅과 특성 라우팅을 혼합해서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-793">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="7bc1e-794">일반적으로 브라우저의 HTML 페이지를 처리하는 컨트롤러에는 규칙 기반 경로를 사용하고 REST API를 제공하는 컨트롤러에는 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-794">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="7bc1e-795">작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-795">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7bc1e-796">컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-796">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7bc1e-797">특성 경로를 정의하는 동작은 규칙 기반 경로를 통해 도달할 수 없으며 그 반대도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-797">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="7bc1e-798">컨트롤러의 **모든** 경로 특성은 컨트롤러에 있는 모든 작업에서 특성 라우팅을 사용하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-798">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="7bc1e-799">두 라우팅 시스템 간의 차이는 URL이 경로 템플릿과 일치한 후 적용되는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-799">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="7bc1e-800">규칙 기반 라우팅에서는 일치 항목의 경로 값을 사용하여 모든 규칙 기반 라우팅된 작업의 조회 테이블에서 작업 및 컨트롤러를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-800">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="7bc1e-801">특성 라우팅에서 각 템플릿은 이미 작업과 연결되어 있으며, 더 이상의 조회가 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-801">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="7bc1e-802">복잡한 세그먼트</span><span class="sxs-lookup"><span data-stu-id="7bc1e-802">Complex segments</span></span>

<span data-ttu-id="7bc1e-803">복잡한 세그먼트(예: `[Route("/dog{token}cat")]`)는 non-greedy 방식으로 오른쪽에서 왼쪽으로 리터럴을 매칭하여 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-803">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="7bc1e-804">자세한 내용은 [소스 코드](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-804">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="7bc1e-805">자세한 내용은 [이 문제](https://github.com/dotnet/AspNetCore.Docs/issues/8197)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-805">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="7bc1e-806">URL 생성</span><span class="sxs-lookup"><span data-stu-id="7bc1e-806">URL Generation</span></span>

<span data-ttu-id="7bc1e-807">MVC 애플리케이션은 라우팅의 URL 생성 기능을 사용하여 작업에 대한 URL 링크를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-807">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="7bc1e-808">URL을 생성하면 하드 코딩된 URL이 제거되어 코드를 더욱 견고하게 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-808">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="7bc1e-809">이 섹션에서는 MVC에서 제공하는 URL 생성 기능을 중심으로 기본적인 URL 생성 원리에 대해서만 다룰 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-809">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="7bc1e-810">URL 생성에 대한 자세한 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-810">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="7bc1e-811">`IUrlHelper` 인터페이스는 URL 생성을 위한 MVC와 라우팅 간의 기본 인프라입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-811">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="7bc1e-812">컨트롤러, 보기 및 보기 구성 요소의 `Url` 속성을 통해서 사용 가능한 `IUrlHelper`의 인스턴스를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-812">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="7bc1e-813">이 예제에서 `IUrlHelper` 인터페이스는 `Controller.Url` 속성을 통해 다른 작업에 대한 URL을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-813">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="7bc1e-814">응용 프로그램이 기본 규칙 기반 경로를 사용할 경우 `url` 변수의 값은 URL 경로 문자열 `/UrlGeneration/Destination`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-814">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="7bc1e-815">이 URL 경로는 현재 요청의 경로 값(앰비언트 값)을 `Url.Action`에 전달된 값과 결합하고 그 값을 경로 템플릿에 대체하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-815">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="7bc1e-816">경로 템플릿에 있는 각 경로 매개 변수의 값은 이름을 값 및 앰비언트 값과 매칭한 값으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-816">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="7bc1e-817">값이 없는 경로 매개 변수는 기본값이 있는 경우 기본값을 사용할 수 있고, 값이 선택 사항인 경우 건너뛰어도 됩니다(이 예제의 `id`처럼).</span><span class="sxs-lookup"><span data-stu-id="7bc1e-817">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="7bc1e-818">필요한 경로 매개 변수에 해당 값이 없는 경우 URL 생성에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-818">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="7bc1e-819">경로에 대한 URL 생성이 실패하면 모든 경로를 시도하거나 일치 항목을 찾을 때까지 그 다음 경로를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-819">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="7bc1e-820">위의 `Url.Action`예제는 규칙 기반 라우팅을 가정합니다. 개념은 다르지만 URL 생성 원리는 특성 라우팅과 비슷하게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-820">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="7bc1e-821">규칙 기반 라우팅에서 경로 값은 템플릿을 확장하는 데 사용되고, 일반적으로`controller` 및 `action`의 경로 값은 해당 템플릿에 표시됩니다. 라우팅에서 매핑된 URL이 *규칙* 을 따르기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-821">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="7bc1e-822">특성 라우팅에서는 `controller` 및 `action`의 경로 값이 템플릿에 표시되지 않는 대신, 사용할 템플릿을 조회하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-822">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="7bc1e-823">이 예제는 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-823">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="7bc1e-824">MVC는 모든 특성 라우팅 작업의 조회 테이블을 작성하고 `controller` 및 `action` 값을 매칭하여 URL 생성에 사용할 경로 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-824">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="7bc1e-825">위의 예제에서는 `custom/url/to/destination`이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-825">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="7bc1e-826">작업 이름으로 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7bc1e-826">Generating URLs by action name</span></span>

<span data-ttu-id="7bc1e-827">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="7bc1e-827">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="7bc1e-828">`Action`) 및 모든 관련 오버로드는 개발자가 컨트롤러 이름과 작업 이름을 지정하여 연결 대상을 지정하려 한다는 생각을 바탕으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-828">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="7bc1e-829">`Url.Action`을 사용하면 `controller` 및 `action`의 현재 경로 값이 자동으로 지정됩니다. `controller` 및 `action`의 값은 모두 *앰비언트 값* **및** *값* 의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-829">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="7bc1e-830">`Url.Action` 메서드는 항상 `action` 및 `controller`의 현재 값을 사용하며 현재 작업에 라우팅하는 URL 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-830">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="7bc1e-831">라우팅은 앰비언트 값의 값을 사용하여 개발자가 URL을 생성할 때 제공하지 않은 정보를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-831">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="7bc1e-832">`{a}/{b}/{c}/{d}` 같은 경로와 `{ a = Alice, b = Bob, c = Carol, d = David }` 앰비언트 값을 사용하면 모든 경로 매개 변수가 값을 갖기 때문에 추가 값이 없어도 라우팅에서 URL을 생성하기에 충분한 정보가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-832">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="7bc1e-833">`{ d = Donovan }` 값을 추가하면 `{ d = David }` 값이 무시되고 `Alice/Bob/Carol/Donovan` URL 경로가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-833">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="7bc1e-834">URL 경로는 계층적입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-834">URL paths are hierarchical.</span></span> <span data-ttu-id="7bc1e-835">위의 예에서 `{ c = Cheryl }` 값을 추가하면 두 `{ c = Carol, d = David }` 값이 모두 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-835">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="7bc1e-836">이 경우 `d`의 값이 없기 때문에 URL 생성이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-836">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="7bc1e-837">원하는 `c` 및 `d` 값을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-837">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="7bc1e-838">기본 경로(`{controller}/{action}/{id?}`)를 사용해도 이 문제가 발생할 가능성은 있지만, `Url.Action`이 항상 `controller` 및 `action` 값을 명시적으로 지정하기 때문에 실제로 이 동작이 발생하는 경우는 극히 드뭅니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-838">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="7bc1e-839">`Url.Action` 의 긴 오버로드에서는 `controller` 및 `action` 이외의 경로 매개 변수의 값을 제공하기 위한 추가 *경로 값* 개체가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-839">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="7bc1e-840">`Url.Action("Buy", "Products", new { id = 17 })`처럼 `id`와 함께 사용되는 것을 흔히 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-840">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="7bc1e-841">관례상 *경로 값* 개체는 대부분 무명 형식의 개체지만, `IDictionary<>` 또는 *오래된 일반 .NET 개체* 일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-841">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="7bc1e-842">경로 매개 변수와 일치하지 않는 추가 경로 값들은 쿼리 문자열에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-842">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="7bc1e-843">절대 URL을 만들려면 `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`을 받는 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-843">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="7bc1e-844">경로로 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7bc1e-844">Generating URLs by route</span></span>

<span data-ttu-id="7bc1e-845">위의 코드에서는 컨트롤러 및 작업 이름을 전달하여 URL을 생성하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-845">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="7bc1e-846">`IUrlHelper`는 `Url.RouteUrl` 메서드 모음도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-846">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="7bc1e-847">이 메서드는 `Url.Action`과 비슷하지만, `action` 및 `controller`의 현재 값을 경로 값에 복사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-847">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="7bc1e-848">가장 일반적인 사용법은 URL을 생성할 경로를 지정하기 위한 경로 이름을 지정하는 것으로, 일반적으로 컨트롤러 또는 작업 이름을 지정하지 *않습니다*.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-848">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="7bc1e-849">HTML에서 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7bc1e-849">Generating URLs in HTML</span></span>

<span data-ttu-id="7bc1e-850">`IHtmlHelper`는 각각 `<form>` 및 `<a>` 요소를 생성하는 `HtmlHelper` 메서드인 `Html.BeginForm` 및 `Html.ActionLink`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-850">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="7bc1e-851">이러한 메서드는 `Url.Action` 메서드를 사용하여 URL을 생성하며 비슷한 인수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-851">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="7bc1e-852">`HtmlHelper`에 대한 `Url.RouteUrl` 보조 도구는 `Html.BeginRouteForm` 및 `Html.RouteLink`이며 서로 기능이 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-852">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="7bc1e-853">TagHelper는 `form` TagHelper 및 `<a>` TagHelper를 통해 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-853">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="7bc1e-854">둘 다 구현에 `IUrlHelper`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-854">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="7bc1e-855">자세한 내용은 [양식 작업](../views/working-with-forms.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-855">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="7bc1e-856">보기 내에서 `IUrlHelper`는 `Url` 속성을 통해서 위에서 다루지 않은 임시 URL을 생성하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-856">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="7bc1e-857">작업 결과에 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7bc1e-857">Generating URLS in Action Results</span></span>

<span data-ttu-id="7bc1e-858">위의 예제에서는 컨트롤러에서 `IUrlHelper`를 사용하는 방법을 보여주었으며, 컨트롤러에서 가장 일반적인 사용법은 URL을 작업 결과의 일부로 생성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-858">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="7bc1e-859">`ControllerBase` 및 `Controller` 기본 클래스는 다른 작업을 참조하는 작업 결과에 대한 편의 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-859">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="7bc1e-860">한 가지 일반적인 사용법은 사용자 입력을 수락한 후 리디렉션하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-860">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="7bc1e-861">작업 결과 팩터리 메서드는 `IUrlHelper`의 메서드와 비슷한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-861">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="7bc1e-862">전용 규칙 기반 경로의 특별한 사례</span><span class="sxs-lookup"><span data-stu-id="7bc1e-862">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="7bc1e-863">규칙 기반 라우팅은 *전용 규칙 기반 경로* 라고 하는 특별한 경로 정의를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-863">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="7bc1e-864">아래 예제에서 `blog`라는 이름의 경로는 전용 규칙 기반 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-864">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7bc1e-865">이러한 경로 정의를 사용하면 `Url.Action("Index", "Home")`은 `default` 경로를 사용하여 URL 경로 `/`를 생성합니다. 그런데 그 이유는 무엇일까요?</span><span class="sxs-lookup"><span data-stu-id="7bc1e-865">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="7bc1e-866">`{ controller = Home, action = Index }` 경로 값이면 `blog`를 사용하여 URL을 생성하기에 충분하며, 그 결과는 `/blog?action=Index&controller=Home`가 될 것이라고 추측할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-866">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="7bc1e-867">전용 규칙 기반 경로는 URL 생성과 관련하여 경로의 "지나친 욕심"을 방지하는 해당 경로 매개 변수가 없는 기본 값의 특별한 동작을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-867">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="7bc1e-868">이 예제에서 기본값은 `{ controller = Blog, action = Article }`이고, `controller` 및 `action`이 경로 매개 변수로 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-868">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="7bc1e-869">라우팅이 URL 생성을 수행할 때 입력한 값이 기본값과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-869">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="7bc1e-870">`{ controller = Home, action = Index }` 값이 `{ controller = Blog, action = Article }`과 일치하지 않기 때문에 `blog`를 사용한 URL 생성은 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-870">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="7bc1e-871">그 후 라우팅이 `default`로 대체되고, 이것은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-871">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="7bc1e-872">Areas</span><span class="sxs-lookup"><span data-stu-id="7bc1e-872">Areas</span></span>

<span data-ttu-id="7bc1e-873">[영역](areas.md)은 관련 기능을 별도의 라우팅-네임스페이스(컨트롤러 작업용) 및 폴더 구조(보기용)로 그룹화하는 데 사용되는 MVC 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-873">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="7bc1e-874">영역을 사용하면 컨트롤러의 *영역* 이 서로 다른 한, 응용 프로그램 하나에서 이름이 같은 컨트롤러를 여러 개 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-874">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="7bc1e-875">영역을 사용하면 또 다른 경로 매개 변수 `area`를 `controller` 및 `action`에 추가하여 라우팅을 위한 계층 구조를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-875">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="7bc1e-876">이 섹션에서는 라우팅이 영역과 상호 작용하는 방법을 설명합니다. 보기에 영역을 사용하는 자세한 방법은 [영역](areas.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-876">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="7bc1e-877">다음 예제는 기본 규칙 기반 경로 및 `Blog`라는 이름의 영역에 대한 *영역 경로* 를 사용하도록 MVC를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-877">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="7bc1e-878">`/Manage/Users/AddUser` 같은 URL 경로를 매칭할 때, 첫 번째 경로는 `{ area = Blog, controller = Users, action = AddUser }` 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-878">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="7bc1e-879">`area` 경로 값은 `area`의 기본값을 사용하여 생성되며, 사실 `MapAreaRoute`를 통해 생성되는 경로는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-879">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="7bc1e-880">`MapAreaRoute`는 제공된 영역 이름(여기에서는 `Blog`)을 사용하는 `area`에 대해 기본값 및 제약 조건을 모두 사용하여 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-880">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="7bc1e-881">기본값은 경로가 항상 `{ area = Blog, ... }`를 생성함을 보장하고, 제약 조건은 URL 생성을 위해 `{ area = Blog, ... }` 값을 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-881">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="7bc1e-882">규칙 기반 라우팅은 순서에 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-882">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7bc1e-883">일반적으로 영역이 있는 경로는 영역이 없는 경로에 비해 구체적이기 때문에 경로 테이블의 앞부분에 배치되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-883">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="7bc1e-884">위의 예제를 사용하면 경로 값이 다음 작업과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-884">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="7bc1e-885">`AreaAttribute`는 컨트롤러가 영역의 일부임을 나타내며, 이때 이 컨트롤러가 `Blog` 영역에 있다고 표현합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-885">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="7bc1e-886">`[Area]` 특성이 없는 컨트롤러는 어떠한 영역의 구성원도 아니며, 라우팅에서 `area` 경로 값을 제공해도 매칭되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-886">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="7bc1e-887">다음 예제에서는 나열된 첫 번째 컨트롤러만 `{ area = Blog, controller = Users, action = AddUser }` 경로 값과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-887">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="7bc1e-888">각 컨트롤러의 네임스페이스는 완전성을 위해서 사용되었습니다. 네임스페이스가 없으면 컨트롤러에서 이름 충돌이 발생하고 컴파일러 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-888">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="7bc1e-889">클래스 네임스페이스는 MVC의 라우팅에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-889">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="7bc1e-890">처음 두 컨트롤러는 영역의 구성원이며, `area` 경로 값으로 해당 영역 이름을 제공하는 경우에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-890">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="7bc1e-891">세 번째 컨트롤러는 어떤 영역의 구성원도 아니며, 라우팅에서 `area`에 대한 값을 제공하지 않을 때에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-891">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="7bc1e-892">매칭 측면에서 *값 없음* , 즉 `area` 값이 없는 것은 `area`의 값이 null 또는 빈 문자열인 경우와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-892">In terms of matching *no value* , the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="7bc1e-893">영역 내에서 작업을 실행하면 `area`의 경로 값이 라우팅에서 URL을 생성하기 위해 사용되는 *앰비언트 값* 으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-893">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="7bc1e-894">즉, 기본적으로 영역은 다음 예제에서 볼 수 있듯이 URL 생성을 위한 *스티커처럼* 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-894">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="7bc1e-895">IActionConstraint 이해</span><span class="sxs-lookup"><span data-stu-id="7bc1e-895">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="7bc1e-896">이 섹션에서는 프레임워크의 내부 구조 및 MVC가 실행할 작업을 선택하는 원리를 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-896">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="7bc1e-897">일반적인 응용 프로그램에는 사용자 지정 `IActionConstraint`가 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-897">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="7bc1e-898">이 인터페이스에 익숙하지 않은 분들도 이미 `IActionConstraint`를 사용해 보셨을 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-898">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="7bc1e-899">`[HttpGet]` 특성 그리고 그와 비슷한 `[Http-VERB]` 특성들은 `IActionConstraint`를 구현하여 작업 메서드 실행을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-899">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="7bc1e-900">기본 규칙 기반 경로를 사용한다고 가정할 때, URL 경로 `/Products/Edit`는 여기에 보이는 두 작업 **모두** 와 매칭하는 `{ controller = Products, action = Edit }` 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-900">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="7bc1e-901">`IActionConstraint` 용어에서는 두 작업이 경로 데이터와 매칭되므로 두 작업이 후보로 간주된다고 말할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-901">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="7bc1e-902">`HttpGetAttribute`가 실행되면 *Edit()* 는 *GET* 에 대해서만 일치하고 다른 HTTP 동사에 대해서는 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-902">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="7bc1e-903">`Edit(...)` 작업은 정의된 제약 조건이 없으므로 모든 HTTP 동사와 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-903">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="7bc1e-904">따라서 `POST`를 고려해본다면 `Edit(...)`만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-904">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="7bc1e-905">하지만 `GET`의 경우에는 여전히 두 작업 모두 매칭될 수 있습니다. 그러나`IActionConstraint`가 적용된 작업이 적용되지 않은 작업보다 항상 *더 적합한* 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-905">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="7bc1e-906">따라서 `Edit()`에는 `[HttpGet]`가 있으므로 보다 구체적인 것으로 간주되며, 두 작업이 모두 매칭될 수 있는 경우에도 이 작업이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-906">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="7bc1e-907">개념적으로 `IActionConstraint`는 *오버로딩* 의 한 가지 형태이지만, 같은 이름의 메서드를 오버로딩하는 대신, 동일한 URL을 매칭하는 작업 간에 오버로딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-907">Conceptually, `IActionConstraint` is a form of *overloading* , but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="7bc1e-908">특성 라우팅에서도 `IActionConstraint`를 사용하며, 다른 두 컨트롤러의 작업이 모두 후보로 간주될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-908">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="7bc1e-909">IActionConstraint 구현</span><span class="sxs-lookup"><span data-stu-id="7bc1e-909">Implementing IActionConstraint</span></span>

<span data-ttu-id="7bc1e-910">`IActionConstraint`를 구현하는 가장 간단한 방법은 `System.Attribute`에서 파생된 클래스를 만들어서 작업 및 컨트롤러에 배치하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-910">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="7bc1e-911">MVC는 특성으로 적용된 `IActionConstraint`를 자동으로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-911">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="7bc1e-912">애플리케이션 모델을 사용하여 제약 조건을 적용할 수 있으며, 제약 조건이 적용되는 방식을 개발자가 메타프로그래밍할 수 있는 가장 유연한 접근 방식일 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-912">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="7bc1e-913">다음 예에서는 제약 조건이 경로 데이터의 *국가 코드* 를 기반으로 하는 작업을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-913">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="7bc1e-914">[GitHub의 전체 예제](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="7bc1e-914">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="7bc1e-915">개발자는 `Accept` 메서드를 구현하고 제약 조건이 실행되는 '순서'를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-915">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="7bc1e-916">이 예제에서 `Accept` 메서드는 `country` 경로 값이 일치하면 작업이 일치함을 나타내기 위해 `true`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-916">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="7bc1e-917">비-특성 작업으로 대체할 수 있다는 점에서 `RouteValueAttribute`와는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-917">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="7bc1e-918">이 예제는 `en-US` 작업을 정의하면 `fr-FR` 같은 국가 코드는 `[CountrySpecific(...)]`이 적용되지 않은 보다 일반적인 컨트롤러로 대체되는 것을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-918">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="7bc1e-919">`Order` 속성은 제약 조건이 소속되는 *단계* 를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-919">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="7bc1e-920">작업 제약 조건은 `Order`에 따라 그룹으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-920">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="7bc1e-921">예를 들어 프레임워크에서 제공하는 모든 HTTP 메서드 특성은 같은 단계에서 실행될 수 있도록 동일한 `Order` 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-921">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="7bc1e-922">원하는 정책을 구현하는 데 필요한 만큼 단계를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-922">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="7bc1e-923">`Order`에 대한 값을 결정하려면 HTTP 메서드보다 제약 조건이 먼저 적용되어야 하는지 생각해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-923">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="7bc1e-924">숫자가 낮을수록 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bc1e-924">Lower numbers run first.</span></span>

::: moniker-end
